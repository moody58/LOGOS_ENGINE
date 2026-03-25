
------------------------------------------------
LOGOS_RUNTIME_SNAPSHOT_v1.1
------------------------------------------------

STATO: REALE (VALIDATO + ESTESO)

------------------------------------------------
1. ARCHITETTURA RUNTIME REALE
------------------------------------------------

INPUT (Retool UI)
→ Query (Resource query)
→ Supabase REST API
→ Database (events)

Sistema event-driven append-only.

------------------------------------------------
2. API SUPABASE
------------------------------------------------

BASE URL

https://utvwefciuxtwoqvvcwel.supabase.co/rest/v1

------------------------------------------------

ENDPOINT UTILIZZATO

POST /events

------------------------------------------------

HEADERS

apikey: [anon_key]

Authorization: Bearer [anon_key]

Content-Type: application/json

------------------------------------------------

RESPONSE ATTESA

✔ Status: 201 Created  
✔ Body: vuoto oppure minimale  

------------------------------------------------

COMPORTAMENTO ERRORE

✔ 4xx → errore richiesta (payload / permessi)  
✔ 5xx → errore server  

Esempi reali osservati:

- invalid path → URL errato  
- access forbidden → API key non corretta  
- empty insert → body non letto  

------------------------------------------------
3. QUERY RETOOL
------------------------------------------------

NOME

insert_event

------------------------------------------------

TIPO

Resource Query (REST API)

------------------------------------------------

METODO

POST

------------------------------------------------

URL (PATH)

events

------------------------------------------------

BODY (RAW JSON)

{
  "type": {{ type_select.value }},
  "event_date": {{ event_date_input.value }},
  "amount": {{ amount_input.value }},
  "notes": {{ notes_input.value }}
}

------------------------------------------------

NOTE

✔ body dinamico  
✔ nessuna trasformazione  
✔ dipendenza diretta da componenti UI  

------------------------------------------------
4. NAMING COMPONENTI (STANDARD)
------------------------------------------------

type_select

event_date_input

amount_input

notes_input

------------------------------------------------

NOTE

✔ naming logico introdotto  
✔ migliora leggibilità e debug  
✔ sostituisce naming generico (select1, ecc.)

------------------------------------------------
5. MAPPING UI → DB
------------------------------------------------

type_select.value → events.type

event_date_input.value → events.event_date

amount_input.value → events.amount

notes_input.value → events.notes

------------------------------------------------

CAMPI NON UTILIZZATI (ATTUALMENTE)

project_id → NON integrato

entity_id → NON integrato

payload → NON utilizzato nel form

------------------------------------------------

NOTE

✔ schema DB più ampio del form  
✔ estensioni previste in step successivi  

------------------------------------------------
6. ERROR CASE (REALI)
------------------------------------------------

CAMPI MANCANTI

- event_date null → record inserito con campo nullo  
- amount null → record inserito con valore nullo  

✔ nessun blocco lato DB  
✔ comportamento coerente con append-only  

------------------------------------------------

INPUT INVALIDO

- testo in campo numerico → bloccato lato UI  
- JSON malformato → errore API  

------------------------------------------------

BODY NON LETTO

- JSON in formato key/value → insert vuoto  

------------------------------------------------
7. FLUSSO COMPLETO
------------------------------------------------

1. Utente compila form Retool:

- seleziona tipo evento  
- seleziona data  
- inserisce importo  
- inserisce note  

------------------------------------------------

2. Utente clicca:

"Salva evento"

------------------------------------------------

3. Retool:

→ esegue query insert_event

------------------------------------------------

4. Query:

→ invia POST a Supabase API

------------------------------------------------

5. Supabase:

→ riceve JSON  
→ valida struttura minima  
→ inserisce record  

------------------------------------------------

6. Database:

→ nuova riga append-only  

------------------------------------------------

7. Risultato:

→ evento persistito  

------------------------------------------------
STATO
------------------------------------------------

🟢 RUNTIME COMPLETO  
🟢 DEBUG POSSIBILE  
🟢 SISTEMA RICOSTRUIBILE  

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
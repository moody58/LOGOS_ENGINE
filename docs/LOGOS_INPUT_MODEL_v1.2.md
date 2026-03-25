# LOGOS INPUT MODEL — v1.2

## 1 — DEFINIZIONE

L'input è una stringa libera in linguaggio naturale.

Esempio:

"500 euro acconto Alfie Mario Rossi"

---

## 2 — PIPELINE

INPUT (testo libero)
        ↓
PARSING (estrazione non deterministica)
        ↓
EVENT (inserimento minimo)
        ↓
ARRICCHIMENTO (operazioni successive)

---

## 3 — PRINCIPI FONDAMENTALI

- parsing tollerante
- nessuna validazione bloccante
- nessuna classificazione forzata
- incompletezza ammessa
- separazione parsing / interpretazione

---

## 4 — COMPONENTI ESTRATTE (OPZIONALI)

Il parsing può estrarre:

- amount
- unit
- parole chiave
- frammenti semantici

NON è obbligatorio estrarre:

- type
- entity
- project

---

## 5 — TYPE MODEL (NON DETERMINISTICO)

Il type NON deve essere dedotto in modo affidabile.

Se non evidente:

→ type = "Evento"

La classificazione avviene in fase di arricchimento.

---

## 6 — ENTITY / PROJECT MODEL

Entity e Project NON vengono determinati con certezza.

Se ambigui:

→ entity_id = NULL  
→ project_id = NULL  

La contestualizzazione è una operazione successiva.

---

## 7 — UNIT MODEL

Campo introdotto:

events.unit (TEXT, opzionale)

Caratteristiche:

- valore grezzo (es: "ora", "kg", "€")
- nessuna normalizzazione
- nessuna conversione
- nessun vincolo

---

## 8 — PAYLOAD MODEL

Le informazioni non strutturate vengono salvate in:

events.payload

Caratteristiche:

- testo libero o JSON
- contiene input originale e residui parsing
- nessuna struttura obbligatoria

---

## 9 — REGOLA DI MINIMO

Qualsiasi input genera un evento.

Non esiste requisito minimo.

Il minimo è:

→ esistenza dell’input

---

## 10 — OUTPUT GARANTITO

Ogni input produce:

- una riga in events
- status = NEW

Anche se:

- type = NULL o "Evento"
- amount = NULL
- entity_id = NULL
- project_id = NULL

---

## 11 — SEPARAZIONE RESPONSABILITÀ

Parsing:

- estrazione minima
- nessuna interpretazione

Utente / sistema:

- contestualizzazione
- arricchimento
- classificazione

---

## 12 — PRINCIPIO CHIAVE

Meglio un evento incompleto che un evento sbagliato.

---

## 13 — MODELLO OPERATIVO

INPUT → EVENT GREZZO → OPERAZIONI

NON:

INPUT → EVENT COMPLETO

---

## 14 — STATO MODELLO

✔ tollerante  
✔ non deterministico  
✔ coerente con LOGOS  
✔ pronto per uso reale  

---
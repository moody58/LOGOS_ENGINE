#DOC.LOGOS_SYSTEM_STATE_v0.4

# LOGOS SYSTEM STATE — v0.4

## 1 — IDENTITÀ SISTEMA

LOGOS è un Event Ledger System.

Principi fondamentali:

- append-only
- DB = storage
- nessuna logica nel DB
- nessun workflow
- input non bloccante
- operazioni indipendenti sugli eventi

---

## 2 — ARCHITETTURA CORE

Il sistema è composto da 4 layer:

1. FACT → events  
2. STATE → status  
3. DIMENSION → projects / entities  
4. LOGGING → system_logs  

---

## 3 — SCHEMA DATABASE

### 3.1 — TABELLA EVENTS

```sql
events (
  id UUID PRIMARY KEY,
  created_at TIMESTAMP,
  event_date DATE,
  type TEXT,
  amount NUMERIC,
  status TEXT DEFAULT 'NEW',
  project_id UUID,
  entity_id UUID
)
SIGNIFICATO CAMPI

id
Identificatore univoco evento.

created_at
Timestamp creazione evento (append-only).

event_date
Data reale dell’evento (può differire da created_at).

type
Tipologia evento (es. Entrata, Uscita, Tempo, ecc.).

amount
Valore numerico associato (può essere NULL).

status
Etichetta operativa dell’evento.

Valori:

NEW
WRITTEN
ERROR

project_id
Riferimento al contesto (projects).

entity_id
Riferimento al soggetto (entities).

LOGICA EVENTS
ogni evento è indipendente
può essere incompleto (NULL ammessi)
non esistono vincoli di validazione
non esiste aggiornamento obbligatorio
append-only (no DELETE)
UPDATE consentiti per arricchimento e classificazione
I campi possono essere NULL.
La completezza è progressiva.

3.2 — TABELLA PROJECTS
projects (
  id UUID PRIMARY KEY,
  name TEXT,
  status TEXT,
  parent_id UUID
)
SIGNIFICATO CAMPI

id
Identificatore progetto.

name
Nome progetto.

status
Stato descrittivo (non operativo core).

parent_id
Riferimento a progetto padre (gerarchia opzionale).

LOGICA PROJECTS
rappresenta il contesto degli eventi
può essere gerarchico (padre/figlio)
nessuna logica automatica di aggregazione
uso completamente libero
3.3 — TABELLA ENTITIES
entities (
  id UUID PRIMARY KEY,
  name TEXT,
  type TEXT
)
SIGNIFICATO CAMPI

id
Identificatore entity.

name
Nome soggetto (cliente, fornitore, persona, ecc.).

type
Categoria entity (libera).

LOGICA ENTITIES
rappresenta il soggetto coinvolto
nessuna deduplicazione automatica
nessun vincolo semantico
3.4 — TABELLA SYSTEM_LOGS
system_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  event_id UUID,
  action TEXT,
  timestamp TIMESTAMP DEFAULT NOW(),
  note TEXT
)
SIGNIFICATO CAMPI

id
Identificatore log.

event_id
Evento a cui il log si riferisce.

action
Tipo operazione eseguita.

Valori standard:

CREATE_EVENT
LINK_ENTITY_PROJECT
ENRICH_EVENT
MARK_WRITTEN
MARK_ERROR

timestamp
Momento dell’operazione.

note
Campo descrittivo opzionale.

LOGICA SYSTEM_LOGS
append-only
logging manuale
non vincolante
non influenza il comportamento del sistema
non è fonte di verità
la fonte di verità è sempre events
serve per osservazione e analisi

4 — ACTION MODEL v1.1

Operazioni indipendenti sugli eventi:

REGISTRARE → INSERT events
CONTESTUALIZZARE → UPDATE project_id / entity_id
ARRICCHIRE → UPDATE campi
ETICHETTARE WRITTEN → status = WRITTEN
ETICHETTARE ERROR → status = ERROR

5 — MODELLO OPERATIVO
EVENTO
  ↳ operazioni indipendenti nel tempo

6 — STATUS (RIDEFINIZIONE)

status NON è stato di processo.

È una etichetta interpretativa.

NEW → non interpretato
WRITTEN → valido
ERROR → problematico

Lo status NON determina comportamento del sistema.
È solo interpretazione dell'evento.

7 — LOGGING MODEL

Il logging rappresenta:

la storia delle operazioni sugli eventi.

NON è obbligatorio
NON è automatico
NON è vincolante

8 — STATO ATTUALE SISTEMA

✔ events attivo
✔ status attivo
✔ dimension layer attivo
✔ logging attivo
✔ query operative attive
✔ flusso reale validato

9 — CAPACITÀ ATTUALI

Il sistema consente:

raccolta dati non bloccante
classificazione eventi
collegamento contesto/soggetto
osservazione operazioni
analisi base
utilizzo operativo reale
10 — LIMITI CONSAPEVOLI

Non implementati:

utenti
audit user
log automatici
business logic avanzata
gestione economica complessa
deduplicazione
automazioni
11 — NODI APERTI

ASPRI:

soci
split economici
gerarchie progetto avanzate

ADEXIMA:

valore tempo
marginalità

SISTEMA:

identity
audit
multi-tenant

MAURIZIOLAB:

supervisione multi-progetto
12 — STATO DI MATURITÀ

LOGOS è:

stabile
coerente
ricostruibile
utilizzabile
osservabile
13 — PRINCIPIO CHIAVE
Il sistema è definito dallo schema + dalle operazioni, non dalla UI.
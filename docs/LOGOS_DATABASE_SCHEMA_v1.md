
------------------------------------------------
LOGOS_DATABASE_SCHEMA_v1
------------------------------------------------

STATO: REALE (Supabase attivo)

------------------------------------------------
TABELLE
------------------------------------------------

1. events
2. projects
3. entities

------------------------------------------------
TABELLA: events
------------------------------------------------

DESCRIZIONE

Ledger principale append-only.
Contiene tutti gli eventi del sistema.

------------------------------------------------

CAMPI

id
- tipo: UUID
- primary key
- default: gen_random_uuid()

created_at
- tipo: TIMESTAMP
- default: now()

event_date
- tipo: DATE

project_id
- tipo: UUID
- relazione logica → projects.id

entity_id
- tipo: UUID
- relazione logica → entities.id

type
- tipo: TEXT
- valori runtime:
  - Entrata
  - Uscita
  - Tempo
  - Evento

amount
- tipo: NUMERIC

unit
- tipo: TEXT

reference_id
- tipo: TEXT

source
- tipo: TEXT

payment_method
- tipo: TEXT

notes
- tipo: TEXT

raw_input
- tipo: TEXT

payload
- tipo: JSONB
- default: {}

------------------------------------------------

NOTE

✔ tabella append-only  
✔ nessun vincolo FK rigido  
✔ flessibilità tramite payload  

------------------------------------------------
TABELLA: projects
------------------------------------------------

DESCRIZIONE

Contesto logico degli eventi.

------------------------------------------------

CAMPI

id
- tipo: UUID
- primary key

name
- tipo: TEXT
- unique

parent_project_id
- tipo: UUID

type
- tipo: TEXT

status
- tipo: TEXT
- default: ACTIVE

created_at
- tipo: TIMESTAMP

------------------------------------------------

NOTE

✔ utilizzata per filtraggio eventi  
✔ supporto gerarchie (opzionale)  

------------------------------------------------
TABELLA: entities
------------------------------------------------

DESCRIZIONE

Soggetti del sistema:
persone, aziende, animali, ecc.

------------------------------------------------

CAMPI

id
- tipo: UUID
- primary key

type
- tipo: TEXT

name
- tipo: TEXT

parent_entity_id
- tipo: UUID

status
- tipo: TEXT

metadata
- tipo: JSONB
- default: {}

created_at
- tipo: TIMESTAMP

updated_at
- tipo: TIMESTAMP

------------------------------------------------

NOTE

✔ struttura flessibile  
✔ supporto gerarchie  
✔ estendibile via metadata  

------------------------------------------------
RELAZIONI LOGICHE
------------------------------------------------

events.project_id → projects.id  
events.entity_id → entities.id  

✔ relazioni non vincolate (no FK)  
✔ gestione applicativa (runtime)

------------------------------------------------
PAYLOAD STRATEGY
------------------------------------------------

✔ JSONB per estensioni evento  
✔ evita modifiche schema  
✔ governato da runtime spec  

------------------------------------------------
STATO
------------------------------------------------

🟢 SCHEMA ATTIVO  
🟢 VALIDATO IN PRODUZIONE  
🟢 PRONTO PER ISTANZE  

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
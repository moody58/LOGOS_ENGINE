#DOC.logos.system_rebuild_plan_v2

------------------------------------------------
LOGOS_SYSTEM_REBUILD_PLAN_v2
------------------------------------------------

STATO: STRATEGICO — OTTIMIZZATO (NO OVER-ENGINEERING)

------------------------------------------------
1. ARCHITETTURA TARGET
------------------------------------------------

LOGOS ENGINE 2.0

INPUT (Retool / API / Import)
↓
EVENT INSERT (Supabase)
↓
EVENT STATUS SYSTEM (queue logica)
↓
PROCESSOR (logica minima attivabile)
↓
RESOLUTION (entity / project)
↓
EVENT LEDGER (events)
↓
DIMENSIONS (entities / projects)
↓
ANALYTICS (SQL views)
↓
DASHBOARD (Looker / Metabase)

------------------------------------------------

PRINCIPIO CHIAVE

✔ Supabase = CORE DATI  
✔ LOGOS = LOGICA LEGGERA  
✔ UI = CONTROLLO INPUT  

------------------------------------------------
2. COMPONENTI REALI (OTTIMIZZATI)
------------------------------------------------

CORE MINIMO

✔ events (già esistente)
✔ events.status (queue logica)
✔ entities
✔ projects

------------------------------------------------

PROCESSING

✔ NO engine centralizzato
✔ SI logiche attivabili:

- Retool trigger
- SQL update
- eventuale function (solo se serve)

------------------------------------------------

QUEUE

✔ NON esiste tabella separata
✔ events.status = queue

------------------------------------------------

VALIDAZIONE

✔ 80% UI (Retool)
✔ 20% DB (constraint leggeri)

------------------------------------------------

SUGGESTION

✔ query SQL (LIKE + LIMIT)
✔ NO engine dedicato

------------------------------------------------

LOGGING

✔ tabella semplice:

system_logs

campi:
- id
- event_id
- action
- timestamp
- note

------------------------------------------------

ANALYTICS

✔ SQL views
✔ NO formule complesse

------------------------------------------------

DASHBOARD

✔ Looker Studio (default)
✔ Metabase (opzione open source)

------------------------------------------------
3. PRIORITÀ (RICALIBRATE)
------------------------------------------------

FASE 1 — CONTROLLO EVENTI

✔ events.status
✔ flusso NEW → WRITTEN

------------------------------------------------

FASE 2 — PROCESSOR MINIMO

✔ update status
✔ gestione base pipeline

------------------------------------------------

FASE 3 — ENTITY / PROJECT

✔ lookup semplice
✔ creazione base

------------------------------------------------

FASE 4 — LOGGING + IDEMPOTENZA

✔ system_logs
✔ reference_id (light)

------------------------------------------------

FASE 5 — UX + ANALYTICS

✔ dropdown dinamici
✔ viste SQL
✔ dashboard

------------------------------------------------

------------------------------------------------
4. COSA NON FARE
------------------------------------------------

✖ NON ricreare RAW_INPUT
✖ NON creare processor complesso
✖ NON duplicare logica UI
✖ NON costruire suggestion engine
✖ NON creare dashboard custom

------------------------------------------------
5. PRINCIPI GUIDA
------------------------------------------------

1. semplicità > completezza  
2. usare strumenti nativi  
3. evitare duplicazioni  
4. logica minima necessaria  
5. evoluzione incrementale  

------------------------------------------------
6. RISULTATO ATTESO
------------------------------------------------

LOGOS 2.0:

✔ modulare  
✔ replicabile  
✔ semplice  
✔ estendibile  
✔ senza refactor  

------------------------------------------------

STATO

🟢 STRATEGIA CONSOLIDATA  
🟢 PRONTO PER ESECUZIONE  

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
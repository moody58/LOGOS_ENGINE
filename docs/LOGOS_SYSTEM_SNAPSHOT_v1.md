
------------------------------------------------
LOGOS_SYSTEM_SNAPSHOT_v1
------------------------------------------------

STATO: REALE (IMPLEMENTATO)

------------------------------------------------
ARCHITETTURA GENERALE
------------------------------------------------

LOGOS è un sistema event-driven basato su:

INPUT → API → DATABASE → QUERY

------------------------------------------------
STACK ATTUALE
------------------------------------------------

DATABASE
- Supabase (Postgres)

API
- Supabase REST API (PostgREST)

INPUT LAYER
- Retool (UI form)

------------------------------------------------
PIPELINE REALE
------------------------------------------------

1. INPUT

Utente inserisce dati tramite form Retool:

- type
- event_date
- amount
- notes

------------------------------------------------

2. TRIGGER

Button "Salva evento"

→ attiva query:

POST /rest/v1/events

------------------------------------------------

3. API LAYER

Supabase REST API

✔ riceve JSON  
✔ valida formato base  
✔ inserisce record  

------------------------------------------------

4. DATABASE

Tabella: events

✔ nuovo record append-only  
✔ nessuna modifica record esistenti  

------------------------------------------------

5. STORAGE

Dati persistiti in Supabase

✔ immediatamente disponibili  
✔ interrogabili via query  

------------------------------------------------
MODALITÀ INPUT
------------------------------------------------

ATTUALE

✔ Retool form (UI user-friendly)  
✔ inserimento manuale guidato  

PREVISTO

- API dirette (future)
- import batch (future)

------------------------------------------------
ENTRY POINT
------------------------------------------------

ATTUALE

✔ Retool App (accesso tramite interfaccia)  

NOTA

✔ UI attualmente tecnica  
✔ migliorabile in UX futura  

------------------------------------------------
GESTIONE SICUREZZA
------------------------------------------------

✔ utilizzo anon key  
✔ accesso via REST API  
✔ nessuna esposizione service_role  

NOTA

✔ RLS non configurato (fase iniziale)  

------------------------------------------------
COMPORTAMENTO SISTEMA
------------------------------------------------

✔ append-only (ledger)  
✔ input non bloccante  
✔ logica minima nel DB  
✔ flessibilità via payload  

------------------------------------------------
STATO ATTUALE
------------------------------------------------

LOGOS v1:

🟢 CORE ATTIVO  
🟢 INPUT LAYER FUNZIONANTE  
🟢 DATABASE STABILE  
🟢 PIPELINE VALIDATA  

------------------------------------------------
LIMITI ATTUALI
------------------------------------------------

- UI non ottimizzata  
- nessuna gestione duplicati attiva  
- entity/project non ancora integrati nel form  
- nessuna dashboard attiva  

------------------------------------------------
PRONTO PER
------------------------------------------------

✔ test utente reale  
✔ estensione input (entity/project)  
✔ sviluppo dashboard  

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
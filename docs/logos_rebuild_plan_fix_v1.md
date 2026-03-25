#DOC.logos.rebuild_plan_fix_v1

------------------------------------------------
OBIETTIVO
------------------------------------------------

Allineare LOGOS_REBUILD_PLAN a:

✔ principi runtime LOGOS
✔ semplicità operativa
✔ architettura Supabase-native
✔ zero over-engineering

------------------------------------------------
FIX STRUTTURALI
------------------------------------------------

1. EVENT QUEUE

❌ RIMUOVERE concetto di queue separata

✔ SOSTITUIRE con:

events.status

Motivazione:

- append-only già sufficiente
- Supabase NON richiede queue esterna
- evita duplicazione layer

------------------------------------------------

2. PROCESSOR

❌ evitare orchestratore complesso

✔ introdurre:

PROCESSOR MINIMAL

Comportamento:

- legge eventi status = NEW
- applica logica minima
- aggiorna status → WRITTEN

✔ NO:

- pipeline multi-step
- code distribuite
- FSM avanzate

------------------------------------------------

3. NORMALIZATION / VALIDATION

❌ NON creare layer separati backend

✔ spostare in:

UI (Retool)

Motivazione:

- UX-driven
- riduzione complessità
- coerenza con runtime spec

------------------------------------------------

4. ENTITY / PROJECT WORKFLOW

❌ NO workflow asincroni complessi

✔ usare:

lookup diretto + creazione semplice

Regola:

- match → collega
- no match → crea

✔ miglioramenti successivi, NON ora

------------------------------------------------

5. IDEMPOTENZA

❌ NON implementare subito fingerprint complessi

✔ introdurre:

reference_id (campo semplice)

✔ deduplica:

logica applicativa futura

------------------------------------------------

6. LOGGING

❌ NO sistema logging complesso

✔ introdurre:

system_logs (semplice)

Uso:

- debug
- errori

------------------------------------------------

7. EVENT STATE SYSTEM

✔ introdurre:

status su events

Valori ammessi:

- NEW
- WRITTEN
- ERROR

✔ NO altri stati in questa fase

------------------------------------------------

8. ANALYTICS

❌ NO layer analytics custom

✔ usare:

- SQL views
- Looker / Metabase

------------------------------------------------

9. DASHBOARD

✔ aggiungere esplicitamente:

DASHBOARD LAYER

Componenti:

- KPI base
- prima nota
- filtri

------------------------------------------------

10. INPUT LAYER

✔ definire chiaramente:

- Retool → principale
- Form → opzionale

✔ UX guidata = responsabilità UI

------------------------------------------------

------------------------------------------------
SEMPLIFICAZIONE GLOBALE
------------------------------------------------

Rimuovere:

✖ queue logica separata  
✖ orchestratore complesso  
✖ workflow asincroni avanzati  
✖ validation backend strutturata  
✖ sistemi duplicati  

------------------------------------------------

------------------------------------------------
ARCHITETTURA RISULTANTE
------------------------------------------------

INPUT (Retool)
↓
INSERT events (status = NEW)
↓
PROCESSOR MINIMAL
↓
status → WRITTEN
↓
QUERY / VIEW
↓
DASHBOARD

------------------------------------------------

------------------------------------------------
PRINCIPI CONSOLIDATI
------------------------------------------------

✔ append-only sempre  
✔ DB semplice  
✔ logica minima  
✔ UX intelligente  
✔ evoluzione progressiva  

------------------------------------------------

------------------------------------------------
STATO
------------------------------------------------

✔ rebuild plan corretto  
✔ pronto per roadmap operativa  
✔ base stabile LOGOS 2.0  

------------------------------------------------
FINE DOCUMENTO
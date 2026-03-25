
#DOC.logos.runtime_spec_v1

------------------------------------------------
LOGOS_RUNTIME_SPEC_v1
------------------------------------------------

------------------------------------------------
SEZIONE 1 — INSERIMENTO EVENTO
------------------------------------------------

Un evento è l’unità minima di LOGOS.

Rappresenta qualsiasi azione tracciabile:
- entrata
- uscita
- attività
- pagamento
- evento generico

REGOLE

1. Ogni evento deve essere registrato come nuova riga (append-only)

→ gli eventi NON vengono sovrascritti  
→ eventuali correzioni avvengono tramite nuovi eventi  

2. L’inserimento può avvenire da più fonti:

- form (utente base)  
- tool (Retool)  
- API (futuro)  
- import  

3. Ogni evento deve contenere almeno:

- type  
- event_date  
- amount (se economico)  

4. L’evento può essere arricchito con:

- project  
- entity  
- notes  
- payload  

5. L’inserimento NON deve essere bloccante

→ errori non critici NON impediscono la registrazione  

6. Obiettivo:

→ registrare velocemente  
→ NON perdere informazioni 

7. Le modifiche agli eventi sono sempre registrate come nuovi eventi

→ il sistema non sovrascrive eventi esistenti  
→ ogni correzione genera un nuovo evento  
→ la vista utente mostra lo stato aggiornato   

------------------------------------------------
SEZIONE 2 — EVENT STRUCTURE
------------------------------------------------

Un evento è composto da:

1. CAMPI CORE  
2. CAMPI RELAZIONALI  
3. CAMPI DESCRITTIVI  
4. PAYLOAD  

CAMPI CORE:

- type  
- event_date  
- amount  

CAMPI RELAZIONALI:

- project_id  
- entity_id  

CAMPI DESCRITTIVI:

- notes  
- source  
- payment_method  

PAYLOAD:

→ estensione flessibile controllata  

PRINCIPI:

✔ schema stabile  
✔ flessibilità nel payload  
✔ evento leggibile anche senza payload  

------------------------------------------------
SEZIONE 3 — VALIDAZIONE INPUT
------------------------------------------------

✔ NON bloccante  
✔ progressiva  

PRINCIPIO:

✔ salvare sempre > bloccare  

LIVELLI:

1. minima  
2. soft  
3. guidata (UI)  

ERRORI:

✔ critici (rari)  
✔ non critici (standard)  

PRINCIPI:

✔ continuità del dato  
✔ qualità progressiva 
✔ validazione forte delegata alla UI 

------------------------------------------------
SEZIONE 4 — ENTITY RESOLUTION
------------------------------------------------

Processo:

→ identifica  
→ collega  
→ crea entity  

FLUSSO:

input → matching → esito  

CASI:

✔ match → collega  
✔ no match → crea  

PRINCIPI:

✔ velocità > precisione iniziale  
✔ duplicati accettati  
✔ miglioramento progressivo  

------------------------------------------------
SEZIONE 5 — PROJECT RESOLUTION
------------------------------------------------

Processo:

→ identifica  
→ assegna  
→ crea project  

PRINCIPI:

✔ ogni evento ha contesto  
✔ progetto quasi obbligatorio  

CASI:

✔ match → assegna  
✔ no match → crea  
✔ se assente → assegnare progetto default (es. "GENERIC")

------------------------------------------------
SEZIONE 6 — IDEMPOTENZA
------------------------------------------------

✔ NON blocca inserimento  
✔ previene duplicati logici  

STRUMENTI FUTURI:

- reference_id  
- fingerprint  

PRINCIPI:

✔ meglio duplicato che perdita  
✔ deduplicazione successiva  

------------------------------------------------
SEZIONE 7 — USO PAYLOAD
------------------------------------------------

✔ estensione  
✔ non sostituzione  

REGOLE:

✔ semplice  
✔ coerente  
✔ non duplicata  

EVOLUZIONE:

→ da payload a campo strutturato se necessario  

------------------------------------------------
SEZIONE 8 — COMPORTAMENTO GLOBALE
------------------------------------------------

LOGOS è:

→ sistema event-driven  
→ append-only  
→ non bloccante  

COMPORTAMENTO:

1. registra sempre gli eventi  
2. non blocca l’utente  
3. accetta imperfezioni iniziali  
4. migliora nel tempo  

ARCHITETTURA LOGICA:

INPUT → EVENT → RELAZIONI → QUERY → ANALISI  

RESPONSABILITÀ:

DB:

✔ coerenza minima  
✔ storage  

UI:

✔ qualità input  
✔ esperienza utente  

ANALYTICS:

✔ interpretazione  
✔ KPI  

------------------------------------------------
STATO
------------------------------------------------

✔ documento completo  
✔ pronto per uso operativo  
✔ base per sviluppo input layer  

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
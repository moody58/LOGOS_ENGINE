# LOGOS — SYSTEM SPEC v1 (FINAL STATE)

## VISIONE SISTEMA

LOGOS è:

- Event Ledger System  
- replicabile per istanze indipendenti  
- modulare  
- scalabile senza refactor strutturali  

LOGOS NON è:

- gestionale contabile  
- sistema monolitico  
- tool dipendente da piattaforma  

---

## ARCHITETTURA GLOBALE

### CORE

- Supabase (Postgres)
- 1 database per istanza

### ISTANZE

- ASPRI  
- ADEXIMA  
- MAURIZIOLAB (futuro aggregatore)  

### PRINCIPI

- isolamento totale dati  
- replicabilità via template  

---

## DATA MODEL

### TABELLA PRINCIPALE

events (ledger append-only)

### ESTENSIONI

- payload JSONB → flessibilità controllata  
- entities → soggetti  
- projects → contesto  

### CONTROLLO

- NO tabelle CONTROLLO_*  
- SI query / views / Looker  

---

## LOGICA SISTEMA

### FLUSSO

INPUT (multi fonte)  
↓  
normalizzazione  
↓  
validazione UX  
↓  
lookup entity/project  
↓  
INSERT events  
↓  
query / views  
↓  
dashboard  

---

## SEPARAZIONE LOGICA

### DB (Supabase)

- coerenza minima  
- query riutilizzabili  
- viste (views)  
- aggregazioni  

### UI (Retool / Form)

- input guidato  
- suggerimenti  
- validazione interazione  

---

## INPUT LAYER

### MULTI-FONTE

- Retool (gestionale principale)  
- Google Form (fallback)  
- API (futuro)  
- import (futuro)  

### UX

- guidata  
- dropdown  
- suggerimenti  

---

## OUTPUT / ANALYTICS

Looker Studio:

- KPI  
- prima nota  
- saldo  
- analisi  

---

## MODELLO ECONOMICO

LOGOS gestisce:

- entrate  
- uscite  
- saldo  

ASPRI:

- acconti via events payload  

LOGOS NON gestisce:

- contabilità complessa  

---

## PAYLOAD GOVERNANCE

- estensione eventi  
- NON sostituisce campi principali  
- struttura controllata  
- naming coerente  

---

## DECISIONI CONSOLIDATE

- Supabase come core  
- istanze separate  
- logica: DB + UX layer  
- controllo via query / views  
- data model: event ledger + payload  

---

## EVOLUZIONE

- refactor strutturali NON previsti  
- micro-evoluzioni previste  

---

## RISCHI RESIDUI

1. UX Retool per utenti non tecnici  
   → fallback Google Form  

2. complessità iniziale  
   → accettata  

3. multi-tool  
   → gestito  

---

## STATO

- architettura definita  
- schema validato  
- pronto per build  
- sistema operativo  

---

## META

LOGOS v1 è:

- stabile  
- flessibile  
- estendibile  
- pronto per uso reale  

---

## RUOLO DOCUMENTO

Questo documento rappresenta:

- la definizione ufficiale del sistema LOGOS  
- la base per tutte le istanze future  
- il riferimento per audit e ricostruzione  

#DOC.LOGOS_SYSTEM_STATE_v0.5

# LOGOS SYSTEM STATE — v0.5

## 1 — IDENTITÀ SISTEMA

LOGOS è un Event Ledger System.

Principi:

- append-only
- DB = storage
- nessuna logica nel DB
- input non bloccante
- operazioni indipendenti

---

## 2 — ARCHITETTURA

Layer:

1. FACT → events  
2. STATE → status  
3. DIMENSION → projects / entities  
4. LOGGING → system_logs  

---

## 3 — TABELLA EVENTS (SCHEMA REALE)

```sql
events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  created_at TIMESTAMP DEFAULT now(),
  event_date DATE,

  project_id UUID,
  entity_id UUID,

  type TEXT,
  amount NUMERIC,
  unit TEXT,

  reference_id TEXT,
  source TEXT,
  payment_method TEXT,

  notes TEXT,
  raw_input TEXT,
  payload JSONB DEFAULT '{}',

  status TEXT DEFAULT 'NEW'
)
4 — CLASSIFICAZIONE CAMPI
4.1 — CORE (strutturali)
id
created_at
event_date
status
4.2 — DIMENSIONALI
project_id
entity_id
4.3 — DESCRITTIVI
type
amount
unit
4.4 — ESTENSIONE OPERATIVA
reference_id
source
payment_method
4.5 — INPUT LAYER
raw_input
payload
4.6 — SUPPORTO
notes
5 — LOGICA EVENTS
PRINCIPI
ogni evento è valido anche se incompleto
nessun campo obbligatorio (oltre id)
NULL ammessi ovunque
nessuna validazione DB
RAW INPUT

Contiene:

→ input originale utente

Serve per:

audit
ricostruzione
debugging
PAYLOAD (JSONB)

Contiene:

→ dati non strutturati o parsing parziale

Caratteristiche:

flessibile
non vincolato
evolutivo
UNIT
valore grezzo (es: "ora", "€")
nessuna conversione
nessuna normalizzazione
REFERENCE_ID
riferimento esterno (es: fattura, bonifico)
opzionale
SOURCE
origine evento (manuale, import, ecc.)
opzionale
PAYMENT_METHOD
metodo pagamento (contanti, bonifico, ecc.)
opzionale
6 — STATUS

Valori:

NEW
WRITTEN
ERROR

Definizione:

Etichetta operativa, NON stato di processo.

7 — ACTION MODEL v1.1

Operazioni indipendenti:

REGISTRARE
CONTESTUALIZZARE
ARRICCHIRE
ETICHETTARE WRITTEN
ETICHETTARE ERROR
8 — INPUT MODEL (ALLINEATO)

INPUT → raw_input + payload → evento minimo

Parsing:

non deterministico
non bloccante
non completo
9 — LOGGING

system_logs traccia:

azioni sugli eventi
timeline operativa
10 — STATO ATTUALE

✔ schema evoluto
✔ input layer completo
✔ logging attivo
✔ sistema operativo

11 — CAPACITÀ

Il sistema ora può:

catturare input naturale completo
mantenere informazione grezza
evolvere evento nel tempo
tracciare operazioni
12 — PRINCIPIO CHIAVE
Lo schema è progettato per NON perdere informazione.
13 — NOTE ARCHITETTURALI
raw_input + payload = memoria completa input
campi strutturati = vista interpretata
nessuna informazione viene scartata
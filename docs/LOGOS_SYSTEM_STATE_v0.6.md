#DOC.LOGOS_SYSTEM_STATE_v0.6 (UPDATED)

# LOGOS SYSTEM STATE — v0.6

## 1 — IDENTITÀ SISTEMA

LOGOS è un Event Ledger System.

Principi fondamentali:

- append-only (no DELETE)
- UPDATE consentiti per arricchimento e classificazione
- DB = storage
- nessuna logica nel DB
- input non bloccante
- operazioni indipendenti sugli eventi
- nessuna perdita di informazione

---

## 2 — ARCHITETTURA

Layer del sistema:

1. FACT → events  
2. STATE → status  
3. DIMENSION → projects / entities  
4. LOGGING → system_logs  

---

## 3 — EVENTS — SCHEMA REALE

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
4 — EVENTS — SCHEMA ALIGNMENT v1.0
4.1 — PRINCIPIO BASE

La tabella events è composta da layer logici distinti.

Ogni campo appartiene a un layer preciso.

4.2 — LAYER DEFINITIVI
CORE (identità evento)
id
created_at
event_date
status

Definiscono l’esistenza dell’evento.

DIMENSION
project_id
entity_id

Definiscono:

contesto
soggetto
DESCRIZIONE STRUTTURATA
type
amount
unit

Rappresentano:

interpretazione dell’evento

⚠️ opzionale
⚠️ non garantita

INPUT MEMORY (CRITICO)
raw_input
payload

Definiscono:

fonte di verità dell’input

raw_input → input originale
payload → parsing parziale / dati non strutturati

👉 QUESTO È IL LAYER PIÙ IMPORTANTE

ESTENSIONE OPERATIVA
reference_id
source
payment_method

Metadati operativi.

NON core.

SUPPORTO
notes

Campo libero.

NON strutturale.

5 — PRINCIPIO DI VERITÀ

La fonte di verità è composta da:

→ raw_input (input originale)
→ payload (dati non strutturati)
→ events (stato attuale dell’evento)

Questi tre livelli coesistono.

6 — PRINCIPIO DI EVOLUZIONE

Un evento evolve nel tempo:

input grezzo → interpretazione → arricchimento
7 — PRINCIPIO DI SICUREZZA

Nessuna informazione deve essere persa.

Se dubbio:

→ salvare in payload

8 — VINCOLI SISTEMA
nessun campo obbligatorio (oltre id)
NULL ammessi
nessuna logica DB
nessuna validazione
nessun parsing deterministico
9 — INPUT MODEL v1.2 (ALLINEATO)

INPUT → parsing non deterministico → evento minimo

Caratteristiche:

input libero
parsing tollerante
nessuna classificazione forzata
incompletezza ammessa
10 — ACTION MODEL v1.1

Operazioni indipendenti sugli eventi:

REGISTRARE
CONTESTUALIZZARE
ARRICCHIRE
ETICHETTARE WRITTEN
ETICHETTARE ERROR

Nessun workflow.

11 — STATUS MODEL

Valori:

NEW
WRITTEN
ERROR

Definizione:

Etichetta operativa.

Lo status NON determina comportamento del sistema.
È solo interpretazione dell’evento.

12 — LOGGING MODEL

system_logs:

traccia operazioni sugli eventi
append-only
manuale
osservativo

Il logging NON è fonte di verità.
La fonte di verità è sempre events.

13 — STATO ATTUALE SISTEMA

✔ schema completamente allineato
✔ input model coerente
✔ action model stabilizzato
✔ logging attivo
✔ sistema operativo reale

14 — CAPACITÀ

Il sistema è in grado di:

catturare input naturale
preservare informazione grezza
evolvere eventi nel tempo
tracciare operazioni
supportare analisi
15 — PRINCIPIO CHIAVE
Il sistema preserva la realtà, non la semplifica.
16 — STATO DI MATURITÀ

LOGOS è:

coerente
stabile
ricostruibile
estensibile
non fragile
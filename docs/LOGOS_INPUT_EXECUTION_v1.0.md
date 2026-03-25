# LOGOS INPUT EXECUTION — MINIMAL v1.0

## 1 — FORMATO INPUT REALE

L’unico input richiesto è:

raw_input = testo libero

Esempio:

"500 euro acconto Alfie Mario Rossi"

---

## 2 — INSERT MINIMO

```sql
INSERT INTO events (
  raw_input,
  amount,
  unit,
  payload,
  status
)
VALUES (
  'testo input',
  valore_opzionale,
  unit_opzionale,
  json_parziale,
  'NEW'
);

Se payload non viene costruito:
→ usare '{}'::jsonb come default

3 — CAMPI MINIMI UTILIZZATI
OBBLIGATORI (operativamente)
raw_input
payload

raw_input è SEMPRE obbligatorio.
Rappresenta la fonte primaria dell’evento.

OPZIONALI (solo se evidenti)
amount
unit
NON USATI IN INPUT
project_id
entity_id
type
notes
payment_method
reference_id

→ tutto NULL

4 — PAYLOAD MINIMO

Struttura standard:

{
  "raw": "testo originale",
  "tokens": ["parole", "chiave"]
}

Il payload può essere anche vuoto.

Esempio valido:

{}
5 — FLUSSO OPERATIVO
scrivi input
↓
INSERT
↓
evento creato
↓
fine
6 — PRINCIPI
nessun controllo
nessuna validazione
nessun completamento
nessun blocco
7 — ESEMPI REALI
ESEMPIO 1
INSERT INTO events (
  raw_input,
  amount,
  unit,
  payload,
  status
)
VALUES (
  '500 euro acconto Alfie Mario Rossi',
  500,
  'euro',
  '{"raw":"500 euro acconto Alfie Mario Rossi","tokens":["acconto","Alfie","Mario Rossi"]}',
  'NEW'
);
ESEMPIO 2
INSERT INTO events (
  raw_input,
  amount,
  unit,
  payload,
  status
)
VALUES (
  'Sverminazione cuccioli della cucciolata marzo 2026',
  NULL,
  NULL,
  '{"raw":"Sverminazione cuccioli della cucciolata marzo 2026","tokens":["sverminazione","cucciolata marzo 2026"]}',
  'NEW'
);
ESEMPIO 3
INSERT INTO events (
  raw_input,
  amount,
  unit,
  payload,
  status
)
VALUES (
  'Aggiungi 50 minuti sviluppo Home sito Adexima',
  50,
  'minuti',
  '{"raw":"Aggiungi 50 minuti sviluppo Home sito Adexima","tokens":["sviluppo","Adexima"]}',
  'NEW'
);
8 — RISULTATO

Ogni input produce:

evento salvato
informazione completa preservata
status = NEW
9 — TEMPO OPERATIVO

Obiettivo:

< 5 secondi per inserimento

10 — PRINCIPIO CHIAVE
Inserisci veloce → capisci dopo
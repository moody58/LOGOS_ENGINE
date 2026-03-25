#DOC.LOGOS_INPUT_MODEL_v1.0

# LOGOS INPUT MODEL — v1.0

## 1 — DEFINIZIONE

L'input è una stringa in linguaggio naturale che descrive un evento.

Esempio:

"500 euro acconto Alfie Mario Rossi"

---

## 2 — PIPELINE

```text
INPUT (testo libero)
        ↓
PARSING (estrazione componenti)
        ↓
MAPPING (assegnazione campi)
        ↓
EVENT (insert in DB)
3 — COMPONENTI MINIME

Ogni input può contenere:

type
amount
unit
entity
project
note
4 — MAPPATURA COMPONENTI → DB
componente	campo DB
type	events.type
amount	events.amount
entity	entities.name → entity_id
project	projects.name → project_id
note	events.note / payload
unit	events.type o note
5 — ESEMPI REALI
ESEMPIO 1

Input:

"500 euro acconto Alfie Mario Rossi"

Mapping:

type: Entrata
amount: 500
project: Alfie
entity: Mario Rossi
note: acconto
ESEMPIO 2

Input:

"vaccinazione Amanda cimurro"

Mapping:

type: Spesa
amount: NULL
project: Amanda
entity: cimurro
note: vaccinazione
ESEMPIO 3

Input:

"1 ora sopralluogo Villa Sierri"

Mapping:

type: Tempo
amount: 1
unit: ora
project: Villa Sierri
entity: NULL
note: sopralluogo
6 — REGOLE DI PARSING
6.1 — AMOUNT

Se presente numero:

→ amount = numero

6.2 — UNIT

Se presente:

euro → valore economico
ora / ore → tempo
6.3 — TYPE (euristica)
presenza euro → Entrata / Spesa
presenza ora → Tempo
default → Evento
6.4 — ENTITY

Ultimo nome proprio / persona / soggetto identificabile.

6.5 — PROJECT

Elemento contestuale (luogo, cucciolata, cliente).

6.6 — NOTE

Tutto ciò che non viene classificato.

7 — REGOLE DI MINIMO

Per creare un evento:

✔ almeno 1 tra:

type
note
8 — NULL POLICY

È accettato:

amount = NULL
entity_id = NULL
project_id = NULL
9 — NO VALIDAZIONE BLOCCANTE

Il sistema NON deve:

rifiutare input
richiedere completezza
correggere automaticamente
10 — OUTPUT MINIMO GARANTITO

Ogni input produce sempre:

events row
status = NEW
11 — SEPARAZIONE RESPONSABILITÀ

Parsing ≠ completamento

parsing → crea evento
utente → arricchisce / corregge
12 — PRINCIPIO CHIAVE
Meglio un evento imperfetto che nessun evento.

---

#INSIGHT.LOGOS.input_truth

Questo è il punto più importante:

👉 LOGOS NON capisce perfettamente  
👉 LOGOS **cattura velocemente**

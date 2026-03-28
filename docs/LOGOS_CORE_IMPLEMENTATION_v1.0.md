#DOC.LOGOS_CORE_IMPLEMENTATION_v1.0

------------------------------------------------
OVERVIEW
------------------------------------------------

Questo documento descrive in modo completo e ricostruibile:

✔ struttura database
✔ policies RLS
✔ API (REST + RPC)
✔ configurazione Retool
✔ query e script
✔ flusso operativo

Obiettivo:

→ ricostruzione totale del sistema senza ambiguità

------------------------------------------------
1 — DATABASE (SUPABASE)
------------------------------------------------

TABella: events

Struttura:

- id (uuid, PK)
- created_at (timestamp)
- event_date (date, nullable)
- project_id (uuid, nullable)
- entity_id (uuid, nullable)
- type (text, nullable)
- amount (numeric, nullable)
- unit (text, nullable)
- reference_id (text, nullable)
- source (text, nullable)
- payment_method (text, nullable)
- notes (text, nullable)
- raw_input (text)
- payload (jsonb)
- status (text)

Principi:

✔ nessun campo obbligatorio (oltre id)
✔ NULL ammessi
✔ nessuna logica DB

------------------------------------------------
2 — RLS (ROW LEVEL SECURITY)
------------------------------------------------

ATTIVO su events

Policies:

### SELECT

using:
true

---

### INSERT

with check:
true

---

### UPDATE

using:
true

with check:
true

---

Nota:

Senza "with check (true)" l’UPDATE fallisce silenziosamente.

------------------------------------------------
3 — RPC FUNCTION
------------------------------------------------

Nome:

update_event_status

SQL:

create or replace function update_event_status(
  event_id text,
  new_status text
)
returns void
language plpgsql
as $$
begin
  update events
  set status = new_status
  where id = event_id::uuid;
end;
$$;

Principio:

✔ evita PATCH REST instabile
✔ centralizza update

------------------------------------------------
4 — RETOOL — RESOURCE
------------------------------------------------

Resource: supabase_api

Base URL:
https://<project>.supabase.co/rest/v1

Headers:

apikey: <anon_key>
Authorization: Bearer <anon_key>
Content-Type: application/json

------------------------------------------------
5 — QUERY RETOOL
------------------------------------------------

### 5.1 — events_new

Method: GET  
URL: /events  

Params:

- select = *
- status = eq.NEW
- order = created_at.desc

---

### 5.2 — insert_event

Method: POST  
URL: /events  

Body (RAW):

{{ JSON.stringify({
  raw_input: raw_input,
  amount: amount,
  unit: unit,
  project_id: project_id,
  entity_id: entity_id,
  status: "NEW",
  payload: {}
}) }}

---

### 5.3 — update_written

Method: POST  
URL: /rpc/update_event_status  

Body:

{{ JSON.stringify({
  event_id: id,
  new_status: "WRITTEN"
}) }}

---

### 5.4 — update_error

Method: POST  
URL: /rpc/update_event_status  

Body:

{{ JSON.stringify({
  event_id: id,
  new_status: "ERROR"
}) }}

------------------------------------------------
6 — UI COMPONENTS (RETTOOL)
------------------------------------------------

Componenti:

- input_raw (TextArea)
- select1 (project)
- select2 (entity)
- button (insert)
- table1 (event list)

------------------------------------------------
7 — INSERT FLOW (SCRIPT)
------------------------------------------------

Button → Run script

await insert_event.trigger({
  additionalScope: {
    raw_input: input_raw.value,
    amount: input_raw.value && input_raw.value.match(/\d+/)
      ? Number(input_raw.value.match(/\d+/)[0])
      : null,
    unit: input_raw.value && input_raw.value.includes("min")
      ? "minuti"
      : null,
    project_id: select1.value || null,
    entity_id: select2.value || null
  }
});

await events_new.trigger();

------------------------------------------------
8 — TABLE ACTIONS
------------------------------------------------

Colonne:

✔ written_action (button)
✖ error_action (button)

---

### SCRIPT ✔

const row = currentRow;

if (!row || !row.id) return;

await update_written.trigger({
  additionalScope: {
    id: row.id
  }
});

await events_new.trigger();

---

### SCRIPT ✖

const row = currentRow;

if (!row || !row.id) return;

await update_error.trigger({
  additionalScope: {
    id: row.id
  }
});

await events_new.trigger();

------------------------------------------------
9 — PRINCIPI ARCHITETTURALI
------------------------------------------------

✔ DB = storage, non logica  
✔ input sempre accettato  
✔ parsing tollerante  
✔ update via RPC  
✔ UI → script → query  

---

Fonte di verità:

→ raw_input  
→ payload  
→ stato evento  

------------------------------------------------
10 — FLUSSO COMPLETO
------------------------------------------------

input → parsing → insert → list → click → update → refresh

---

tempo medio:

< 2 secondi per evento

------------------------------------------------
11 — LIMITI ATTUALI
------------------------------------------------

⚠ parsing semplice (regex)
⚠ unit non normalizzate
⚠ entity/project non risolti automaticamente
⚠ dati vuoti ammessi

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
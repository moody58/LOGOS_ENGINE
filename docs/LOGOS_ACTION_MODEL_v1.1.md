#DOC.LOGOS_ACTION_MODEL_v1.1

DEFINIZIONE BASE

Un evento è una unità informativa modificabile nel tempo tramite operazioni indipendenti.

Non esiste sequenza.
Non esiste stato di avanzamento.
Non esiste workflow.

AZIONE 1 — REGISTRARE EVENTO
✔ operazione

Creare un evento.

✔ effetto

→ nuova riga in events
→ status = NEW (default tecnico, non logico)

✔ natura

👉 operazione di creazione

AZIONE 2 — CONTESTUALIZZARE
✔ operazione

Associare un evento a:

project
entity
✔ effetto

→ valorizza project_id
→ valorizza entity_id

✔ natura

👉 operazione relazionale

AZIONE 3 — ARRICCHIRE
✔ operazione

Aggiungere o correggere informazioni.

✔ effetto

→ modifica campi descrittivi

✔ natura

👉 operazione informativa

AZIONE 4 — ETICHETTARE (WRITTEN)
✔ operazione

Assegnare etichetta operativa.

✔ effetto

→ status = WRITTEN

✔ natura

👉 classificazione, NON avanzamento

AZIONE 5 — ETICHETTARE (ERROR)
✔ operazione

Segnalare evento problematico.

✔ effetto

→ status = ERROR

✔ natura

👉 classificazione, NON fallimento di processo

#STRUCTURE.LOGOS.status_redefinition

🔁 STATUS NON È PROCESSO

Status NON significa:

❌ “a che punto sono”
❌ “fase completata”

Status significa:

interpretazione operativa dell’evento
✔ NEW

evento non ancora interpretato

✔ WRITTEN

evento ritenuto valido / utilizzabile

✔ ERROR

evento problematico / da rivedere

👉 Nessuna progressione obbligatoria
👉 Nessuna sequenza implicita

#STRUCTURE.LOGOS.independence_rules

🔓 INDIPENDENZA DELLE AZIONI

Ogni operazione:

✔ può essere eseguita in qualsiasi momento
✔ può essere ripetuta
✔ non dipende da altre

ESEMPI REALI
Caso 1
REGISTRA → WRITTEN

(senza project/entity)

Caso 2
REGISTRA → CONTESTUALIZZA (dopo giorni)
Caso 3
REGISTRA → ERROR → ARRICCHISCI → WRITTEN
Caso 4
REGISTRA → ARRICCHISCI → CONTESTUALIZZA

👉 nessun ordine imposto

#INSIGHT.LOGOS.true_model

Il modello reale è:

EVENTO
  ↳ operazioni indipendenti nel tempo

NON:

evento → step → step → step

#STRUCTURE.LOGOS.clean_representation

MODELLO CORRETTO
EVENTO

[REGISTRARE]
[CONTESTUALIZZARE]
[ARRICCHIRE]
[ETICHETTARE WRITTEN]
[ETICHETTARE ERROR]

👉 insieme di azioni disponibili
👉 NON sequenza

#DECISION.LOGOS.action_model_fixed

✔ eliminata ambiguità workflow
✔ mantenute 5 azioni
✔ status ridefinito correttamente
✔ modello coerente con LOGOS
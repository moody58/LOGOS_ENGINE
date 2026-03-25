#DOC

------------------------------------------------
LOGOS EVENTS — SCHEMA ALIGNMENT v1.0
------------------------------------------------

OBIETTIVO

Allineare:

✔ input model  
✔ schema database  
✔ principi LOGOS  

---

1 — PRINCIPIO BASE

La tabella events è composta da layer logici distinti.

Ogni campo appartiene a un layer preciso.

---

2 — LAYER DEFINITIVI

2.1 — CORE (identità evento)

- id  
- created_at  
- event_date  
- status  

👉 definiscono l'esistenza dell'evento

---

2.2 — DIMENSION

- project_id  
- entity_id  

👉 contesto e soggetto

---

2.3 — DESCRIZIONE STRUTTURATA

- type  
- amount  
- unit  

👉 interpretazione dell’evento

⚠️ opzionale, non garantita

---

2.4 — INPUT MEMORY (CRITICO)

- raw_input  
- payload  

👉 fonte di verità dell’input

✔ raw_input = input originale  
✔ payload = parsing parziale / dati non strutturati  

👉 QUESTO È IL LAYER PIÙ IMPORTANTE

---

2.5 — ESTENSIONE OPERATIVA

- reference_id  
- source  
- payment_method  

👉 metadati operativi  
👉 NON core  

---

2.6 — SUPPORTO

- notes  

👉 campo libero  
👉 non strutturale  

---

3 — PRINCIPIO DI VERITÀ

La fonte di verità è:

👉 raw_input + payload

NON:

❌ type  
❌ amount  
❌ project_id  

---

4 — PRINCIPIO DI EVOLUZIONE

Un evento evolve così:

input grezzo → interpretazione → arricchimento

---

5 — PRINCIPIO DI SICUREZZA

Nessuna informazione deve essere persa.

Se dubbio:

→ salvare in payload

---

6 — VINCOLI

✔ nessun campo obbligatorio (oltre id)  
✔ NULL ammessi  
✔ nessuna logica DB  
✔ nessuna validazione  

---

7 — STATO SCHEMA

✔ coerente  
✔ estensibile  
✔ non fragile  
✔ pronto per UI  

---

------------------------------------------------
FINE BLOCCO
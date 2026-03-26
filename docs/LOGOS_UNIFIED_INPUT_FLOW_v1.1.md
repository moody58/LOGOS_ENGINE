# LOGOS UNIFIED INPUT FLOW — v1.1

## 1 — SCHERMATA INIZIALE

Elementi:

- campo input singolo
- placeholder intelligente
- esempi visibili

Esempio:

"Scrivi un evento, un progetto o una modifica…"

---

## 2 — INPUT UTENTE

Input completamente libero.

Nessun vincolo.

---

## 3 — INTERPRETAZIONE (NON VINCOLANTE)

Il sistema propone una interpretazione.

NON prende decisioni.

---

### 3.1 — CLASSI POSSIBILI

- EVENT  
- CREATE_ENTITY  
- CREATE_PROJECT  
- UPDATE (esplicito)

---

### 3.2 — COMPONENTI

Se riconosciuti:

- amount  
- unit  
- entity (possibile)  
- project (possibile)  

---

## 4 — PREVIEW STRUTTURATA (OBBLIGATORIA)

Il sistema mostra SEMPRE una preview.

Nessuna azione viene eseguita senza preview.

---

### 4.1 — EVENTO

Mostra:

- type (suggested)
- amount/unit
- project (suggested)
- entity (suggested)
- note

---

### 4.2 — ENTITY

Mostra:

- nome
- tipo
- attributi

---

### 4.3 — PROJECT

Mostra:

- nome
- tipo
- parent

---

### 4.4 — UPDATE

Mostra:

- record target
- campi modificati
- valori prima/dopo

---

## 5 — AMBIGUITÀ

Se più interpretazioni:

→ il sistema propone opzioni  
→ l’utente sceglie  

---

## 6 — MICRO-GUIDANCE

Suggerimenti:

- project esistenti  
- entity esistenti  

NON vincolanti.

---

## 7 — MODIFICA RAPIDA

Tutti i campi modificabili:

- click → modifica  
- dropdown suggeriti  
- creazione nuova entità possibile  

---

## 8 — CONFERMA (OBBLIGATORIA)

Azioni:

- Conferma  
- Modifica  
- Annulla  

---

## 9 — ESECUZIONE

Solo dopo conferma:

- EVENT → INSERT events  
- ENTITY → INSERT entities  
- PROJECT → INSERT projects  
- UPDATE → UPDATE esplicito  

---

## 10 — PRINCIPI

✔ input non bloccante  
✔ suggerimenti non vincolanti  
✔ preview sempre presente  
✔ nessuna perdita informazione  
✔ decisione sempre utente  

---

## 11 — PRINCIPIO CRITICO

```text
interpretazione ≠ esecuzione
12 — ESPERIENZA

scrivo → vedo → correggo → confermo → fatto

13 — TEMPO TARGET

< 3 secondi

14 — NON OBIETTIVI

❌ automazione completa
❌ deduzione perfetta
❌ eliminazione utente

15 — STATO

✔ modello completo
✔ coerente con LOGOS
✔ pronto per U
# LOGOS UI WIREFRAME — v1.1

------------------------------------------------
1 — SCHERMATA COMPLETA
------------------------------------------------

┌──────────────────────────────┐
│ LOGOS                        │
│──────────────────────────────│
│ [ Scrivi un evento...      ] │  ← INPUT (sticky)
│                              │
│  • 50 euro benzina           │
│  • crea progetto Villa       │
│  • 1 ora sopralluogo         │
│──────────────────────────────│
│                              │
│        PREVIEW AREA          │
│                              │
│   (dinamica)                 │
│                              │
│──────────────────────────────│
│ [ Annulla ]   [ Conferma ]   │  ← ACTION BAR (sticky)
└──────────────────────────────┘

---

------------------------------------------------
2 — STATO EMPTY
------------------------------------------------

┌──────────────────────────────┐
│ [ Scrivi un evento...      ] │
│                              │
│  Prova a scrivere:           │
│  • 500 euro acconto          │
│  • vaccinazione Amanda       │
│  • crea progetto Adexima     │
│                              │
│                              │
│──────────────────────────────│
│ [ Annulla ]   [ Conferma ]   │
│                (disabled)    │
└──────────────────────────────┘

✔ nessuna preview  
✔ guida leggera  
✔ conferma disattiva  

---

------------------------------------------------
3 — STATO INPUT
------------------------------------------------

┌──────────────────────────────┐
│ Aggiungi 50 minuti sviluppo  │
│ Home sito Adexima            │
│──────────────────────────────│
│                              │
│   (analisi in corso...)      │
│                              │
│──────────────────────────────│
│ [ Annulla ]   [ Conferma ]   │
│                (disabled)    │
└──────────────────────────────┘

✔ micro-feedback  
✔ nessun blocco  

---

------------------------------------------------
4 — STATO PREVIEW (CHIARO)
------------------------------------------------

┌──────────────────────────────┐
│ Aggiungi 50 minuti sviluppo  │
│ Home sito Adexima            │
│──────────────────────────────│
│ Tipo:      [ Evento ▾ ]      │
│                              │
│ Importo:   [ 50 ] [ minuti ▾ ]│
│                              │
│ Progetto:  [ Adexima ▾ ]     │
│                              │
│ Entity:    [ + Aggiungi ▾ ]  │
│                              │
│ Nota:      [ sviluppo Home ] │
│                              │
│──────────────────────────────│
│ [ Annulla ]   [ Conferma ]   │
│                (ATTIVO)      │
└──────────────────────────────┘

✔ nessuna ambiguità  
✔ conferma immediata  

---

------------------------------------------------
5 — STATO PREVIEW (GUIDATO)
------------------------------------------------

Caso: campo mancante / ambiguità

┌──────────────────────────────┐
│ 150 euro crocchette          │
│──────────────────────────────│
│ ⚠ Seleziona un progetto      │
│                              │
│ Tipo:      [ Evento ▾ ]      │
│                              │
│ Importo:   [ 150 ] [ euro ▾ ]│
│                              │
│ Progetto:  [ + Aggiungi ▾ ]  │  ← vuoto
│                              │
│ Entity:    [ VARIE ▾ ]       │
│                              │
│──────────────────────────────│
│ [ Annulla ]   [ Conferma ]   │
│                (attenuato)   │
└──────────────────────────────┘

---

### DETTAGLI VISIVI

- banner guida sopra i campi  
- campo problematico evidenziato  
- conferma attenuata  

---

------------------------------------------------
6 — STATO MODIFICA
------------------------------------------------

Tap su "Progetto"

↓

┌──────────────────────────────┐
│ Progetto                     │
│──────────────────────────────│
│ • Cucciolata marzo           │
│ • Adexima                    │
│ • GENERIC                    │
│ • + Crea nuovo               │
└──────────────────────────────┘

---

Dopo selezione:

→ campo aggiornato  
→ warning sparisce  
→ conferma si attiva  

---

------------------------------------------------
7 — CREAZIONE INLINE
------------------------------------------------

Caso nuovo:

┌──────────────────────────────┐
│ Progetto: Villa Sierri +Nuovo│
│                              │
│ Crea nuovo progetto?         │
│                              │
│ [ Crea ]    [ Annulla ]      │
└──────────────────────────────┘

---

------------------------------------------------
8 — STATO AMBIGUITÀ
------------------------------------------------

┌──────────────────────────────┐
│ ⚠ Interpretazione non chiara │
│──────────────────────────────│
│ Scegli azione:               │
│                              │
│ ( ) Evento                   │
│ ( ) Crea progetto            │
│ ( ) Aggiorna progetto        │
│                              │
└──────────────────────────────┘

↓

Selezione → preview aggiornata

---

------------------------------------------------
9 — STATO CONFERMA
------------------------------------------------

### ATTIVO

[ Conferma ] (pieno, evidenziato)

---

### SOFT-DISABLED

[ Conferma ] (attenuato)

+ messaggio guida sopra:

⚠ seleziona il tipo  
⚠ scegli un project  
⚠ completa entity  

---

✔ si attiva automaticamente  
✔ nessun blocco definitivo  

---

------------------------------------------------
10 — FEEDBACK CONFERMA
------------------------------------------------

Tap Conferma:

→ vibrazione leggera  
→ toast: "Evento creato"  
→ reset UI  

---

------------------------------------------------
11 — FLUSSO COMPLETO
------------------------------------------------

scrivo
↓
preview
↓
(guida se serve)
↓
correggo
↓
confermo
↓
fine

---

------------------------------------------------
FINE DOCUMENTO
------------------------------------------------
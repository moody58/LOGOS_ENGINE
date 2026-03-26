# LOGOS UI MINIMAL — REAL v1.1

------------------------------------------------
1 — LAYOUT (MOBILE FIRST)
------------------------------------------------

Schermata unica, verticale, thumb-friendly.

[ INPUT BOX ]
(altezza ampia, sempre visibile, sticky in alto)

"Scrivi un evento, progetto o modifica…"

Sotto (micro-esempi tappabili):

• 50 euro benzina  
• 1 ora sopralluogo  
• crea progetto Villa Sierri  

----------------------------------------

[ PREVIEW CARD ]
(compare subito sotto l’input)

Card a tutta larghezza, padding ampio.

----------------------------------------

[ AZIONI ]
(bottom sticky)

[ Annulla ]     [ Conferma ]

---

Regole mobile:

- tutto tappabile con pollice
- niente hover
- target min 44px
- zero scroll orizzontale
- preview sempre sopra le azioni

---

------------------------------------------------
2 — EMPTY STATE
------------------------------------------------

Input + esempi.

Sotto input:

“Prova a scrivere…”

- 500 euro acconto Alfie Mario Rossi  
- crea progetto Villa Sierri  
- vaccinazione Amanda  

Tap su esempio → autocompila input.

---

------------------------------------------------
3 — PREVIEW (DOPO INPUT)
------------------------------------------------

Card con righe “field chips” cliccabili.

Esempio:

Tipo: [ Evento ▾ ]  
Importo: [ 50 ] [ minuti ▾ ]  
Progetto: [ Adexima ▾ ]  
Entity: [ — Aggiungi ▾ ]  
Nota: [ sviluppo Home sito ]

---

SEGNALI VISIVI (fondamentali):

✔ riconosciuto → bordo verde tenue  
⚠ incerto → bordo giallo + icona ⚠  
＋ nuovo (non in DB) → bordo blu + “Nuovo”  
○ vuoto → placeholder grigio “Aggiungi”

Ogni campo ha icona:

- ▼ dropdown
- ✎ editabile
- ＋ crea nuovo

---

------------------------------------------------
4 — MODIFICA INLINE
------------------------------------------------

Tap su campo → editor inline (no popup).

Esempi:

Progetto:

[ Adexima ▾ ]
↓ tap

Lista:

• Adexima  
• GENERIC  
• + Crea “Adexima” (se non esiste o fuzzy match)

---

Importo:

[ 50 ] → tap → tastiera numerica

Unità:

[ minuti ▾ ] → lista: minuti, ore, euro, kg…

---

------------------------------------------------
5 — CREAZIONE ENTITÀ / PROJECT (INLINE)
------------------------------------------------

Se il sistema rileva “nuovo”:

Progetto: [ Villa Sierri ＋Nuovo ]

Tap:

→ “Crea nuovo progetto: Villa Sierri?”

[ Crea ] [ Annulla ]

(oppure seleziona uno esistente simile)

---

Regola:

- suggerisci esistenti (fuzzy)
- offri creazione immediata
- nessun blocco

---

------------------------------------------------
6 — AMBIGUITÀ
------------------------------------------------

Se input ambiguo:

Banner in alto nella card:

“Interpretazione non chiara”

Opzioni (radio):

( ) Evento  
( ) Crea progetto  
( ) Aggiorna progetto

Selezione → aggiorna preview in tempo reale.

---

------------------------------------------------
7 — CONFERMA
------------------------------------------------

Bottom bar:

[ Annulla ]   [ Conferma ]

---

Tap Conferma:

→ haptic feedback leggero  
→ toast: “Creato” (1s)  
→ input si svuota  
→ preview scompare  

---

------------------------------------------------
8 — PRINCIPI UX
------------------------------------------------

- tutto visibile in 1 schermata  
- ogni campo è azione  
- ogni azione è reversibile  
- nessun blocco  

Principio:

```text
tocco → modifico → confermo
9 — NORMALIZZAZIONE (NON DISTRUTTIVA)

Mostra sempre:

Originale: “30 minuti”

(opzionale, sotto)
Normalizzato: “0.5 ore”

Regole:

non sostituisce l’originale
non blocca
solo suggerimento
10 — NON OBIETTIVI

❌ wizard
❌ multi-step
❌ obblighi
❌ deduzione perfetta

# LOGOS UI MINIMAL — REAL v1.2

# LOGOS UI MINIMAL — REAL v1.3

------------------------------------------------
NORMALIZZAZIONE
------------------------------------------------

- avviene sempre nel sistema
- NON è mostrata di default

Mostrata solo se:

- chiarisce l’input
- non genera ambiguità

---

------------------------------------------------
SUGGERIMENTI SEMANTICI
------------------------------------------------

Il sistema propone:

- project simili
- entity simili
- parole coerenti

---

Caratteristiche:

- non invasivi
- selezionabili
- ignorabili

---

Esempio:

Input: "adex"

Suggerimenti:

[ Adexima ]
[ + Crea "adex" ]

---

------------------------------------------------
RIDUZIONE ENTROPIA INPUT
------------------------------------------------

Il sistema aiuta a:

- evitare duplicati
- evitare errori di digitazione
- riconoscere termini simili

---

MA:

- non corregge automaticamente
- non impone scelte
- non blocca input

---

------------------------------------------------
CONFERMA — STATO GUIDATO
------------------------------------------------

Il pulsante "Conferma" può avere 2 stati:

---

### 1 — ATTIVO

Condizioni:

- interpretazione chiara  
- nessuna ambiguità critica  

Comportamento:

→ click immediato  
→ esecuzione diretta  

---

### 2 — SOFT-DISABLED (GUIDATO)

Condizioni:

- ambiguità non risolta  
- tipo azione non chiaro  
- campo rilevante mancante  

---

Visual:

- pulsante attenuato  
- messaggio guida visibile sopra o vicino  

Esempi:

⚠ seleziona il tipo di azione  
⚠ scegli un project  
⚠ completa il campo entity  

---

Comportamento:

- NON blocca definitivamente  
- si attiva automaticamente appena risolto  
- non richiede passaggi aggiuntivi  

---

------------------------------------------------
PRINCIPI
------------------------------------------------

✔ guida senza forzare  
✔ suggerisce senza imporre  
✔ evita errori silenziosi  
✔ mantiene flusso rapido  

---

------------------------------------------------
PRINCIPIO CHIAVE
------------------------------------------------

```text
l’utente capisce cosa fare senza pensarci
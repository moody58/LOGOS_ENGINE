############################################################
LOGOS — SCRIPT LAYER
File: LOGOS_SCRIPT_LAYER.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: Script Architecture
Status: Active
Version: v1.0
############################################################

---

## Prefazione

Questo documento definisce il livello SCRIPT del sistema LOGOS.

Il Script Layer è responsabile di:

• orchestrazione degli eventi
• automazione dei flussi
• connessione tra interfaccia e motore
• gestione asincrona delle operazioni

Rappresenta il livello operativo tra:

INPUT (utente / form / API)
e
ENGINE (processor / ledger)

---

## Indice

1 Ruolo del Script Layer
2 Architettura generale
3 Componenti script
4 Flussi operativi
5 Trigger e automazione
6 Principi di progettazione
7 Evoluzioni future

---

## 1 — Ruolo del Script Layer

Il Script Layer ha le seguenti responsabilità:

• ricevere input esterni
• trasformare input in eventi LOGOS
• attivare il processor
• gestire workflow asincroni
• aggiornare lo stato del sistema

Non contiene logica di business complessa
(ma la coordina).

---

## 2 — Architettura generale

INPUT
↓
SCRIPT LAYER
↓
RAW_INPUT
↓
PROCESSOR
↓
RAW_DATA

---

Il Script Layer NON bypassa mai il processor.

---

## 3 — Componenti script

INGESTION

File: ingestion.gs

Funzioni:

• LOGOS_ingestEvent(event)
Inserisce un evento in RAW_INPUT

• LOGOS_testInsert()
Funzione di test per validazione pipeline

---

PROCESSOR

File: LOGOS_PROCESSOR.gs

Funzioni principali:

• processRawInputV2()
Processa la coda eventi

Responsabilità:

• validazione progetto
• risoluzione entità
• gestione ENTITY_PENDING
• deduplicazione eventi
• scrittura ledger

---

ENTITY MANAGEMENT

Funzioni:

• LOGOS_commitSingleEntity(row)

Responsabilità:

• creazione entità da ENTITY_CONFIRMATION
• generazione ID
• scrittura su ENTITIES

---

TRIGGER

Funzione:

• onEdit(e)

Responsabilità:

• intercettare conferma entità
• attivare creazione entità
• attivare reprocessing automatico

---

## 4 — Flussi operativi

FLUSSO BASE

Input evento
↓
LOGOS_ingestEvent
↓
RAW_INPUT
↓
processRawInputV2
↓
RAW_DATA

---

FLUSSO CON ENTITÀ NON RISOLTA

Input evento
↓
RAW_INPUT
↓
Processor
↓
ENTITY_PENDING
↓
ENTITY_CONFIRMATION
↓
Confirm
↓
LOGOS_commitSingleEntity
↓
processRawInputV2
↓
RAW_DATA

---

FLUSSO EVENT-DRIVEN

Utente modifica foglio
↓
onEdit
↓
Trigger automatico
↓
Aggiornamento sistema

---

## 5 — Trigger e automazione

Trigger principale:

onEdit

Attivazione:

modifica manuale foglio

Condizione:

ENTITY_CONFIRMATION
colonna User_Confirmation = "Confirm"

---

Azioni:

1. creazione entità
2. reprocess eventi pending

---

Caratteristiche:

• asincrono
• automatico
• non richiede intervento manuale

---

## 6 — Principi di progettazione

Separazione responsabilità

• ingestion non valida
• processor non riceve input diretto
• trigger non contiene logica complessa

---

Idempotenza

• eventi duplicati evitati tramite fingerprint

---

Resilienza

• eventi non validi non bloccano sistema

---

Consistenza

• sistema coerente nel tempo
• backlog recuperato automaticamente

---

No bypass

• nessuna scrittura diretta nel ledger

---

## 7 — Evoluzioni future

• separazione ingestion API / UI
• introduzione queue esterna
• worker processor dedicato
• gestione trigger avanzata

---

Possibili estensioni:

• webhook
• integrazione Siri
• integrazione Baserow

---

---

## Note

Il Script Layer è una componente critica del sistema LOGOS
e deve essere mantenuto coerente con:

• LOGOS_ENGINE
• LOGOS_PROCESSOR
• LOGOS_SPEC

---

############################################################
LOGOS — SYSTEM ARCHITECTURE
File: LOGOS_ARCHITECTURE.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: Architecture Specification
Status: Consolidated Draft
############################################################

Prefazione
Questo documento descrive l'architettura del sistema LOGOS.
L'obiettivo è definire la struttura operativa del motore,
i livelli logici del sistema e le interazioni tra i componenti.

Il documento rappresenta l'allineamento tra il modello
concettuale definito nello SPEC e l'implementazione reale
del motore LOGOS.

Indice
1 Visione architetturale
2 Strati del sistema
3 Event ingestion pipeline
4 Event processing
5 Ledger layer
6 Dimension layer
7 Analytics layer
8 Runtime Google Stack
9 Evoluzione SQL / Baserow
10 Vincoli architetturali
11 Scalabilità
12 Note di versionamento

1 — Visione architetturale
LOGOS è progettato come Event Processing Engine
basato su un ledger append-only.

L'architettura generale del sistema è:

INPUT
↓
RAW_INPUT (event queue)
↓
PROCESSOR
↓
RAW_DATA (event ledger)
↓
DATASET ANALITICI
↓
DASHBOARD

Questa architettura consente separazione tra:
- ingestione dati
- processing
- storage eventi
- analisi

2 — Strati del sistema

INPUT LAYER
Raccoglie eventi provenienti da:
Form
API
Import dati
Input manuale

INGESTION LAYER
Gestisce la coda eventi tramite il foglio:

RAW_INPUT

PROCESSING LAYER
Il motore LOGOS_PROCESSOR valida e trasforma
gli eventi inseriti nella coda.

LEDGER LAYER
Gli eventi validati vengono scritti nel ledger:

RAW_DATA

ANALYTICS LAYER
Dataset aggregati derivati dal ledger.

VISUALIZATION LAYER
Dashboard e indicatori KPI.

3 — Event ingestion pipeline

Pipeline operativa:

INPUT
↓
RAW_INPUT
↓
Processor
↓
RAW_DATA

RAW_INPUT rappresenta la coda degli eventi
non ancora processati.

4 — Event processing

Il processor esegue le seguenti operazioni:

validazione progetto
risoluzione entità
normalizzazione input
generazione ID movimento
scrittura evento nel ledger

Il processor lavora su batch limitati
per garantire stabilità su Google Apps Script.

5 — Ledger layer

Il ledger principale è:

RAW_DATA

Il ledger segue il principio append-only.

Gli eventi non vengono modificati
né cancellati.

Le correzioni vengono registrate tramite
nuovi eventi.

6 — Dimension layer

Due tabelle dimensionali principali:

PROJECTS
ENTITIES

Entrambe supportano gerarchie parent-child.

Le dimensioni consentono aggregazioni
e analisi multidimensionali.

7 — Analytics layer

Dataset principali:

CONTROLLO_PROJECT
CONTROLLO_ENTITY

Dataset di dettaglio:

CONTROLLO_PROJECT_DETTAGLIO
CONTROLLO_ENTITY_DETTAGLIO

Questi dataset calcolano KPI come:

ore lavorate
spese
incassi
margine
numero eventi

8 — Runtime Google Stack

Implementazione attuale del sistema.

Google Sheets
+
Apps Script

Il processor viene attivato tramite:

onEdit trigger

Flusso operativo:

onEdit
↓
LOGOS_inputTrigger
↓
processRawInputV2

Nota:
questa modalità è considerata temporanea
ed è adottata nella fase prototipale.

9 — Evoluzione SQL / Baserow

In ambienti database il sistema potrà evolvere
verso un modello worker-based.

Architettura prevista:

Event Queue
↓
Worker Processor
↓
Event Ledger
↓
Analytics Engine

Questa architettura consente maggiore
scalabilità e gestione di volumi elevati.

10 — Vincoli architetturali

Il sistema deve rispettare:

ledger append-only
separazione input / ledger
dimension tables separate
pipeline event-driven

11 — Scalabilità

Il sistema su Google Sheets è progettato
per gestire fino a decine di migliaia di eventi.

Per volumi superiori è prevista la migrazione
verso database SQL.

12 — Note di versionamento

Documento generato tramite protocollo CQD.

Allineato con:

LOGOS_SPEC.md
implementazione kernel Apps Script
processor pipeline LOGOS.
############################################################
LOGOS — SYSTEM MAP
File: LOGOS_SYSTEM_MAP.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: System Map
Status: Consolidated
############################################################

Prefazione
Questo documento rappresenta la mappa sistemica del motore LOGOS.
Lo scopo è fornire una visione sintetica ma completa della struttura
del sistema, dei suoi componenti principali e delle relazioni tra di essi.

La System Map è pensata come documento di orientamento rapido:
un nuovo sviluppatore deve poter comprendere il funzionamento
complessivo di LOGOS leggendo questo documento.

Indice
1 Visione generale
2 Componenti del sistema
3 Pipeline eventi
4 Struttura dati
5 Motore operativo
6 Layer analitico
7 Stack tecnologico
8 Modello di istanza
9 Evoluzioni previste
10 Note di versionamento

1 — Visione generale

LOGOS è un Event Processing Engine basato su ledger.

Struttura logica:

INPUT
↓
EVENT QUEUE
↓
EVENT PROCESSOR
↓
EVENT LEDGER
↓
ANALYTICS LAYER
↓
DASHBOARD

Questo modello consente di separare chiaramente:

• ingestione eventi
• processamento
• archiviazione
• analisi

2 — Componenti del sistema

Componenti principali:

INPUT SOURCES
Form
API
Import storico
Input manuale

EVENT QUEUE
RAW_INPUT

PROCESSOR
LOGOS Processor (Apps Script)

EVENT LEDGER
RAW_DATA

DIMENSION TABLES
PROJECTS
ENTITIES

ANALYTICS DATASETS
CONTROLLO_PROJECT
CONTROLLO_ENTITY
CONTROLLO_PROJECT_DETTAGLIO
CONTROLLO_ENTITY_DETTAGLIO

SYSTEM LOG
SYSTEM_LOG

3 — Pipeline eventi

Pipeline operativa completa:

INPUT
↓
RAW_INPUT
↓
VALIDATION ENGINE
↓
ENTITY RESOLUTION
↓
EVENT FINGERPRINT
↓
LEDGER WRITE
↓
RAW_DATA

4 — Struttura dati

Elementi principali del modello:

EVENT
ENTITY
PROJECT

Relazioni:

EVENT → PROJECT
EVENT → ENTITY

Le dimensioni supportano gerarchie parent-child.

5 — Motore operativo

Il motore LOGOS è composto da:

Kernel
Apps Script runtime
event trigger system

Processor
processRawInputV2

Queue cursor
LOGOS_QUEUE_CURSOR

Il motore è progettato per funzionare
in modalità batch controllata.

6 — Layer analitico

I dataset analitici derivano dal ledger RAW_DATA.

Indicatori principali:

ore lavorate
spese
incassi
margine
numero eventi

Il layer analitico è separato dal ledger
per garantire integrità storica.

7 — Stack tecnologico

Implementazione attuale:

Google Sheets
Google Apps Script

Architettura futura prevista:

SQL Database
Event Worker
API ingestion layer

8 — Modello di istanza

LOGOS è progettato come template universale.

Ogni istanza è indipendente.

Esempi:

LOGOS_ADEXIMA
LOGOS_ASPRI
LOGOS_MAURIZIOLAB

Le istanze non condividono dati.

9 — Evoluzioni previste

Possibili evoluzioni del sistema:

API ingestion
event worker
message queue
database SQL

10 — Note di versionamento

Questo documento è stato generato durante
il consolidamento della documentazione LOGOS.

Documenti collegati:

LOGOS_SPEC.md
LOGOS_ARCHITECTURE.md
LOGOS_ENGINE.md
LOGOS_DATA_MODEL.md
LOGOS_CHANGELOG.md
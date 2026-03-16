############################################################ 

LOGOS --- DATABASE MAP File: LOGOS_DATABASE_MAP.md System: LOGOS Engine
Ecosystem: AIOS Document Type: Database Map Status: Draft (CQD
validated) \############################################################

# 1 --- SCOPO DEL DOCUMENTO

Questo documento descrive la struttura del database runtime LOGOS
implementato tramite Google Sheets.

La mappa ha tre obiettivi:

• fornire una visione sintetica della struttura dati • mostrare le
relazioni tra i fogli • facilitare la ricostruzione del database

Il documento rappresenta la mappa operativa del database LOGOS.

# 2 --- STRUTTURA GENERALE

Il database LOGOS segue una pipeline eventi composta da:

INPUT ↓ RAW_INPUT (event queue) ↓ PROCESSOR ↓ RAW_DATA (event ledger) ↓
ANALYTICS DATASETS ↓ DASHBOARD

# 3 --- FOGLI DEL DATABASE

INPUT / QUEUE RAW_INPUT

LEDGER RAW_DATA

DIMENSION TABLES PROJECTS ENTITIES

WORKFLOW SUPPORT ENTITY_CONFIRMATION SETTINGS

ANALYTICS CONTROLLO_PROJECT CONTROLLO_PROJECT_DETTAGLIO CONTROLLO_ENTITY
CONTROLLO_ENTITY_DETTAGLIO

SYSTEM SYSTEM_LOG

# 4 --- PIPELINE EVENTI

Pipeline operativa completa:

INPUT ↓ RAW_INPUT ↓ LOGOS_PROCESSOR ↓ RAW_DATA ↓ DATASET ANALITICI

# 5 --- EVENT QUEUE

Foglio:

RAW_INPUT

Contiene eventi non ancora processati.

Stati possibili:

NEW WRITTEN ERROR_PROJECT ENTITY_PENDING

Flussi possibili:

RAW_INPUT → RAW_DATA RAW_INPUT → ENTITY_CONFIRMATION RAW_INPUT →
ERROR_PROJECT

# 6 --- ENTITY RESOLUTION WORKFLOW

Quando l'entità non viene riconosciuta automaticamente, il sistema
utilizza il foglio:

ENTITY_CONFIRMATION

Pipeline:

RAW_INPUT ↓ ENTITY_PENDING ↓ ENTITY_CONFIRMATION ↓ ENTITIES

Questo foglio consente la conferma manuale delle entità.

# 7 --- EVENT LEDGER

Il ledger principale del sistema è:

RAW_DATA

Caratteristiche:

• append-only • nessuna modifica dei record esistenti • correzioni
tramite nuovi eventi

Relazioni:

RAW_DATA → PROJECTS RAW_DATA → ENTITIES

# 8 --- DIMENSION TABLES

Le dimensioni organizzative del sistema sono:

PROJECTS ENTITIES

Entrambe supportano gerarchie parent-child.

Relazioni:

EVENT → Project_ID EVENT → Entity_ID

# 9 --- ANALYTICS LAYER

I dataset analitici derivano dal ledger RAW_DATA.

Tabelle principali:

CONTROLLO_PROJECT CONTROLLO_ENTITY

Tabelle di dettaglio:

CONTROLLO_PROJECT_DETTAGLIO CONTROLLO_ENTITY_DETTAGLIO

Questi dataset calcolano KPI operativi.

# 10 --- SYSTEM TABLES

Foglio di log del sistema.

SYSTEM_LOG

Contiene eventi tecnici del motore.

Esempi:

INPUT_PROCESSOR_START INPUT_WRITTEN INPUT_ERROR_PROJECT
INPUT_ENTITY_PENDING

# 11 --- CONFIGURAZIONE SISTEMA

Foglio:

SETTINGS

Utilizzato per:

• parametri di sistema • liste di validazione • configurazioni runtime

# 12 --- RELAZIONI TRA FOGLI

Schema sintetico:

RAW_INPUT │ ├── PROJECTS ├── ENTITIES │ ├── ENTITY_CONFIRMATION │ └──
RAW_DATA │ ├── CONTROLLO_PROJECT ├── CONTROLLO_ENTITY ├──
CONTROLLO_PROJECT_DETTAGLIO └── CONTROLLO_ENTITY_DETTAGLIO

# 13 --- LIVELLI LOGICI DEL DATABASE

Livello 1 --- Input RAW_INPUT

Livello 2 --- Workflow ENTITY_CONFIRMATION SETTINGS

Livello 3 --- Ledger RAW_DATA

Livello 4 --- Dimensioni PROJECTS ENTITIES

Livello 5 --- Analytics CONTROLLO_PROJECT CONTROLLO_ENTITY
CONTROLLO_PROJECT_DETTAGLIO CONTROLLO_ENTITY_DETTAGLIO

Livello 6 --- Log SYSTEM_LOG

# 14 --- COMPATIBILITÀ SQL

Mapping previsto:

RAW_INPUT → ingestion_queue RAW_DATA → event_ledger PROJECTS → projects
ENTITIES → entities

Dataset analitici → materialized views

# VERSION HISTORY

v1.0 --- Creazione Database Map del sistema LOGOS.

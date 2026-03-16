# LOGOS System Index

Indice generale del sistema LOGOS.

Questo documento descrive i componenti principali del sistema.

---

# Core Scripts

## LOGOS_KERNEL.gs

Kernel del sistema.

Responsabilità:

- handler globale onEdit
- protezione chiavi primarie
- generazione Movement_ID
- trigger ENTITY_CONFIRMATION
- scrittura SYSTEM_LOG

Il kernel è il punto centrale di automazione.

---

## LOGOS_PROCESSOR.gs

Motore di ingestione eventi.

Funzione principale:

processRawInputV2()

Pipeline:

RAW_INPUT → PROCESSOR → RAW_DATA

Funzioni incluse:

validationEngine()  
entityResolutionEngine()  
buildProjectMap()  
buildEntityMap()  
generateEventFingerprint()  
LOGOS_generateMovementID()

Il processor è il worker del sistema.

---

## LOGOS_STRESS_TEST.gs

File utilizzato durante sviluppo.

Funzioni:

LOGOS_STP_generateEvents()  
LOGOS_STP_drainQueue()

Serve per:

- test prestazioni
- simulazione carico
- verifica stabilità

Non è necessario in produzione.

---

# Google Sheets Tables

Il database LOGOS è implementato tramite Google Sheets.

Tabelle principali:

RAW_INPUT  
RAW_DATA  
PROJECTS  
ENTITIES  
ENTITY_CONFIRMATION  
SYSTEM_LOG

---

# Data Flow

Event Source  
↓  
RAW_INPUT (queue)  
↓  
processRawInputV2()  
↓  
RAW_DATA (ledger)

---

# Design Principles

Principi architetturali del sistema.

## Event Driven

Gli eventi sono processati in modo asincrono tramite coda.

---

## Append Only Ledger

RAW_DATA è append-only.

Garantisce:

- tracciabilità
- integrità storica
- auditabilità

---

## Dimension Tables

Le dimension tables validano i dati.

PROJECTS  
ENTITIES

---

## Script Controlled Integrity

L'integrità del sistema è garantita da:

LOGOS_KERNEL.gs  
LOGOS_PROCESSOR.gs

Non da formule del foglio.

---

# Component Map

Processor Layer

LOGOS_PROCESSOR.gs

Automation Layer

LOGOS_KERNEL.gs

Data Layer

Google Sheets

---

# Future Evolution

Possibili evoluzioni:

Dashboard analytics  
API ingestion  
Baserow backend  
SQL ledger

L'architettura event-driven consente migrazione senza cambiare il modello logico.
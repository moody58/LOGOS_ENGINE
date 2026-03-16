
# LOGOS_RUNTIME_ARCHITECTURE.md

## System
LOGOS — Runtime Architecture

LOGOS is designed as an event‑driven ledger system implemented on Google Sheets with Apps Script runtime orchestration.

The runtime layer coordinates:

- event ingestion
- validation
- entity resolution
- ledger persistence
- analytics aggregation
- telemetry logging

---

# Runtime Overview

Pipeline:

RAW_INPUT → Processor → RAW_DATA → Analytics → Reporting

Where:

RAW_INPUT acts as the event queue  
Processor executes validation and ingestion logic  
RAW_DATA is the immutable ledger  
Analytics tables compute derived metrics

---

# Runtime Layers

## 1 — Input Layer

Tables:

RAW_INPUT

Purpose:

Acts as the ingestion queue for all external inputs.

Possible input sources:

- Manual entry
- Google Forms
- API integrations
- Siri shortcuts
- CSV imports
- automation scripts

Each record enters the system with:

Status = NEW

This status activates the ingestion pipeline.

---

## 2 — Processor Layer

Runtime component:

processRawInputV2()

Responsibilities:

- read events from RAW_INPUT
- validate project existence
- resolve entity identity
- generate event fingerprint
- prevent duplicate processing
- assign movement ID
- append ledger entry

Processing flow:

NEW → validation → entity resolution → fingerprint check → ledger append

---

## 3 — Ledger Layer

Table:

RAW_DATA

Characteristics:

- append‑only
- immutable historical record
- event sourced

Fields include:

Movement ID  
Timestamp  
Project_ID  
Entity_ID  
Tipo_Movimento  
Valore

Derived fields:

Unita  
Categoria_Derivata

Ledger guarantees:

- traceability
- chronological order
- auditability

---

## 4 — Dimension Tables

Tables:

PROJECTS  
ENTITIES  
SETTINGS

Purpose:

Provide reference data for the ledger.

PROJECTS:

Defines project hierarchy and metadata.

ENTITIES:

Defines people, organizations, or economic actors.

SETTINGS:

Holds system configuration values and dropdown sources.

---

## 5 — Workflow Layer

Table:

ENTITY_CONFIRMATION

Purpose:

Resolve unknown entities detected during ingestion.

Process:

RAW_DATA entry → entity not found → record created in ENTITY_CONFIRMATION

Possible states:

PENDING  
SUGGESTED  
CONFIRMED

Once confirmed the entity can be added to ENTITIES.

---

## 6 — Analytics Layer

Tables:

CONTROLLO_PROJECT_DETTAGLIO  
CONTROLLO_PROJECT  
CONTROLLO_ENTITY_DETTAGLIO  
CONTROLLO_ENTITY

Purpose:

Compute metrics derived from ledger events.

Typical metrics:

- hours
- expenses
- revenue
- margin
- movement count

Analytics uses:

MAP + LAMBDA + SUMIFS pattern.

This enables scalable aggregation inside Google Sheets.

---

## 7 — Telemetry Layer

Table:

SYSTEM_LOG

Responsible component:

Kernel

Purpose:

Record runtime system events.

Examples:

INPUT_TRIGGER  
INPUT_WRITTEN  
ENTITY_CONFIRMATION_REQUIRED  
PRIMARY_KEY_PROTECTION

Each log entry records:

timestamp  
event type  
sheet  
row  
user  
details

---

# Runtime Guarantees

The system guarantees:

Event integrity  
Traceable ingestion  
Deterministic processing  
Append‑only ledger  
Deterministic analytics views

---

# Operational Characteristics

LOGOS behaves similarly to a simplified:

Event Sourcing System

Key characteristics:

events stored as immutable ledger entries  
derived analytics computed from ledger  
entity resolution handled asynchronously

---

# Future Extensions

Possible runtime evolution:

- API ingestion endpoints
- webhook triggers
- external database mirror
- migration to SQL engine
- event streaming architecture


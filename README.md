# LOGOS_ENGINE — Legacy Event Processing System

---

## 1. Overview

LOGOS_ENGINE is the first operational implementation of the LOGOS system, developed using Google Sheets and Google Apps Script.

This version represents a prototype environment used to validate the core architecture before transitioning to a scalable system.

It implements an Event Processing Engine based on an append-only ledger, designed to track operational events such as time, expenses, revenue, and activities.

LOGOS_ENGINE played a fundamental role in validating the core model:

* event-driven ingestion
* separation between input and ledger
* dimensional data management (projects / entities)
* derived analytical pipeline

It was used as a real-world environment for testing and evolving the system before the transition to the current LOGOS architecture.

---

## 2. What is LOGOS_ENGINE

LOGOS_ENGINE is an event processing system that transforms raw inputs into structured records within an append-only ledger.

Main pipeline:

```
INPUT  
↓  
RAW_INPUT (event queue)  
↓  
PROCESSOR (Apps Script)  
↓  
RAW_DATA (event ledger)  
↓  
ANALYTICS DATASETS  
↓  
DASHBOARD  
```

The system follows a model that is:

* event-driven
* append-only
* non-destructive

Events are never modified or deleted — only appended.

---

## 3. System Context (AIOS)

LOGOS_ENGINE is part of the AIOS ecosystem.

Within AIOS:

* it represents the operational event management layer
* it implements the Event Ledger model
* it provides the data foundation for analytics and operational control

It has been used as:

* an experimentation environment
* a validation layer for the conceptual model
* a base for defining runtime behaviors

---

## 4. Technology Stack (Google Ecosystem)

The system is implemented using:

* **Google Sheets** → runtime database
* **Google Apps Script** → processing engine
* **onEdit triggers** → event-driven execution

Main components:

* RAW_INPUT → event queue
* RAW_DATA → append-only ledger
* PROJECTS / ENTITIES → dimension tables
* ENTITY_CONFIRMATION → asynchronous entity resolution
* SYSTEM_LOG → runtime logging

Core processor:

```
processRawInputV2()
```

Execution flow:

```
onEdit → trigger → event processing
```

---

## 5. How It Worked

### Event Ingestion

Events are inserted via:

* manual input
* forms
* API / imports

and stored in:

```
RAW_INPUT
```

Each event has a processing state:

* NEW
* ENTITY_PENDING
* PROJECT_PENDING
* WRITTEN
* ERROR_PROJECT
* DUPLICATE

---

### Processing

The processor reads events from the queue and:

1. validates the project
2. resolves the entity
3. manages pending states
4. prevents duplicates
5. writes to the ledger

Core function:

```
processRawInputV2()
```

---

### Entity Resolution

If an entity is not found:

```
RAW_INPUT  
↓  
ENTITY_PENDING  
↓  
ENTITY_CONFIRMATION  
↓  
user confirmation  
↓  
entity creation  
↓  
automatic reprocessing  
```

This ensures:

* no pipeline blocking
* asynchronous recovery of events

---

### Ledger

The main ledger is:

```
RAW_DATA
```

Characteristics:

* append-only
* immutable
* auditable

Corrections are handled through new events, not modifications.

---

### Analytics

Analytical datasets are derived from the ledger:

* CONTROLLO_PROJECT
* CONTROLLO_ENTITY

Computed using Google Sheets formulas.

---

## 6. Limitations

LOGOS_ENGINE has structural limitations due to the Google-based stack.

### Scalability

* constrained by Apps Script (timeouts, batch limits)
* suitable for tens of thousands of events
* not designed for high-load environments

---

### Architecture

* no dedicated workers
* dependency on onEdit triggers
* limited control over processing flow

---

### Data Management

* logic split between scripts and formulas
* difficult version control
* risk of range/formula inconsistencies

---

### Performance

* heavy use of ARRAYFORMULA, MAP, LAMBDA
* need to limit ranges (e.g. H2:H1002)
* performance degradation as data grows

---

### UX and Input

* partially unstructured input
* limited normalization
* reliance on manual confirmations

---

### Maintainability

* non-modular Apps Script code
* complex debugging
* strong coupling between UI and backend

---

## 7. Transition to LOGOS

The transition to the new LOGOS system was driven by:

* need for scalability
* clearer separation of layers
* introduction of a dedicated engine
* improved input system and UX
* data normalization

New architecture:

* frontend: Retool
* backend: Supabase
* logic: modular + evolving engine

Main differences:

| LOGOS_ENGINE            | LOGOS               |
| ----------------------- | ------------------- |
| Google Sheets           | Structured database |
| onEdit triggers         | Controlled flows    |
| Apps Script             | Modular logic       |
| Formula-based analytics | Engine + queries    |
| Limited scalability     | Scalable system     |

---

## 8. Current Status (Legacy)

LOGOS_ENGINE is now:

* a legacy system
* no longer actively developed
* maintained for reference

It remains useful for:

* understanding the original model
* tracing architectural evolution
* audit and transparency

---

## 9. Open Collaboration

This project is part of a broader experimental system (AIOS).

It is shared openly to explore ideas, structures, and workflows.

If you find this interesting or plan to build upon it, feel free to reach out — I’d be glad to connect.

---

## AIOS Ecosystem

LOGOS_ENGINE is part of the AIOS system as a legacy implementation.

Related components:

- **AIOS_CORE** [https://github.com/moody58/aios_core] — defines the system methodology  
- **LOGOS** [https://github.com/moody58/logos] — current generation data engine  

This repository represents an earlier stage in the system evolution.

---

## Final Note

This project is part of the AIOS ecosystem.

LOGOS_ENGINE represents an earlier implementation of the LOGOS system.

It is shared for transparency, reference, and historical context.

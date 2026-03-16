# LOGOS Runtime Architecture

## Overview

LOGOS è un motore di processamento eventi implementato su Google Sheets + Google Apps Script.

Il sistema segue un modello **event-driven ledger**.

Pipeline principale:

RAW_INPUT → PROCESSOR → RAW_DATA

Tabelle di dimensione:

- PROJECTS
- ENTITIES

Tabelle di supporto:

- ENTITY_CONFIRMATION
- SYSTEM_LOG

Il processor legge gli eventi dalla coda, valida i dati, risolve le entità e scrive movimenti nel ledger.

---

# Runtime Flow

## Event Ingestion

Eventi possono arrivare da:

- Google Forms
- API
- Import CSV
- Automazioni
- Inserimento manuale (fase sviluppo)

Gli eventi vengono scritti nella tabella:

RAW_INPUT

Campo chiave:

Status

Valori possibili:

NEW  
WRITTEN  
ERROR_PROJECT  
ENTITY_PENDING  
DUPLICATE

---

# Processor Engine

Script principale:

processRawInputV2()

Responsabilità:

1 Lettura eventi NEW  
2 Validazione progetto  
3 Risoluzione entità  
4 Deduplicazione evento  
5 Scrittura ledger  
6 Aggiornamento status

---

# RAW_DATA — Ledger

RAW_DATA è un ledger append-only.

Campi principali:

Movement_ID  
Timestamp  
Event_Date  
Subject  
Project_ID  
Entity_ID  
Movement_Type  
Amount  
Causale  
Reference_ID  
Source  
Payment_Method  
Notes

Regole ledger:

- Append only
- Nessuna modifica storica
- Nessuna cancellazione

Questo garantisce integrità contabile.

---

# SYSTEM_LOG

SYSTEM_LOG registra eventi runtime.

Generato dalla funzione:

logEvent()

Eventi tracciati:

INPUT_PROCESSOR_START  
INPUT_WRITTEN  
INPUT_BATCH_COMPLETED  
INPUT_ERROR_PROJECT  
INPUT_ENTITY_PENDING  
INPUT_DUPLICATE_SKIPPED

Il log è fondamentale per debugging e auditing.

---

# ENTITY_CONFIRMATION

Quando viene rilevata un'entità sconosciuta:

1 Il processor crea una riga in ENTITY_CONFIRMATION  
2 Lo stato diventa PENDING  
3 L'utente valida l'entità  
4 L'entità viene creata in ENTITIES

Questo evita errori di inserimento automatico.

---

# Dimension Tables

## PROJECTS

Contiene l'elenco dei progetti.

Campi principali:

Project_ID  
Project_Name  
Parent_Project_ID  
Project_Type  
Status  
Start_Date  
End_Date  
Created_Timestamp

Il processor usa PROJECTS per validare gli eventi.

---

## ENTITIES

Contiene soggetti del sistema:

- clienti
- fornitori
- persone
- organizzazioni

Campi principali:

Entity_ID  
Entity_Type  
First_Name  
Last_Name  
Display_Name  
Parent_Entity_ID  
Created_Timestamp  
Modified_Timestamp

Il processor costruisce una mappa per risolvere:

First_Name  
Display_Name

---

# Processing Model

LOGOS utilizza un modello di processamento batch.

Ogni run del processor:

- legge nuovi eventi
- elabora fino a MAX_EVENTS_PER_RUN
- aggiorna lo status

Questo evita timeout di Apps Script.

---

# Scalabilità

Prestazioni osservate:

~1000 eventi / minuto

Il sistema può gestire facilmente:

30k – 80k movimenti ledger

Prima che sia necessario migrare verso:

- SQL
- Baserow
- Backend dedicato

---

# Architettura concettuale

Event Queue:

RAW_INPUT

Worker:

PROCESSOR

Ledger:

RAW_DATA

Dimension:

PROJECTS  
ENTITIES

Audit:

SYSTEM_LOG
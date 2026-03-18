\############################################################

LOGOS — CHANGELOG

File: LOGOS\_CHANGELOG.md

System: LOGOS Engine

Ecosystem: AIOS

Document Type: Architectural Changelog

Status: Active

Version: v2.0

\############################################################



\---



\## Prefazione



Questo documento registra l’evoluzione architetturale del sistema LOGOS.



Il changelog documenta:



• decisioni strutturali

• evoluzioni del modello dati

• modifiche comportamentali del motore

• introduzione di nuovi layer operativi



Il documento rappresenta la memoria storica ufficiale

del comportamento del sistema.



\---



\## Indice



1 Principi del changelog

2 Decisioni architetturali

3 Evoluzioni del sistema

4 Incidenti e correzioni

5 Introduzione Script Layer

6 Migrazioni tecnologiche

7 Note di versionamento



\---



\## 1 — Principi del changelog



Il changelog LOGOS segue un modello architetturale.



Categorie:



• Decisione

• Evoluzione

• Incidente

• Migrazione



\---



\## 2 — Decisioni architetturali



Event Ledger Architecture

LOGOS utilizza un modello append-only.



Separazione Input / Ledger

RAW\_INPUT → RAW\_DATA



Dimension Tables indipendenti

PROJECTS

ENTITIES



Header Mapping dinamico

Identificazione colonne per nome



Event Fingerprint

Controllo duplicati batch



\---



Decisione — Gestione asincrona entità



È introdotto un modello asincrono per la gestione delle entità:



NEW

↓

ENTITY\_PENDING

↓

ENTITY\_CONFIRMATION

↓

ENTITY CREATION

↓

REPROCESS

↓

WRITTEN



\---



Decisione — Reprocessing automatico



Gli eventi in stato ENTITY\_PENDING vengono automaticamente

rieseguiti dopo la creazione dell’entità.



Il processor accetta ora stati:



• NEW

• ENTITY\_PENDING



\---



Decisione — Sistema Event-Driven



Il sistema è evoluto verso un modello event-driven.



Trigger:



onEdit

↓

LOGOS\_commitSingleEntity

↓

processRawInputV2



\---



Decisione — UX-first



L’architettura privilegia la semplicità di input rispetto

alla rigidità del modello dati.



\---



Decisione — Script Layer



Introduzione di un layer esplicito di automazione:



• ingestion

• processor

• entity commit

• trigger



\---



\---



\## 3 — Evoluzioni del sistema



Evoluzione — Pipeline avanzata



INPUT

↓

RAW\_INPUT

↓

VALIDATION\_ENGINE

↓

ENTITY\_RESOLUTION

↓

EVENT\_FINGERPRINT

↓

LEDGER\_APPEND

↓

RAW\_DATA



\---



Evoluzione — Entity Confirmation Layer



Introduzione del foglio ENTITY\_CONFIRMATION come layer intermedio

per la validazione delle entità.



Caratteristiche:



• gestione asincrona

• input utente guidato

• prevenzione blocchi pipeline



\---



Evoluzione — Auto Entity Creation



Il sistema crea automaticamente nuove entità dopo conferma utente.



\---



Evoluzione — Backlog Recovery



Gli eventi pendenti vengono recuperati automaticamente

senza intervento manuale.



\---



Evoluzione — Suggestion Engine (base)



Introduzione di suggerimenti entità:



• match esatto

• match fuzzy base (includes)



\---



Evoluzione — Rimozione dipendenza formule



Parte della logica è stata spostata da formule Google Sheets

a script per migliorare controllo e coerenza.



Applicato a:



• ENTITY\_CONFIRMATION



\---



\---



\## 4 — Incidenti e correzioni



Incidente — Processor non reprocessava ENTITY\_PENDING



Risolto introducendo supporto multi-stato nel filtro.



\---



Incidente — Duplicazione ENTITY\_CONFIRMATION



Risolto introducendo controllo su input già esistente.



\---



Incidente — Scrittura colonne ENTITIES non coerente



Risolto definendo mapping esplicito appendRow.



\---



Incidente — Range invalidi RAW\_INPUT



Risolto con controlli su startRow e numRows.



\---



Incidente — Parentesi mancanti nel processor



Errore sintattico corretto nel blocco ENTITY\_RESOLUTION.



\---



\---



\## 5 — Introduzione Script Layer



È formalizzato il layer script come componente del sistema.



Componenti:



• ingestion.gs

• LOGOS\_PROCESSOR.gs

• trigger onEdit

• LOGOS\_commitSingleEntity



Funzione:



• orchestrazione eventi

• automazione flussi

• integrazione tra UI e engine



\---



\---



\## 6 — Migrazioni tecnologiche



Implementazione attuale



Google Sheets

Google Apps Script



\---



Evoluzione prevista



Event Queue

↓

Worker Processor

↓

Event Ledger

↓

Analytics Engine



Compatibile con:



• SQL

• Baserow



\---



\---



\## 7 — Note di versionamento



Versione v2.0



Aggiornamento maggiore:



• introduzione gestione asincrona entità

• reprocessing automatico

• sistema event-driven

• introduzione Script Layer



\---



Documenti correlati



LOGOS\_SPEC.md

LOGOS\_ARCHITECTURE.md

LOGOS\_ENGINE.md

LOGOS\_DATA\_MODEL.md

LOGOS\_PROCESSOR.md

LOGOS\_SCRIPT\_LAYER.md



\---




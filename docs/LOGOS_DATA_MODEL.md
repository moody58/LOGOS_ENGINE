############################################################
LOGOS — DATA MODEL
File: LOGOS_DATA_MODEL.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: Data Model Specification
Status: Consolidated Draft
############################################################

Prefazione
Questo documento definisce il modello dati ufficiale del sistema LOGOS.
Il modello dati rappresenta la struttura logica utilizzata per registrare
eventi operativi, gestire dimensioni organizzative e generare dataset
analitici.

Il modello è progettato per essere compatibile sia con Google Sheets
sia con database relazionali (SQL / Baserow).

Indice
1 Principi del modello dati
2 Event Ledger
3 Input Queue
4 Dimension Tables
5 Analytics Layer
6 Campi derivati
7 Compatibilità SQL
8 Vincoli di integrità
9 Note di versionamento

1 — Principi del modello dati

Il modello dati LOGOS segue alcuni principi fondamentali:

ledger append-only
separazione tra input e ledger
dimension tables indipendenti
analytics layer derivato

Il ledger rappresenta la fonte primaria dei dati.

2 — Event Ledger

Il ledger principale del sistema è:

RAW_DATA

Questo foglio contiene tutti gli eventi validati dal processor.

Campi principali:

ID_Movimento
Timestamp
Data_Evento
Soggetto_Input
Project_ID
Entity_ID
Tipo_Movimento
Valore
Unità
Causale
Categoria_Derivata
Riferimento_ID
Fonte
Metodo_Pagamento
Note

Regole principali:

gli eventi sono append-only
le modifiche non sovrascrivono eventi esistenti
le correzioni generano nuovi eventi

3 — Input Queue

Gli eventi vengono inizialmente inseriti nella coda:

RAW_INPUT

Questa tabella rappresenta eventi non ancora processati.

Campi principali:

Timestamp_Form
Project_Name
Tipo_Movimento
Valore
Data_Evento
Causale
Riferimento_ID
Metodo_Pagamento
Note
Soggetto
Source
Status

Status indica lo stato dell'evento nella pipeline.

Valori possibili:

NEW
WRITTEN
ERROR_PROJECT
ENTITY_PENDING

4 — Dimension Tables

Il sistema utilizza due dimensioni principali:

PROJECTS
ENTITIES

PROJECTS
Campi principali:

Project_ID
Display_Name
Parent_Project
Created_At

ENTITIES
Campi principali:

Entity_ID
Display_Name
Entity_Type
Parent_Entity
Created_At

Entrambe le dimensioni supportano gerarchie parent-child.

5 — Analytics Layer

I dataset analitici sono derivati dal ledger RAW_DATA.

Tabelle principali:

CONTROLLO_PROJECT
CONTROLLO_ENTITY

Tabelle di dettaglio:

CONTROLLO_PROJECT_DETTAGLIO
CONTROLLO_ENTITY_DETTAGLIO

Queste tabelle generano KPI operativi come:

totale ore
totale spese
totale incassi
margine
numero eventi

6 — Campi derivati

Alcuni campi nel sistema non fanno parte del ledger logico,
ma sono derivazioni calcolate.

Esempi:

Unità
Categoria_Derivata

Questi campi vengono generati tramite formule (ARRAYFORMULA)
nell'implementazione Google Sheets.

In un database SQL questi campi diventerebbero:

computed fields
view columns

7 — Compatibilità SQL

Il modello dati LOGOS è progettato per essere compatibile
con database relazionali.

Mapping previsto:

RAW_INPUT → ingestion queue
RAW_DATA → event ledger table
PROJECTS → dimension table
ENTITIES → dimension table

I dataset analitici diventano:

materialized views
analytics tables

8 — Vincoli di integrità

Il sistema mantiene integrità tramite:

ID univoci
validation engine
entity resolution
log eventi

Le chiavi primarie sono protette nel runtime Apps Script.

9 — Note di versionamento

Documento generato tramite protocollo CQD.

Allineato con:

LOGOS_SPEC.md
LOGOS_ARCHITECTURE.md
LOGOS_ENGINE.md
implementazione attuale del database LOGOS.
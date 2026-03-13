FILE NAME

0601_LOGOS_SPEC_v1.2

DOCUMENT TYPE

Technical Specification

SYSTEM

LOGOS Engine

ECOSYSTEM

AIOS

LANGUAGE

Italiano (terminologia tecnica inglese dove necessario)

STATO

Stabile

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

1\. Introduzione

Il sistema LOGOS è il motore dati utilizzato dai progetti
dell'ecosistema AIOS.

LOGOS nasce dall'evoluzione del sistema CORE sviluppato inizialmente
come database operativo per tracciare attività, tempo e movimenti
economici tramite fogli di calcolo strutturati.

Durante lo sviluppo il sistema è stato trasformato in un motore eventi
replicabile, progettato per mantenere integrità dei dati e separazione
tra registrazione eventi e dataset analitici.

LOGOS adotta un modello event-driven: ogni attività o transazione viene
registrata come evento in un registro centrale.

I dataset analitici vengono generati tramite aggregazioni sui dati del
registro eventi.

Questo documento descrive architettura, modello dati, automazioni e
principi di governance del sistema.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

2\. Content Map del Sistema

LOGOS_ENGINE

│

├── RAW_INPUT

├── RAW_DATA

├── PROJECTS

├── ENTITIES

├── SETTINGS

│

├── CONTROLLO_PROJECT

├── CONTROLLO_PROJECT_DETTAGLIO

├── CONTROLLO_ENTITY

└── CONTROLLO_ENTITY_DETTAGLIO

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

3\. Architettura del sistema

LOGOS Engine è un sistema dati autonomo utilizzato dai progetti
dell'ecosistema AIOS.

Il sistema è implementato come database operativo su Google Sheets.

LOGOS è separato dal sistema AIOS.

AIOS svolge funzione di regia e coordinamento metodologico ma non
modifica direttamente il database LOGOS.

Questa separazione garantisce stabilità del motore dati e indipendenza
tra sistemi.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

4\. Posizionamento del sistema

Il database operativo LOGOS_ENGINE è ospitato su Google Drive.

La struttura principale è la seguente.

LOGOS_ENGINE

│

├── LOGOS_TEMPLATE

│

├── LOGOS_ADEXIMA

├── LOGOS_ASPRI

├── LOGOS_MAURIZIOLAB

│

└── ARCHIVE

LOGOS_TEMPLATE rappresenta il template universale del sistema.

Ogni progetto utilizza una propria istanza derivata dal template.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

5\. Principi architetturali

Il sistema LOGOS è progettato secondo i seguenti principi.

Sistema transazionale leggero

Event-driven model

Separazione tra registrazione eventi e dataset analitici

Integrità dati tramite automazioni

Template replicabile tra progetti

Configurazione centralizzata tramite foglio SETTINGS

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

6\. Contesto di sviluppo

Il sistema è stato sviluppato per risolvere problemi tipici dei sistemi
informativi basati su fogli di calcolo:

assenza di integrità dati

difficoltà di tracciamento delle modifiche

scarsa replicabilità dei modelli

L\'evoluzione dal sistema CORE al motore LOGOS ha introdotto una
struttura più rigorosa:

registro eventi centrale

dataset analitici derivati

automazioni per integrità dati

template replicabile per progetti multipli.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

7\. Database LOGOS

Il database LOGOS è implementato su Google Sheets.

I fogli principali del database sono:

PROJECTS

ENTITIES

RAW_INPUT

RAW_DATA

SETTINGS

I dataset derivati includono:

CONTROLLO_PROJECT

CONTROLLO_PROJECT_DETTAGLIO

CONTROLLO_ENTITY

CONTROLLO_ENTITY_DETTAGLIO

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

8\. Registro configurazione SETTINGS

Il foglio SETTINGS contiene i valori utilizzati dal sistema per dropdown
e configurazioni.

Tra questi:

Project_Type

Entity_Type

Movement_Type

Status

Il foglio rappresenta il registro di configurazione del sistema e
permette di mantenere il template replicabile tra progetti.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

9\. Modello dati

Il sistema distingue tre entità principali.

Progetti

unità operative del sistema.

Entità

soggetti o organizzazioni coinvolti negli eventi.

Eventi

registrazioni operative contenenti valori misurabili.

Le relazioni tra questi elementi permettono di generare dataset
aggregati e analisi.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

10\. Identificatori

Gli identificatori principali sono:

Project_ID

Entity_ID

Movement_ID

Gli identificatori sono generati automaticamente tramite script e non
devono essere modificati manualmente.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

11\. Automazione

Il sistema utilizza Google Apps Script per automatizzare operazioni
chiave.

Il trigger principale è:

onEdit(e)

Le principali funzioni automatizzate includono:

generazione ID

gestione timestamp

protezione chiavi primarie

blocco auto-parent

blocco cancellazione progetti con movimenti associati

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

12\. Struttura degli eventi

Ogni evento registrato nel sistema è classificato tramite il campo
Tipo_Movimento.

Tipologie principali:

Tempo

Spesa

Incasso

Evento

Unità di misura associate:

Tempo → H

Spesa → €

Incasso → €

Evento → nessuna unità

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

13\. Dataset e metriche

I dataset derivati aggregano informazioni per progetto ed entità.

Metriche principali:

Ore

Spese

Incassi

Numero movimenti

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

14\. Modularità del sistema

LOGOS è progettato come template universale replicabile.

Il sistema può essere duplicato per creare nuove istanze dedicate ai
singoli progetti.

Esempi di istanze previste:

LOGOS_ADEXIMA

LOGOS_ASPRI

LOGOS_MAURIZIOLAB

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

15\. Governance del sistema

Il sistema utilizza un modello di versionamento esplicito.

Ogni modifica strutturale comporta incremento della versione del
sistema.

Le modifiche vengono registrate nel documento LOGOS_CHANGELOG.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

DOCUMENT METADATA

File Name

0601_LOGOS_SPEC_v1.2

Sistema

LOGOS Engine

Ecosistema

AIOS

Lingua

Italiano

Stato

Stabile

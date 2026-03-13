FILE NAME

0604_LOGOS_CHANGELOG_v1.1

DOCUMENT TYPE

System Changelog

SYSTEM

LOGOS Engine

ECOSYSTEM

AIOS

LANGUAGE

Italiano (terminologia tecnica inglese dove necessario)

STATO

Stabile

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

1\. Scopo del documento

Il LOGOS_CHANGELOG registra tutte le modifiche strutturali apportate al
sistema LOGOS Engine.

Il documento ha lo scopo di garantire tracciabilità delle modifiche nel
tempo e mantenere allineati:

struttura del database

automazioni

documentazione tecnica

Ogni modifica strutturale al sistema deve essere registrata nel presente
documento.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

2\. Ambito delle modifiche tracciate

Il changelog registra esclusivamente modifiche strutturali del sistema.

Le modifiche tracciate includono:

modifica dello schema database

modifica delle relazioni tra tabelle

modifica delle automazioni Apps Script

modifica dei dataset analitici

modifica dei documenti SPEC e MODEL

Non vengono registrate modifiche operative ai dati.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

3\. Regole di versionamento

Il sistema LOGOS utilizza un versionamento semantico semplificato.

Formato versione

MAJOR.MINOR

Incremento MAJOR

modifica struttura database

modifica architettura sistema

modifica logica automazioni

Incremento MINOR

aggiunta campi non critici

miglioramenti dataset

ottimizzazioni formule

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

4\. Registro modifiche

Versione

1.0

Tipo modifica

Creazione sistema

Descrizione

Definizione iniziale del sistema LOGOS Engine.

Creazione documentazione ufficiale:

0601_LOGOS_SPEC_v1.1

0602_LOGOS_NOTEBOOK_v1.0

0603_LOGOS_MODEL_v1.0

Definizione struttura database:

PROJECTS

ENTITIES

RAW_INPUT

RAW_DATA

SETTINGS

Creazione dataset analitici:

CONTROLLO_PROJECT

CONTROLLO_PROJECT_DETTAGLIO

CONTROLLO_ENTITY

CONTROLLO_ENTITY_DETTAGLIO

Implementazione automazioni Apps Script:

generazione ID automatici

protezione chiavi primarie

blocco cancellazione progetti con movimenti

blocco auto-parent

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

Versione

1.1

Tipo modifica

Aggiornamento architettura sistema

Descrizione

Separazione architetturale tra AIOS e LOGOS Engine.

LOGOS Engine viene definito come motore dati autonomo ospitato su Google
Drive.

Introduzione della struttura LOGOS_ENGINE con template replicabile.

Definizione delle istanze progetto:

LOGOS_ADEXIMA

LOGOS_ASPRI

LOGOS_MAURIZIOLAB

Aggiornamento del documento LOGOS_SPEC.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

5\. Procedura di aggiornamento del sistema

Quando viene modificato il sistema LOGOS devono essere aggiornati i
seguenti elementi:

database operativo LOGOS_ENGINE

documento LOGOS_MODEL

documento LOGOS_SPEC

registro LOGOS_CHANGELOG

La documentazione deve essere sempre allineata alla struttura reale del
database.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

6\. Procedura CQD documentale

Il controllo qualità documentale (CQD) garantisce coerenza tra documenti
e sistema.

Il controllo deve verificare:

coerenza tra SPEC e MODEL

coerenza tra MODEL e database operativo

coerenza tra automazioni e schema dati

completezza delle sezioni

presenza delle appendici

corretta indicazione della versione del documento

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

7\. Integrazione con AIOS

Il sistema LOGOS fa parte dell'ecosistema AIOS.

AIOS svolge funzione di coordinamento metodologico ma non modifica
direttamente il database LOGOS.

Eventuali modifiche architetturali devono essere registrate nel radar
evolutivo del sistema.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

DOCUMENT METADATA

File Name

0604_LOGOS_CHANGELOG_v1.1

Sistema

LOGOS Engine

Ecosistema

AIOS

Lingua

Italiano

Stato

Stabile

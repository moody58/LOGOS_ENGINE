0605_LOGOS_ENGINE_SYSTEM_MAP_v1.0

Sistema: LOGOS ENGINE

Tipo documento: Architettura motore dati

Area: 06_LOGOS / 0600_ENGINE

Stato: Design Freeze

Versione: v1.0

Data: 2026

Sistema di riferimento: AIOS

# 1 --- SCOPO DEL DOCUMENTO

Questo documento descrive l'architettura del motore dati **LOGOS
ENGINE** all'interno del metasistema AIOS.

Il documento ha lo scopo di:

• definire l'architettura logica del motore dati\
• descrivere la pipeline universale di ingestione dati\
• definire il modello eventi del sistema\
• descrivere la struttura del database LOGOS\
• definire il lifecycle degli eventi\
• stabilire i principi architetturali del motore\
• garantire coerenza con il metasistema AIOS

Il documento rappresenta la **System Map operativa del motore LOGOS**.

# 2 --- POSIZIONE NEL METASISTEMA

Il sistema AIOS è composto da tre livelli principali.

AIOS

metodo e governance del sistema

↓

LOGOS ENGINE

motore dati operativo

↓

ISTANZE PROGETTO

database dei singoli progetti

AIOS definisce:

• metodo\
• protocolli\
• governance dei progetti

LOGOS ENGINE gestisce:

• registrazione eventi\
• dataset operativi\
• metriche\
• analisi dati

I progetti rappresentano il livello operativo.

# 3 --- PRINCIPIO ARCHITETTURALE

LOGOS ENGINE è progettato secondo i seguenti principi.

## 3.1 Event Driven System

Il sistema registra **eventi** e non stati.

Esempio:

evento:

2 ore lavoro progetto Adexima

non stato:

totale ore progetto

Gli stati vengono ricostruiti tramite aggregazioni.

## 3.2 Append-Only Ledger

Gli eventi non vengono modificati o cancellati.

Le eventuali correzioni avvengono tramite:

eventi di rettifica

Questo garantisce:

• audit completo\
• storico immutabile\
• ricostruzione del sistema nel tempo

## 3.3 Separazione Metodo / Dati

AIOS governa il sistema.

LOGOS registra dati.

Questa separazione garantisce:

• stabilità metodologica\
• indipendenza dell'infrastruttura dati\
• replicabilità dei progetti

## 3.4 Architettura Pipeline

Il sistema utilizza una pipeline dati strutturata.

INPUT

↓

STAGING

↓

PROCESSING

↓

EVENT STORE

↓

DATASETS

↓

METRICS

↓

ANALYSIS

Questo modello garantisce:

• tracciabilità\
• robustezza operativa\
• scalabilità del sistema

# 4 --- PIPELINE DATI UNIVERSALE

La pipeline operativa di LOGOS ENGINE è la seguente.

## 4.1 INPUT LAYER

Fonti di input previste:

Google Form

Siri (integrazione futura)

inserimento manuale controllato

L'input rappresenta la registrazione di un evento operativo.

## 4.2 STAGING LAYER

Foglio:

RAW_INPUT

Funzione:

• ricezione input grezzo\
• staging temporaneo dei dati\
• verifica validità input

Campi principali:

Timestamp

Project_Name

Tipo_Movimento

Valore

Soggetto

Causale

Metodo_Pagamento

Riferimento_ID

PROCESS_STATUS

ERROR_MESSAGE

## 4.3 PROCESSING LAYER

Il processing viene eseguito tramite **Apps Script**.

Funzioni principali:

validazione logica input

normalizzazione dati

risoluzione entità

generazione ID evento

scrittura evento nel database

## 4.4 ENTITY RESOLUTION

Il sistema evita duplicazioni di entità tramite tre livelli.

Livello 1:

match esatto

Livello 2:

similarity match

Livello 3:

conferma utente

In caso di ambiguità viene utilizzato il foglio:

ENTITY_CONFIRMATION

## 4.5 EVENT STORE

Foglio principale:

RAW_DATA

Questo foglio rappresenta il **registro eventi del sistema**.

Ogni riga rappresenta un evento.

# 5 --- MODELLO EVENTO

Schema evento LOGOS.

User_Event_ID

ID_Movimento

Timestamp

Project_ID

Entity_ID

Tipo_Movimento

Valore

Unità

Causale

Metodo_Pagamento

Riferimento_ID

Note

## 5.1 Identificatori evento

Il sistema utilizza due identificatori.

### ID tecnico

EVT-YYYYMMDD-HHMMSS-RND

Serve per audit e unicità tecnica.

### ID utente

E-XXXX

Serve per riferimento umano.

Esempio:

E-1045

# 6 --- LIFECYCLE EVENTI

Gli eventi nello staging seguono il seguente ciclo.

PENDING

PROCESSING

WAITING_CONFIRMATION

PROCESSED

ERROR

ARCHIVED

## PENDING

Evento ricevuto nello staging.

## PROCESSING

Evento in fase di elaborazione.

## WAITING_CONFIRMATION

Evento sospeso per ambiguità entità.

## PROCESSED

Evento registrato nel database.

## ERROR

Errore di sistema o validazione.

## ARCHIVED

Evento storico consolidato.

# 7 --- STRUTTURA DATABASE LOGOS

Il database LOGOS Template include i seguenti fogli.

## Configurazione

PROJECTS

ENTITIES

SETTINGS

## Staging

RAW_INPUT

## Validazione

ENTITY_CONFIRMATION

## Event Store

RAW_DATA

## Monitoring

SYSTEM_LOG

## Dataset analitici

CONTROLLO_PROJECT

CONTROLLO_ENTITY

CONTROLLO_PROJECT_DETTAGLIO

CONTROLLO_ENTITY_DETTAGLIO

# 8 --- MONITORING DEL SISTEMA

LOGOS include un sistema di osservabilità.

Componenti:

SYSTEM_LOG

email alerts

feedback form

Il monitoring registra:

• errori di sistema\
• creazione nuove entità\
• creazione nuovi progetti\
• eventi critici

Questo sistema permette audit e debug del motore dati.

# 9 --- USER EXPERIENCE

Il sistema include miglioramenti UX per ridurre errori umani.

Funzionalità previste:

autocomplete campi

messaggi guida

messaggi errore chiari

messaggi conferma evento

feedback form utente

Messaggi di conferma includono sempre:

• descrizione evento\
• contesto progetto\
• riferimento evento

# 10 --- DASHBOARD E ANALYTICS

Gli eventi registrati vengono aggregati nei dataset analitici.

Pipeline:

RAW_DATA

↓

DATASET

↓

METRICS

↓

DASHBOARD

La dashboard permette visualizzazione immediata di:

• ore progetto\
• spese progetto\
• incassi progetto\
• attività entità

Aggiornamento:

quasi real-time

# 11 --- REPLICABILITÀ DEL SISTEMA

LOGOS ENGINE è progettato come **template universale**.

Struttura:

LOGOS_TEMPLATE

I progetti utilizzano copie del template.

Esempi:

LOGOS_ADEXIMA

LOGOS_ASPRI

LOGOS_MAURIZIOLAB

Questo garantisce:

• standardizzazione\
• replicabilità\
• isolamento dei progetti

# 12 --- ROADMAP SVILUPPO

Fase 1 --- preparazione database

creazione fogli

aggiornamento schema

Fase 2 --- script ingestion

processing eventi

entity resolution

generazione eventi

Fase 3 --- monitoring

system log

email alerts

feedback

Fase 4 --- UX

autocomplete

guidance messages

feedback migliorato

Fase 5 --- dashboard

dataset

metriche

report

Tempo stimato implementazione MVP:

6-10 ore sviluppo

# 13 --- STATO DEL PROGETTO

Stato architettura:

DESIGN FREEZE

Il modello architetturale del motore LOGOS è considerato stabilizzato.

Eventuali modifiche future riguarderanno:

• script di implementazione\
• ottimizzazioni UX\
• integrazione nuovi input

# VERSION HISTORY

v1.0

Prima definizione completa dell'architettura LOGOS ENGINE.

Pipeline dati universale.

Modello evento.

Struttura database.

Monitoring layer.

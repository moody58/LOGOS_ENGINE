\############################################################

LOGOS — ENGINE DOCUMENTATION

File: LOGOS\_ENGINE.md

System: LOGOS Engine

Ecosystem: AIOS

Document Type: Engine Technical Documentation

Status: Consolidated Draft

Version: v2.0

\############################################################



\---



\## Prefazione



Questo documento descrive il funzionamento interno del motore

LOGOS Engine.



L'obiettivo è documentare i meccanismi operativi che permettono

la trasformazione degli input operativi in eventi registrati

nel ledger del sistema.



Il documento rappresenta la traduzione tecnica del comportamento

del motore e della sua implementazione tramite Google Apps Script.



\---



\## Indice



1 Panoramica del motore

2 Event Processor

3 Header Mapping

4 Validation Engine

5 Entity Resolution Engine

6 Entity Confirmation Layer

7 Event Fingerprint

8 Ledger Writing

9 System Logging

10 Runtime Behavior

11 Modello Event-Driven

12 Estensioni future

13 Note di versionamento



\---



\## 1 — Panoramica del motore



Il motore LOGOS è responsabile della trasformazione degli eventi

inseriti nel sistema in record validati nel ledger.



Flusso principale:



RAW\_INPUT

↓

Processor

↓

RAW\_DATA



Il sistema opera su un modello:



Event Ledger append-only



\---



Evoluzione architetturale



Il motore è evoluto da pipeline lineare a sistema asincrono

event-driven.



\---



\## 2 — Event Processor



Il cuore del sistema è la funzione:



processRawInputV2()



Il processor esegue:



• lettura eventi dalla coda

• validazione dei dati

• risoluzione delle entità

• gestione stati asincroni

• deduplicazione eventi

• generazione ID movimento

• scrittura nel ledger



\---



Stati gestiti



Il processor non opera più solo su eventi NEW.



Stati supportati:



• NEW

• ENTITY\_PENDING



\---



\## 3 — Header Mapping



Il processor utilizza un sistema di mapping dinamico

degli header della tabella.



Le colonne vengono identificate tramite nome

e non tramite indice fisso.



Questo consente:



• maggiore robustezza

• compatibilità con modifiche della struttura

• portabilità verso altri sistemi



\---



\## 4 — Validation Engine



Il Validation Engine verifica la validità

dei progetti associati agli eventi.



Funzionamento:



projectName → lookup → PROJECTS



Se il progetto non esiste

l'evento viene marcato:



ERROR\_PROJECT



\---



\## 5 — Entity Resolution Engine



Il sistema tenta di risolvere

l'entità associata all'evento.



Lookup effettuato su:



ENTITIES



Se l'entità non viene trovata

l'evento NON viene scartato



ma viene marcato:



ENTITY\_PENDING



\---



\## 6 — Entity Confirmation Layer



Introduzione di un layer asincrono:



ENTITY\_CONFIRMATION



Funzioni:



• raccolta input utente

• suggerimento entità

• validazione asincrona



\---



Workflow entità



NEW

↓

ENTITY\_PENDING

↓

ENTITY\_CONFIRMATION

↓

CONFIRM

↓

ENTITY CREATION

↓

REPROCESS

↓

WRITTEN



\---



Caratteristiche:



• nessun blocco della pipeline

• gestione input incompleti

• supporto decisione utente



\---



\## 7 — Event Fingerprint



Per evitare duplicazioni tecniche

all'interno dello stesso batch,

il sistema utilizza una fingerprint evento.



La fingerprint è generata utilizzando:



Project\_ID

Entity\_ID

Data\_Evento

Tipo\_Movimento

Valore

Causale



La fingerprint viene utilizzata

solo all'interno del batch corrente.



\---



\## 8 — Ledger Writing



Gli eventi validati vengono inseriti

nel ledger RAW\_DATA.



Il sistema utilizza una scrittura

append-only.



Ogni evento genera:



ID\_Movimento

Timestamp

Data\_Evento

Project\_ID

Entity\_ID

Tipo\_Movimento

Valore

Causale

Fonte



\---



\## 9 — System Logging



Il sistema registra eventi operativi

tramite il foglio:



SYSTEM\_LOG



Ogni operazione rilevante

genera una voce nel log.



Esempi:



INPUT\_PROCESSOR\_START

INPUT\_WRITTEN

INPUT\_ERROR\_PROJECT

INPUT\_ENTITY\_PENDING



\---



\## 10 — Runtime Behavior



Il motore viene attivato tramite

trigger nel runtime Google Sheets.



Flusso operativo:



onEdit

↓

LOGOS\_commitSingleEntity

↓

processRawInputV2



\---



Caratteristiche:



• attivazione automatica

• comportamento asincrono

• nessun intervento manuale richiesto



\---



\## 11 — Modello Event-Driven



Il sistema opera come sistema event-driven.



Eventi generati da:



• input utente

• modifiche fogli

• conferme entità



\---



Comportamento



• eventi incompleti non bloccano il sistema

• eventi pending vengono recuperati

• il sistema è consistente nel tempo



\---



Reprocessing automatico



Gli eventi ENTITY\_PENDING vengono rieseguiti

automaticamente dopo la creazione dell’entità.



\---



\## 12 — Estensioni future



Possibili evoluzioni del motore:



• worker processor dedicato

• message queue

• API ingestion

• scheduler batch



Compatibilità:



• SQL

• Baserow



\---



\## 13 — Note di versionamento



Versione v2.0



Aggiornamenti principali:



• introduzione gestione asincrona entità

• introduzione ENTITY\_CONFIRMATION

• supporto multi-stato

• reprocessing automatico

• modello event-driven



\---



Documento allineato con:



LOGOS\_SPEC.md

LOGOS\_ARCHITECTURE.md

LOGOS\_DATA\_MODEL.md

LOGOS\_SCRIPT\_LAYER.md



\---




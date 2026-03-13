FILE NAME

0602_LOGOS_NOTEBOOK_v1.0

DOCUMENT TYPE

System Notebook

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

Questo documento rappresenta il notebook progettuale del sistema LOGOS.

Il notebook non ha funzione di specifica tecnica ma di documentazione
evolutiva del sistema.

Il suo scopo è preservare il contesto di sviluppo, le motivazioni delle
scelte progettuali e la logica evolutiva che ha portato alla definizione
del motore dati LOGOS.

Il documento integra e completa lo SPEC tecnico del sistema.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

2\. Origine del sistema

Il sistema LOGOS nasce dall\'esigenza di costruire un registro operativo
affidabile per la gestione di attività, eventi e movimenti economici
all[']{dir="rtl"}interno dell[']{dir="rtl"}ecosistema AIOS.

Nella fase iniziale del progetto è stato sviluppato un primo modello
denominato CORE.

CORE era un sistema basato su Google Sheets che permetteva di:

\- registrare attività operative

\- tracciare movimenti economici

\- associare eventi a progetti

Il sistema funzionava come registro operativo ma presentava alcuni
limiti strutturali.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

3\. Limiti del modello CORE

Durante l[']{dir="rtl"}utilizzo operativo del sistema CORE sono emersi
alcuni problemi tipici dei sistemi informativi leggeri basati su fogli
di calcolo.

Tra i principali limiti identificati:

assenza di integrità dei dati

difficoltà nel controllare modifiche accidentali

mancanza di identificatori persistenti

difficoltà nel replicare il sistema su nuovi progetti

Inoltre il modello CORE non distingueva chiaramente tra:

dati operativi

configurazione del sistema

dataset analitici

Queste criticità hanno portato alla necessità di progettare un sistema
più strutturato.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

4\. Evoluzione verso LOGOS

L[']{dir="rtl"}evoluzione dal sistema CORE al motore LOGOS ha introdotto
un cambio di paradigma.

Il sistema è stato riprogettato secondo un modello event-driven.

In questo modello ogni attività operativa viene registrata come evento
all[']{dir="rtl"}interno di un registro centrale.

Il registro eventi diventa quindi la fonte primaria dei dati del
sistema.

Le analisi e le metriche vengono poi generate tramite dataset derivati.

Questo approccio consente di:

mantenere tracciabilità completa degli eventi

garantire integrità dei dati

semplificare la replicabilità del sistema

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

5\. Separazione tra metodo e motore dati

Durante lo sviluppo del sistema è emersa la necessità di separare
chiaramente due livelli dell[']{dir="rtl"}ecosistema.

Il primo livello è il sistema metodologico.

Questo livello è rappresentato da AIOS.

AIOS definisce il metodo di progettazione, organizzazione e gestione dei
progetti.

Il secondo livello è il motore dati.

Questo livello è rappresentato da LOGOS.

LOGOS gestisce esclusivamente:

registrazione eventi

struttura dati

dataset analitici

Questa separazione consente di mantenere indipendenti:

il metodo di lavoro

l[']{dir="rtl"}infrastruttura dati

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

6\. Integrazione con AIOS

LOGOS è progettato per essere il motore dati
dell[']{dir="rtl"}ecosistema AIOS.

All[']{dir="rtl"}interno della struttura AIOS il sistema LOGOS è
utilizzato dai progetti operativi.

Esempi di progetti che utilizzano LOGOS includono:

ADEXIMA

ASPRI

MaurizioLab

Ogni progetto può utilizzare una copia del template LOGOS mantenendo la
stessa struttura dati.

Questo consente di avere coerenza tra i sistemi informativi dei diversi
progetti.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

7\. Principio del registro eventi

Il principio centrale del sistema LOGOS è il registro eventi.

Ogni attività operativa è registrata come evento.

Un evento può rappresentare:

un\'attività lavorativa

una spesa

un incasso

un evento informativo

Tutti gli eventi sono registrati nel foglio RAW_DATA.

Il registro eventi è considerato immutabile.

Le analisi non modificano il registro ma utilizzano dataset derivati.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

8\. Dataset derivati

Per evitare modifiche dirette sul registro eventi, il sistema utilizza
dataset derivati.

I dataset derivati sono costruiti tramite formule e aggregazioni.

Questi dataset permettono di calcolare metriche e indicatori operativi.

Tra i dataset principali:

CONTROLLO_PROJECT

CONTROLLO_ENTITY

CONTROLLO_ENTITY_DETTAGLIO

Questi dataset consentono di ottenere una visione aggregata delle
attività del sistema.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

9\. Evoluzione futura del sistema

Il sistema LOGOS è progettato per essere evolutivo.

Nuove funzionalità possono essere introdotte senza modificare la
struttura base del registro eventi.

Tra le possibili evoluzioni del sistema:

integrazione con moduli di input automatizzati

integrazione con sistemi di reportistica avanzata

sviluppo di dashboard operative

integrazione con strumenti di analisi dati

La struttura modulare del sistema consente di sviluppare nuove
funzionalità mantenendo la compatibilità con le versioni precedenti.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

10\. Ruolo del notebook nel sistema documentale

Il notebook ha il compito di conservare la memoria progettuale del
sistema.

Mentre lo SPEC descrive la struttura tecnica del sistema, il notebook
documenta:

il contesto di sviluppo

le motivazioni delle scelte progettuali

l[']{dir="rtl"}evoluzione del sistema

Questo approccio consente di mantenere separati:

documentazione tecnica

documentazione evolutiva

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

DOCUMENT METADATA

File Name

0602_LOGOS_NOTEBOOK_v1.0

Sistema

LOGOS Engine

Ecosistema

AIOS

Lingua

Italiano

Stato

Stabile

Changelog

Prima versione del notebook progettuale del sistema LOGOS

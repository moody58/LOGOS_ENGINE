############################################################
LOGOS — CHANGELOG
File: LOGOS_CHANGELOG.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: Architectural Changelog
Status: Active
############################################################

Prefazione
Questo documento registra l'evoluzione architetturale del sistema LOGOS.
Il changelog non è limitato a modifiche tecniche del codice, ma documenta
decisioni strutturali, evoluzioni del modello dati e modifiche al design
del motore.

Il documento serve come registro storico delle scelte progettuali
che influenzano il comportamento del sistema.

Indice
1 Principi del changelog
2 Decisioni architetturali
3 Evoluzioni del sistema
4 Incidenti e correzioni
5 Migrazioni tecnologiche
6 Note di versionamento

1 — Principi del changelog

Il changelog LOGOS segue un modello architetturale.

Ogni registrazione può appartenere a una delle categorie:

Decisione
Evoluzione
Incidente
Migrazione

Questo approccio permette di mantenere una memoria storica
delle scelte progettuali del sistema.

2 — Decisioni architetturali

Event Ledger Architecture
LOGOS utilizza un modello basato su ledger append-only.
Gli eventi non vengono modificati o cancellati ma
corretti tramite nuovi eventi.

Separazione Input / Ledger
Gli eventi vengono prima inseriti nella coda RAW_INPUT
e successivamente processati nel ledger RAW_DATA.

Dimension Tables indipendenti
Le dimensioni PROJECTS ed ENTITIES sono separate
dal ledger per consentire analisi multidimensionali.

Queue Cursor
È stato introdotto il meccanismo LOGOS_QUEUE_CURSOR
per evitare la scansione completa della coda eventi.

Header Mapping dinamico
Il processor identifica le colonne tramite nome
anziché indice numerico.

Event Fingerprint
Il sistema utilizza una fingerprint evento per
evitare duplicazioni tecniche all'interno dello stesso batch.

3 — Evoluzioni del sistema

Introduzione pipeline eventi

INPUT
↓
RAW_INPUT
↓
PROCESSOR
↓
RAW_DATA
↓
DATASET ANALITICI

Introduzione analytics layer

CONTROLLO_PROJECT
CONTROLLO_ENTITY

con dataset di dettaglio associati.

Separazione campi derivati

Campi come Unità e Categoria_Derivata sono stati
spostati concettualmente nel layer analytics.

4 — Incidenti e correzioni

Duplicazione eventi nello stesso batch
È stato introdotto il controllo tramite fingerprint
per evitare duplicazioni tecniche durante il processing.

Scrittura eventi fuori range nel ledger
La logica di append è stata stabilizzata per garantire
scrittura sempre in fondo al ledger.

5 — Migrazioni tecnologiche

Implementazione attuale
Google Sheets + Apps Script.

Trigger di attivazione

onEdit
↓
LOGOS_inputTrigger
↓
processRawInputV2

Questo metodo è considerato temporaneo
e utilizzato nella fase prototipale.

Evoluzione prevista

Event Queue
↓
Worker Processor
↓
Event Ledger
↓
Analytics Engine

Compatibile con database SQL e Baserow.

6 — Note di versionamento

Questo documento è generato tramite protocollo CQD
e rappresenta il registro ufficiale delle evoluzioni
del sistema LOGOS.

Documenti correlati:

LOGOS_SPEC.md
LOGOS_ARCHITECTURE.md
LOGOS_ENGINE.md
LOGOS_DATA_MODEL.md
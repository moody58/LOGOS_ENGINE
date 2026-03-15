############################################################
LOGOS — ENGINE DOCUMENTATION
File: LOGOS_ENGINE.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: Engine Technical Documentation
Status: Consolidated Draft
############################################################

Prefazione
Questo documento descrive il funzionamento interno del motore
LOGOS Engine.

L'obiettivo è documentare i meccanismi operativi che permettono
la trasformazione degli input operativi in eventi registrati
nel ledger del sistema.

Il documento rappresenta la traduzione tecnica del codice
implementato tramite Google Apps Script.

Indice
1 Panoramica del motore
2 Event Processor
3 Queue Cursor
4 Header Mapping
5 Validation Engine
6 Entity Resolution Engine
7 Event Fingerprint
8 Ledger Writing
9 System Logging
10 Runtime Behavior
11 Estensioni future
12 Note di versionamento

1 — Panoramica del motore

Il motore LOGOS è responsabile della trasformazione degli eventi
inseriti nel sistema in record validati nel ledger.

Flusso principale:

RAW_INPUT
↓
Processor
↓
RAW_DATA

Il processor opera su batch limitati per mantenere stabilità
nell'ambiente Google Apps Script.

2 — Event Processor

Il cuore del sistema è la funzione:

processRawInputV2()

Il processor esegue:

lettura eventi dalla coda
validazione dei dati
risoluzione delle entità
normalizzazione dei campi
generazione ID movimento
scrittura nel ledger

3 — Queue Cursor

Per evitare la rilettura completa della coda eventi,
il sistema utilizza un meccanismo chiamato Queue Cursor.

Il cursore è salvato tramite:

DocumentProperties

Chiave utilizzata:

LOGOS_QUEUE_CURSOR

Il cursore memorizza l'ultima riga processata
all'interno della coda RAW_INPUT.

Questo consente di:

evitare scansioni complete
ridurre carico computazionale
migliorare le prestazioni

4 — Header Mapping

Il processor utilizza un sistema di mapping dinamico
degli header della tabella.

Le colonne vengono identificate tramite nome
e non tramite indice fisso.

Questo consente:

maggiore robustezza
compatibilità con modifiche della struttura
portabilità verso altri sistemi

5 — Validation Engine

Il Validation Engine verifica la validità
dei progetti associati agli eventi.

Funzionamento:

projectName → lookup → PROJECTS

Se il progetto non esiste
l'evento viene marcato:

ERROR_PROJECT

6 — Entity Resolution Engine

Il sistema tenta di risolvere
l'entità associata all'evento.

Lookup effettuato su:

ENTITIES

Se l'entità non viene trovata
l'evento viene marcato:

ENTITY_PENDING

7 — Event Fingerprint

Per evitare duplicazioni tecniche
all'interno dello stesso batch,
il sistema utilizza una fingerprint evento.

La fingerprint è generata utilizzando:

Project_ID
Entity_ID
Data_Evento
Tipo_Movimento
Valore
Causale

La fingerprint viene utilizzata
solo all'interno del batch corrente.

8 — Ledger Writing

Gli eventi validati vengono inseriti
nel ledger RAW_DATA.

Il sistema utilizza una scrittura
append-only.

Ogni evento genera:

ID_Movimento
Timestamp
Data_Evento
Project_ID
Entity_ID
Tipo_Movimento
Valore
Causale
Fonte

9 — System Logging

Il sistema registra eventi operativi
tramite il foglio:

SYSTEM_LOG

Ogni operazione rilevante
genera una voce nel log.

Esempi:

INPUT_PROCESSOR_START
INPUT_WRITTEN
INPUT_ERROR_PROJECT
INPUT_ENTITY_PENDING

10 — Runtime Behavior

Il motore viene attivato tramite
trigger onEdit nel runtime Google Sheets.

Il flusso operativo è:

onEdit
↓
LOGOS_inputTrigger
↓
processRawInputV2

Questo comportamento è considerato
temporaneo e legato alla fase
prototipale del sistema.

11 — Estensioni future

Possibili evoluzioni del motore:

worker processor
queue message broker
API ingestion
scheduler batch processing

12 — Note di versionamento

Documento generato tramite protocollo CQD.

Allineato con:

LOGOS_SPEC.md
LOGOS_ARCHITECTURE.md
implementazione Apps Script del motore.
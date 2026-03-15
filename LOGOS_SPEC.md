############################################################
LOGOS — SYSTEM SPECIFICATION
File: LOGOS_SPEC.md
System: LOGOS Engine
Ecosystem: AIOS
Document Type: System Specification
Status: Consolidated Draft
############################################################

Prefazione
Il presente documento definisce la specifica concettuale ufficiale del sistema LOGOS.
LOGOS è un motore di gestione eventi e ledger universale progettato per supportare
contabilità operativa, gestione progetti, tracciamento del tempo, monitoraggio attività
e analisi KPI tramite una pipeline eventi strutturata.

Il sistema è progettato come template universale destinato a generare istanze operative
indipendenti. Esempi di istanze previste includono:
- LOGOS_ADEXIMA
- LOGOS_ASPRI
- LOGOS_MAURIZIOLAB

Ogni istanza mantiene completa separazione dei dati.

Indice
1 Scopo del sistema
2 Principi architetturali
3 Modello concettuale
4 Tipologie di evento
5 Pipeline di ingestione
6 Event Ledger
7 Dimension Tables
8 Modello temporale
9 Analytics Layer
10 Modello di istanza
11 Compatibilità tecnologica
12 Vincoli architetturali
13 Estensioni previste
14 Note di versionamento

1 — Scopo del sistema
LOGOS è un Event Processing Engine progettato per registrare, processare e analizzare
eventi operativi all'interno di sistemi organizzativi complessi.

Gli eventi possono rappresentare:
- attività operative
- tempo di lavoro
- spese
- incassi
- eventi organizzativi

Gli eventi sono trasformati in record standardizzati all'interno di un ledger append-only.

2 — Principi architetturali
Event-driven architecture
Ogni operazione registrata nel sistema è trattata come evento.

Append-only ledger
Gli eventi non vengono modificati né cancellati; eventuali correzioni avvengono tramite
nuovi eventi che mantengono la cronologia completa.

Separazione input / ledger
Gli input operativi vengono prima inseriti nella coda RAW_INPUT e successivamente
processati dal motore LOGOS_PROCESSOR.

Dimension tables separate
Le dimensioni organizzative sono gestite tramite tabelle dedicate:
PROJECTS e ENTITIES.

3 — Modello concettuale
Oggetti fondamentali del sistema:
EVENT
ENTITY
PROJECT

Relazioni:
EVENT → Entity_ID
EVENT → Project_ID

Le dimensioni ENTITY e PROJECT sono indipendenti e supportano gerarchie parent-child.

4 — Tipologie di evento
Campo: Tipo_Movimento

Valori supportati:
Tempo
Spesa
Incasso
Evento

Evento rappresenta eventi operativi generici come meeting, milestone,
deploy o task non necessariamente economici.

5 — Pipeline di ingestione eventi
INPUT
↓
RAW_INPUT
↓
LOGOS_PROCESSOR
↓
RAW_DATA
↓
DATASET ANALITICI
↓
DASHBOARD

6 — Event Ledger
Ledger principale: RAW_DATA

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

7 — Dimension Tables
PROJECTS
ENTITIES

Entrambe supportano strutture gerarchiche.

8 — Modello temporale
LOGOS utilizza due assi temporali:
Timestamp → tempo di registrazione nel sistema
Data_Evento → tempo reale dell'evento

9 — Analytics Layer
Dataset principali:
CONTROLLO_PROJECT
CONTROLLO_ENTITY

Dataset di dettaglio:
CONTROLLO_PROJECT_DETTAGLIO
CONTROLLO_ENTITY_DETTAGLIO

10 — Modello di istanza
LOGOS è progettato come template engine per generare istanze indipendenti.

11 — Compatibilità tecnologica
Google Sheets + Apps Script
Database SQL
Baserow

12 — Vincoli architetturali
ledger append-only
event-driven ingestion
separazione input / ledger
dimension tables separate

13 — Estensioni previste
API ingestion layer
dashboard avanzate
integrazione SQL
aggregazione multi-istanza

14 — Note di versionamento
Documento generato tramite protocollo CQD e allineato al motore LOGOS
attualmente implementato.
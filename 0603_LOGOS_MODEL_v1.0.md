FILE NAME

0603_LOGOS_MODEL_v1.0

DOCUMENT TYPE

Data Model Specification

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

Il presente documento descrive il modello dati del sistema LOGOS Engine.

Il modello definisce la struttura logica del database utilizzato dal
sistema, incluse:

\- tabelle

\- campi

\- relazioni

\- vincoli di integrità

\- logica di generazione degli identificatori

\- formule di aggregazione

Il database è implementato tramite Google Sheets ed è progettato per
funzionare come registro eventi strutturato.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

2\. Struttura generale del database

Il database LOGOS è organizzato in quattro categorie principali di
tabelle.

CONFIGURAZIONE

SETTINGS

ENTITÀ OPERATIVE

PROJECTS

ENTITIES

REGISTRO EVENTI

RAW_INPUT

RAW_DATA

DATASET ANALITICI

CONTROLLO_PROJECT

CONTROLLO_PROJECT_DETTAGLIO

CONTROLLO_ENTITY

CONTROLLO_ENTITY_DETTAGLIO

Questa separazione garantisce una chiara distinzione tra dati operativi,
configurazione e dataset derivati.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

3\. Tabella SETTINGS

Funzione

Contiene le liste di configurazione utilizzate per i menu a discesa e le
convalide dati.

Campi principali

Campo Tipo Descrizione

Tag String Identificatore logico della lista

Valore String Valore utilizzato nel sistema

Descrizione String Descrizione estesa del valore

Utilizzi principali

Project_Type

Project_Status

Entity_Type

Entity_Status

Tipo_Movimento

Metodo_Pagamento

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

4\. Tabella PROJECTS

Funzione

Contiene l\'elenco dei progetti gestiti dal sistema.

Campi

Project_ID String Identificatore univoco

Project_Name String Nome del progetto

Parent_Project_ID String Collegamento gerarchico

Project_Type String Tipo progetto

Status String Stato progetto

Start_Date Date Data inizio

End_Date Date Data fine

Created_Timestamp Timestamp Data creazione

Notes String Note

Vincoli

Project_ID è chiave primaria.

Parent_Project_ID non può coincidere con Project_ID.

Un progetto con movimenti associati non può essere cancellato.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

5\. Tabella ENTITIES

Funzione

Contiene soggetti e organizzazioni coinvolti negli eventi.

Campi

Entity_ID String

Entity_Type String

First_Name String

Last_Name String

Company_Name String

Display_Name String

Phone String

Email String

Country String

City String

Address String

Geo_Location String

Status String

Parent_Entity_ID String

Created_Timestamp Timestamp

Modified_Timestamp Timestamp

Source String

Notes String

Logica Display_Name

Display_Name è calcolato tramite formula:

se presente Company_Name → usare Company_Name

altrimenti → concatenazione Nome + Cognome

Vincoli

Entity_ID è chiave primaria.

Parent_Entity_ID non può coincidere con Entity_ID.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

6\. Tabella RAW_INPUT

Funzione

Contiene eventuali dati inseriti tramite form o input manuali
semplificati.

Campi

Timestamp_Form Timestamp

Project_Name String

Tipo_Movimento String

Valore Number

Data_Evento Date

Causale String

Riferimento_ID String

Metodo_Pagamento String

Note String

Soggetto String

Questa tabella può essere utilizzata come layer di ingestione dati.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

7\. Tabella RAW_DATA

Funzione

Registro eventi principale del sistema.

Campi

ID_Movimento String

Timestamp Timestamp

Data_Evento Date

Soggetto String

Project_ID String

Entity_ID String

Tipo_Movimento String

Valore Number

Unita String

Causale String

Categoria_Derivata String

Riferimento_ID String

Fonte String

Metodo_Pagamento String

Note String

Logica categoria derivata

Tempo → unità H

Spesa → unità €

Incasso → unità €

Evento → unità vuota

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

8\. Dataset CONTROLLO_PROJECT

Funzione

Aggregazione delle metriche per progetto.

Campi principali

Project_ID

Project_Name

Project_Type

Status

Ore_Diretto

Spese_Diretto

Incassi_Diretto

Movimenti_Diretto

Ore_Figli

Spese_Figli

Incassi_Figli

Movimenti_Figli

Ore_Complessive

Spese_Complessive

Incassi_Complessivi

Movimenti_Complessivi

Le aggregazioni sono calcolate tramite funzioni SUMIFS.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

9\. Dataset CONTROLLO_ENTITY

Funzione

Aggregazione delle metriche per entità.

Campi principali

Entity_ID

Display_Name

Entity_Type

Totale_Ore

Totale_Spese

Totale_Incassi

Numero_Movimenti

Ore_Figli

Spese_Figli

Incassi_Figli

Movimenti_Figli

Ore_Complessive

Spese_Complessive

Incassi_Complessivi

Movimenti_Complessivi

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

10\. Relazioni tra tabelle

Relazioni principali del sistema.

PROJECTS

Project_ID ← RAW_DATA.Project_ID

ENTITIES

Entity_ID ← RAW_DATA.Entity_ID

Gerarchie

PROJECTS.Parent_Project_ID

ENTITIES.Parent_Entity_ID

Queste relazioni consentono aggregazioni gerarchiche.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

11\. Automazioni Apps Script

Il sistema utilizza Google Apps Script per garantire integrità e
automazione.

Trigger principale

onEdit(e)

Funzioni principali

Generazione ID_MOVIMENTO

Generazione Project_ID

Generazione Entity_ID

Protezione chiavi primarie

Blocco cancellazione progetti con movimenti

Blocco auto-parent

Le automazioni garantiscono coerenza del database.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

12\. Regole di integrità dati

Regole principali del sistema.

Le chiavi primarie non sono modificabili manualmente.

La cancellazione di progetti con movimenti associati è bloccata.

Le relazioni gerarchiche non possono essere auto-referenziali.

I timestamp sono generati automaticamente.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

13\. Versionamento del modello

Il modello dati è versionato tramite documento LOGOS_CHANGELOG.

Ogni modifica strutturale deve aggiornare la versione del modello.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

DOCUMENT METADATA

File Name

0603_LOGOS_MODEL_v1.0

Sistema

LOGOS Engine

Ecosistema

AIOS

Lingua

Italiano

Stato

Stabile

Changelog

Prima definizione del modello dati del sistema LOGOS

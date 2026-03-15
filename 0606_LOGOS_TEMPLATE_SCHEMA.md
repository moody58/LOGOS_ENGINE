# STRUTTURA DEL TEMPLATE LOGOS

Il database è organizzato in quattro livelli logici.

Configurazione\
Entità operative\
Registro eventi\
Dataset analitici

La separazione dei livelli consente di mantenere il registro eventi
stabile mentre le analisi vengono generate da dataset derivati.

# LIVELLO 1 --- CONFIGURAZIONE

Foglio:

SETTINGS

Funzione:

Registro centralizzato delle configurazioni del sistema.

Struttura reale:

Setting_Type

Value

Description

(blank column)

Project_Type_List

Status_List

Entity_Type_List

Entity_Status_List

Entity_ID_List

Le prime tre colonne costituiscono il **registro configurazione**.

Le colonne dalla E in avanti sono **liste filtrate** utilizzate per menu
a discesa e convalide dati.

Flusso logico:

SETTINGS registro

↓

FILTER

↓

liste dropdown

↓

convalida dati database

Questo rende il template replicabile tra istanze.

# LIVELLO 2 --- ENTITÀ OPERATIVE

## PROJECTS

Contiene i progetti del sistema.

Campi principali:

Project_ID

Project_Name

Parent_Project_ID

Project_Type

Status

Start_Date

End_Date

Created_Timestamp

Notes

Vincoli:

-   Project_ID chiave primaria

-   Parent_Project_ID non può essere uguale a Project_ID

-   progetti con movimenti associati non possono essere cancellati

Relazione:

Project_ID → RAW_DATA.Project_ID

## ENTITIES

Contiene persone o organizzazioni coinvolte negli eventi.

Campi principali:

Entity_ID

Entity_Type

First_Name

Last_Name

Company_Name

Display_Name

Phone

Email

Country

City

Address

Geo_Location

Status

Parent_Entity_ID

Created_Timestamp

Modified_Timestamp

Source

Notes

Logica Display_Name:

se Company_Name presente → usare Company_Name

altrimenti → Nome + Cognome

Relazione:

Entity_ID → RAW_DATA.Entity_ID

Gerarchia:

Parent_Entity_ID

# LIVELLO 3 --- REGISTRO EVENTI

Il cuore del sistema.

## RAW_INPUT

Layer di ingestione dati.

Usato per:

-   Google Form

-   Siri

-   input manuale semplificato

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

RAW_INPUT può essere trasformato in eventi tramite normalizzazione.

## RAW_DATA

Registro eventi principale del sistema.

Schema reale:

ID_Movimento

Timestamp

Data_Evento

Soggetto_Input

Project_ID

Entity_ID

Tipo_Movimento

Valore

Unita

Causale

Categoria_Derivata

Riferimento_ID

Fonte

Metodo_Pagamento

Note

Logica evento:

Project_ID

↓

trigger script

↓

ID_Movimento + Timestamp

Tipo_Movimento:

Tempo

Spesa

Incasso

Evento

Categoria_Derivata:

Tempo → Operativa

Spesa → Economica_Uscita

Incasso → Economica_Entrata

Evento → Informativa

Questo campo è generato tramite formula.

Il registro eventi è considerato **immutabile**.

# LIVELLO 4 --- DATASET ANALITICI

Questi fogli aggregano i dati del registro eventi.

## CONTROLLO_PROJECT

Aggregazione per progetto.

Metriche principali:

Ore_Diretto

Spese_Diretto

Incassi_Diretto

Movimenti_Diretto

Metriche gerarchiche:

Ore_Figli

Spese_Figli

Incassi_Figli

Movimenti_Figli

Metriche complessive:

Ore_Complessive

Spese_Complessive

Incassi_Complessivi

Movimenti_Complessivi

Le aggregazioni sono calcolate tramite SUMIFS.

## CONTROLLO_PROJECT_DETTAGLIO

Supporta le relazioni padre-figlio tra progetti.

Serve per calcolare aggregazioni gerarchiche.

## CONTROLLO_ENTITY

Aggregazione per entità.

Metriche:

Totale_Ore

Totale_Spese

Totale_Incassi

Numero_Movimenti

Include aggregazioni per entità figlie.

## CONTROLLO_ENTITY_DETTAGLIO

Supporta le gerarchie delle entità.

# RELAZIONI PRINCIPALI

Relazioni tra tabelle:

PROJECTS.Project_ID

↓

RAW_DATA.Project_ID

ENTITIES.Entity_ID

↓

RAW_DATA.Entity_ID

Gerarchie:

PROJECTS.Parent_Project_ID

ENTITIES.Parent_Entity_ID

# AUTOMAZIONI

Il sistema utilizza Apps Script con trigger:

onEdit(e)

Funzioni principali:

-   generazione ID_MOVIMENTO

-   generazione Project_ID

-   generazione Entity_ID

-   protezione chiavi primarie

-   blocco cancellazione progetti con movimenti

-   blocco auto-parent

# MODELLO DATI

Il sistema segue il pattern:

Event Ledger

\+

Dimension Tables

\+

Derived Aggregations

Tradotto in LOGOS:

RAW_DATA → eventi

PROJECTS / ENTITIES → dimensioni

CONTROLLO\_\* → aggregazioni

Questo modello permette di costruire report e dashboard senza modificare
il registro eventi.

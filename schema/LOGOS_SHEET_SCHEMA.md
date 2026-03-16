# LOGOS_SHEET_SCHEMA.md

# SYSTEM
LOGOS — Event Ledger System

Architettura logica:

RAW_INPUT
   │
   ▼
Processor.gs
   │
   ▼
RAW_DATA (Ledger append-only)
   │
   ├── PROJECTS
   ├── ENTITIES
   │
   ▼
CONTROLLO_PROJECT_DETTAGLIO
CONTROLLO_PROJECT
CONTROLLO_ENTITY_DETTAGLIO
CONTROLLO_ENTITY
   │
   ▼
SYSTEM_LOG


------------------------------------------------
FIELD ORIGIN MAP
------------------------------------------------

Manual   → inserimento utente
Script   → generato da Apps Script
Formula  → generato da formula Google Sheets
Derived  → aggregazione analytics


------------------------------------------------
RAW_INPUT
------------------------------------------------

Tipo:
Event Queue

Funzione:
buffer eventi in attesa di processamento

Script collegato:
Processor.gs

Colonne:

Timestamp_Form        Manual
Project_Name          Manual
Tipo_Movimento        Manual
Valore                Manual
Data_Evento           Manual
Causale               Manual
Riferimento_ID        Manual
Metodo_Pagamento      Manual
Note                  Manual
Soggetto              Manual
Source                Manual
Status                Script


Stati possibili:

NEW
WRITTEN
ERROR_PROJECT
ENTITY_PENDING
DUPLICATE


Campi letti dal Processor:

Project_Name
Tipo_Movimento
Valore
Data_Evento
Causale
Riferimento_ID
Metodo_Pagamento
Note
Soggetto
Source
Status


------------------------------------------------
RAW_DATA
------------------------------------------------

Tipo:
Event Ledger (append-only)

Script collegati:

Kernel.gs
Processor.gs

Colonne:

ID_Movimento        Script
Timestamp           Script
Data_Evento         Manual
Soggetto_Input      Manual
Project_ID          Dropdown
Entity_ID           Dropdown
Tipo_Movimento      Dropdown
Valore              Manual
Unita               Formula
Causale             Manual
Categoria_Derivata  Formula
Riferimento_ID      Manual
Fonte               Manual
Metodo_Pagamento    Manual
Note                Manual


VALIDAZIONI DATI (Dropdown)

Project_ID

Tipo:
Data Validation Dropdown

Range origine:

=PROJECTS!$A$2:$A$999



Entity_ID

Tipo:
Data Validation Dropdown

Range origine:

=SETTINGS!$I$2:$I



Tipo_Movimento

Tipo:
Data Validation Dropdown

Range origine:

=SETTINGS!$B$8:$B$11


FORMULE

Unita

=ARRAYFORMULA(
IF(G2:G="";
"";
IF(G2:G="Tempo";"h";
IF(G2:G="Spesa";"€";
IF(G2:G="Incasso";"€";
IF(G2:G="Evento";"Evento";""))))))


Categoria_Derivata

=ARRAYFORMULA(
IF(G2:G="";
"";
IF(G2:G="Tempo";"Operativa";
IF(G2:G="Spesa";"Economica_Uscita";
IF(G2:G="Incasso";"Economica_Entrata";
IF(G2:G="Evento";"Informativa";""))))))


------------------------------------------------
SETTINGS
------------------------------------------------

Funzione:
registro configurazioni sistema

Colonne:

Setting_Type
Value
Description
Project_Type_List
Status_List
Entity_Type_List
Entity_Status_List
Entity_ID_List
Performance_monitor


FORMULE


Entity_ID_List

=FILTER(ENTITIES!A2:A; ENTITIES!A2:A<>"")


LISTE DINAMICHE


Project_Type_List

=FILTER(B:B; A:A="Project_Type")


Status_List

=FILTER(B:B; A:A="Status")


Entity_Type_List

=FILTER(B:B; A:A="Entity_Type")


Entity_Status_List

=FILTER(B:B; A:A="Entity_Status")


------------------------------------------------
PROJECTS
------------------------------------------------

Funzione:
dimension table dei progetti


Colonne:

Project_ID
Project_Name
Parent_Project_ID
Project_Type
Status
Ore_Diretto
Spese_Diretto
Incassi_Diretto
Margine_Diretto
Movimenti_Diretto
Ore_Figli
Spese_Figli
Incassi_Figli
Movimenti_Figli
Ore_Complessive
Spese_Complessive
Incassi_Complessivi
Movimenti_Complessivi


VALIDAZIONI DATI


Parent_Project_ID

=PROJECTS!$A$2:$A$999


Project_Type

=SETTINGS!$E$2:$E


Status

=SETTINGS!$F$2:$F


------------------------------------------------
ENTITIES
------------------------------------------------

Funzione:
dimension table delle entità


Colonne:

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


FORMULE


Display_Name

=ARRAYFORMULA(
IF(B2:B="";
"";
IF(E2:E<>"";
E2:E;
TRIM(C2:C & " " & D2:D)
)
)
)


------------------------------------------------
ENTITY_CONFIRMATION
------------------------------------------------

Funzione:
workflow conferma entità non riconosciute automaticamente

Colonne:

Input_Text
Suggested_Entity
Suggested_Entity_ID
User_Confirmation
Confirmed_Entity_ID
Timestamp
Status



FORMULE

Colonna B — Suggested_Entity

=ARRAYFORMULA(
IF(A2:A="";
"";
IFERROR(
VLOOKUP(
LOWER(A2:A);
{LOWER(ENTITIES!C2:C)\ENTITIES!C2:C};
2;
FALSE
);
""
)
)
)



Colonna C — Suggested_Entity_ID

=ARRAYFORMULA(
IF(A2:A="";
"";
IFERROR(
VLOOKUP(
LOWER(A2:A);
{LOWER(ENTITIES!C2:C)\ENTITIES!A2:A};
2;
FALSE
);
""
)
)
)



Colonna E — Confirmed_Entity_ID

=ARRAYFORMULA(
IF(A2:A="";
"";
IF(D2:D="Confirm";
C2:C;
""
)
)
)



Colonna F — Timestamp

=ARRAYFORMULA(
IF(A2:A="";
"";
IF(F2:F="";
NOW();
F2:F
)
)
)



Colonna G — Status

=ARRAYFORMULA(
IF(A2:A="";
"";
IF(E2:E<>"";
"CONFIRMED";
IF(C2:C<>"";
"SUGGESTED";
"PENDING"
)
)
)
)


------------------------------------------------
CONTROLLO_PROJECT_DETTAGLIO
------------------------------------------------

Funzione:
aggregazione movimenti per progetti figli


Colonne:

Parent_Project_ID
Project_ID
Project_Name
Ore
Spese
Incassi
Movimenti


FORMULE


Relazione padre-figlio

=FILTER(
{PROJECTS!C2:C\PROJECTS!A2:A};
PROJECTS!C2:C<>""
)


Project_Name

=ARRAYFORMULA(
IF(B2:B="";
"";
VLOOKUP(B2:B;PROJECTS!A:B;2;FALSE)
)
)


Ore

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!E:E;figlio;
RAW_DATA!G:G;"Tempo")
)
)
)
)


Spese

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!E:E;figlio;
RAW_DATA!G:G;"Spesa")
)
)
)
)


Incassi

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!E:E;figlio;
RAW_DATA!G:G;"Incasso")
)
)
)
)


Movimenti

=ARRAYFORMULA(
IF(B2:B="";
"";
COUNTIF(RAW_DATA!E:E;B2:B)
)
)


------------------------------------------------
CONTROLLO_PROJECT
------------------------------------------------

Funzione:
vista aggregata completa dei progetti


Colonne:

Project_ID
Project_Name
Project_Type
Status
Ore_Diretto
Spese_Diretto
Incassi_Diretto
Margine_Diretto
Movimenti_Diretto
Ore_Figli
Spese_Figli
Incassi_Figli
Movimenti_Figli
Ore_Complessive
Spese_Complessive
Incassi_Complessivi
Movimenti_Complessivi


FORMULE


Dataset progetti

=FILTER(
{PROJECTS!A2:A499\PROJECTS!B2:B499\PROJECTS!D2:D499\PROJECTS!E2:499};
PROJECTS!A2:A499<>""
)


Ore_Diretto

=ARRAYFORMULA(
MAP(
A2:A200;
LAMBDA(id;
IF(id="";
"";
SUMIFS(
RAW_DATA!H2:H1002;
RAW_DATA!E2:E1002;id;
RAW_DATA!G2:G1002;"Tempo"
)
)
)
))


Spese_Diretto

=ARRAYFORMULA(
MAP(
A2:A200;
LAMBDA(id;
IF(id="";
"";
SUMIFS(
RAW_DATA!H2:H1002;
RAW_DATA!E2:E1002;id;
RAW_DATA!G2:G1002;"Spesa"
)
)
)
))


Incassi_Diretto

=ARRAYFORMULA(
MAP(
A2:A200;
LAMBDA(id;
IF(id="";
"";
SUMIFS(
RAW_DATA!H2:H1002;
RAW_DATA!E2:E1002;id;
RAW_DATA!G2:G1002;"Incasso"
)
)
)
))


Margine_Diretto

=ARRAYFORMULA(
IF(A2:A500="";
"";
G2:G2000 - F2:F2000
)
)


Movimenti_Diretto

=ARRAYFORMULA(
IF(A2:A500="";
"";
COUNTIF(RAW_DATA!E2:E1002;A2:A2000)
)
)


Ore_Figli

=ARRAYFORMULA(
IF(A2:A500="";
"";
MAP(A2:A500;
LAMBDA(id;
SUMIF(CONTROLLO_PROJECT_DETTAGLIO!A2:A2000;id;CONTROLLO_PROJECT_DETTAGLIO!D2:D2000)
)
)
)
)


Spese_Figli

=ARRAYFORMULA(
IF(A2:A500="";
"";
MAP(A2:A500;
LAMBDA(id;
SUMIF(CONTROLLO_PROJECT_DETTAGLIO!A2:A2000;id;CONTROLLO_PROJECT_DETTAGLIO!E2:E2000)
)
)
)
)


Incassi_Figli

=ARRAYFORMULA(
IF(A2:A500="";
"";
MAP(A2:A500;
LAMBDA(id;
SUMIF(CONTROLLO_PROJECT_DETTAGLIO!A2:A2000;id;CONTROLLO_PROJECT_DETTAGLIO!F2:F2000)
)
)
)
)


Movimenti_Figli

=ARRAYFORMULA(
IF(A2:A500="";
"";
MAP(A2:A500;
LAMBDA(id;
SUMIF(CONTROLLO_PROJECT_DETTAGLIO!A2:A2000;id;CONTROLLO_PROJECT_DETTAGLIO!G2:G2000)
)
)
)
)


Ore_Complessive

=ARRAYFORMULA(
IF(A2:A500="";
"";
E2:E500 + J2:J500
)
)


Spese_Complessive

=ARRAYFORMULA(
IF(A2:A500="";
"";
F2:F500 + K2:K500
)
)


Incassi_Complessivi

=ARRAYFORMULA(
IF(A2:A500="";
"";
G2:G500 + L2:L500
)
)


Movimenti_Complessivi

=ARRAYFORMULA(
IF(A2:A500="";
"";
I2:I500 + M2:M500
)
)


------------------------------------------------
CONTROLLO_ENTITY_DETTAGLIO
------------------------------------------------

Funzione:
aggregazione movimenti entità figlie


Colonne:

Parent_Entity_ID
Entity_ID
Display_Name
Ore
Spese
Incassi
Movimenti


FORMULE


Relazione padre-figlio

=FILTER(
{ENTITIES!N2:N \ ENTITIES!A2:A};
ENTITIES!N2:N<>""
)


Display_Name

=ARRAYFORMULA(
IF(B2:B="";
"";
VLOOKUP(B2:B;ENTITIES!A:F;6;FALSE)
)
)


Ore

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;figlio;
RAW_DATA!G:G;"Tempo")
)
)
)
)


Spese

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;figlio;
RAW_DATA!G:G;"Spesa")
)
)
)
)


Incassi

=ARRAYFORMULA(
IF(B2:B="";
"";
MAP(B2:B;
LAMBDA(figlio;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;figlio;
RAW_DATA!G:G;"Incasso")
)
)
)
)


Movimenti

=ARRAYFORMULA(
IF(B2:B="";
"";
COUNTIF(RAW_DATA!F:F;B2:B)
)
)


------------------------------------------------
CONTROLLO_ENTITY
------------------------------------------------

Funzione:
vista aggregata completa delle entità


Colonne:

Entity_ID
Display_Name
Entity_Type
Totale_Ore
Totale_Spese
Totale_Incassi
Margine
Numero_Movimenti
Ore_Figli
Spese_Figli
Incassi_Figli
Numero_Movimenti_Figli
Ore_Complessive
Spese_Complessive
Incassi_Complessivi
Movimenti_Complessivi


FORMULE


Dataset entità

=FILTER(ENTITIES!A2:A; ENTITIES!A2:A<>"")


Display_Name

=ARRAYFORMULA(IF(A2:A="";"";VLOOKUP(A2:A;ENTITIES!A:F;6;FALSE)))


Entity_Type

=ARRAYFORMULA(IF(A2:A="";"";VLOOKUP(A2:A;ENTITIES!A:B;2;FALSE)))


Totale_Ore

=ARRAYFORMULA(
IF(A2:A="";
"";
MAP(A2:A;
LAMBDA(id;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;id;
RAW_DATA!G:G;"Tempo")
)
)
)
)


Totale_Spese

=ARRAYFORMULA(
IF(A2:A="";
"";
MAP(A2:A;
LAMBDA(id;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;id;
RAW_DATA!G:G;"Spesa")
)
)
)
)


Totale_Incassi

=ARRAYFORMULA(
IF(A2:A="";
"";
MAP(A2:A;
LAMBDA(id;
SUMIFS(RAW_DATA!H:H;
RAW_DATA!F:F;id;
RAW_DATA!G:G;"Incasso")
)
)
)
)


Margine

=ARRAYFORMULA(
IF(A2:A="";
"";
F2:F - E2:E
)
)


Numero_Movimenti

=ARRAYFORMULA(
IF(A2:A="";
"";
COUNTIF(RAW_DATA!F:F;A2:A)
)
)


Ore_Figli

=ARRAYFORMULA(
IF(A2:A="";
"";
SUMIF(CONTROLLO_ENTITY_DETTAGLIO!A:A;A2:A;CONTROLLO_ENTITY_DETTAGLIO!D:D)
)
)


Spese_Figli

=ARRAYFORMULA(
IF(A2:A="";
"";
SUMIF(CONTROLLO_ENTITY_DETTAGLIO!A:A;A2:A;CONTROLLO_ENTITY_DETTAGLIO!E:E)
)
)


Incassi_Figli

=ARRAYFORMULA(
IF(A2:A="";
"";
SUMIF(CONTROLLO_ENTITY_DETTAGLIO!A:A;A2:A;CONTROLLO_ENTITY_DETTAGLIO!F:F)
)
)


Numero_Movimenti_Figli

=ARRAYFORMULA(
IF(A2:A="";
"";
SUMIF(CONTROLLO_ENTITY_DETTAGLIO!A:A;A2:A;CONTROLLO_ENTITY_DETTAGLIO!G:G)
)
)


Ore_Complessive

=ARRAYFORMULA(IF(A2:A="";"";D2:D + I2:I))


Spese_Complessive

=ARRAYFORMULA(IF(A2:A="";"";E2:E + J2:J))


Incassi_Complessivi

=ARRAYFORMULA(IF(A2:A="";"";F2:F + K2:K))


Movimenti_Complessivi

=ARRAYFORMULA(IF(A2:A="";"";H2:H + L2:L))


------------------------------------------------
SYSTEM_LOG
------------------------------------------------

Funzione:
telemetria runtime del sistema


Colonne:

Timestamp
Event_Type
Sheet
Row
User
Details


Scrittura:

Kernel.gs

logEvent()
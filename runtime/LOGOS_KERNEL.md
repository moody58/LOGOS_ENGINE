# LOGOS\_KERNEL.md

\#DOC.LOGOS\_KERNEL

Runtime supervisor del sistema LOGOS.

Funzioni principali:

logEvent(eventType, sheetName, row, details)
LOGOS\_inputTrigger(e)
onEdit(e)

Responsabilità:

* Logging eventi
* Trigger ingestione RAW\_INPUT
* Protezione chiavi primarie
* Generazione ID entità e progetti
* Controlli gerarchici
* Timestamp modifiche

Chiavi generate:

MOV\_xxxxxx
PRJ\_xxxxxx
ENT\_xxxxxx

Dipendenze:

SYSTEM\_LOG
RAW\_DATA
PROJECTS
ENTITIES
ENTITY\_CONFIRMATION



\----------- Script completo



/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS — Apps Script Kernel

File: 0607\_LOGOS\_APPS\_SCRIPT\_KERNEL\_v1.5.gs

System: LOGOS Engine

Ecosystem: AIOS

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/





/\* =====================================================

SYSTEM LOG

Scrive eventi nel foglio SYSTEM\_LOG

===================================================== \*/



function logEvent(eventType, sheetName, row, details) {



&#x20; const ss = SpreadsheetApp.getActiveSpreadsheet();

&#x20; const logSheet = ss.getSheetByName("SYSTEM\_LOG");



&#x20; if (!logSheet) return;



&#x20; const user = Session.getActiveUser().getEmail();



&#x20; logSheet.appendRow(\[

&#x20;   new Date(),

&#x20;   eventType,

&#x20;   sheetName,

&#x20;   row,

&#x20;   user,

&#x20;   details

&#x20; ]);



}







/\* =====================================================

INPUT TRIGGER

Attiva automaticamente il processor quando viene

inserito un nuovo evento nella coda RAW\_INPUT

===================================================== \*/



function LOGOS\_inputTrigger(e) {



&#x20; if (!e || !e.range) return;



&#x20; const sheet = e.range.getSheet();

&#x20; const sheetName = sheet.getName();

&#x20; const row = e.range.getRow();



&#x20; if (sheetName !== "RAW\_INPUT") return;

&#x20; if (row === 1) return;



&#x20; const statusColumn = 12;

&#x20; const status = sheet.getRange(row, statusColumn).getValue();



&#x20; if (status === "NEW") {



&#x20;   logEvent(

&#x20;     "INPUT\_TRIGGER",

&#x20;     "RAW\_INPUT",

&#x20;     row,

&#x20;     "Nuovo evento rilevato"

&#x20;   );



&#x20;   processRawInputV2();



&#x20; }



}







/\* =====================================================

ON EDIT CORE HANDLER

Gestisce tutte le automazioni del sistema

===================================================== \*/



function onEdit(e) {



&#x20; if (!e || !e.range) return;



&#x20; /\* ---- attivazione trigger ingestione ---- \*/



&#x20; LOGOS\_inputTrigger(e);



&#x20; const sheet = e.range.getSheet();

&#x20; const sheetName = sheet.getName();

&#x20; const row = e.range.getRow();

&#x20; const col = e.range.getColumn();

&#x20; const props = PropertiesService.getDocumentProperties();

&#x20; const ss = e.source;



&#x20; if (row === 1) return;







/\* =====================================================

PROTEZIONE CHIAVI PRIMARIE

===================================================== \*/



&#x20; const protectedColumns = {

&#x20;   "RAW\_DATA": \[1,2],

&#x20;   "ENTITIES": \[1],

&#x20;   "PROJECTS": \[1]

&#x20; };



&#x20; if (protectedColumns\[sheetName] \&\& protectedColumns\[sheetName].includes(col)) {



&#x20;   if (e.oldValue !== undefined) {

&#x20;     e.range.setValue(e.oldValue);

&#x20;   } else {

&#x20;     e.range.clearContent();

&#x20;   }



&#x20;   SpreadsheetApp.getActive().toast(

&#x20;     "Modifica chiave primaria non consentita",

&#x20;     "Integrità Sistema",

&#x20;     3

&#x20;   );



&#x20;   logEvent(

&#x20;     "PRIMARY\_KEY\_PROTECTION",

&#x20;     sheetName,

&#x20;     row,

&#x20;     "Tentativo modifica chiave primaria"

&#x20;   );



&#x20;   return;



&#x20; }







/\* =====================================================

RAW\_DATA — CREAZIONE MOVIMENTO

===================================================== \*/



&#x20; if (sheetName === "RAW\_DATA") {



&#x20;   const idColumn = 1;

&#x20;   const timestampColumn = 2;

&#x20;   const projectColumn = 5;

&#x20;   const entityInputColumn = 4;

&#x20;   const entityIdColumn = 6;



&#x20;   const idCell = sheet.getRange(row, idColumn);

&#x20;   const timestampCell = sheet.getRange(row, timestampColumn);







/\* ---- Generazione ID movimento ---- \*/



&#x20;   if (col === projectColumn \&\& e.value) {



&#x20;     if (!timestampCell.getValue()) {



&#x20;       let lastNumber = parseInt(props.getProperty("LAST\_MOV\_NUMBER")) || 0;



&#x20;       const nextNumber = lastNumber + 1;



&#x20;       props.setProperty("LAST\_MOV\_NUMBER", nextNumber);



&#x20;       const newId = "MOV\_" + String(nextNumber).padStart(6, "0");



&#x20;       timestampCell.setValue(new Date());

&#x20;       idCell.setValue(newId);



&#x20;       logEvent(

&#x20;         "NEW\_MOVEMENT",

&#x20;         sheetName,

&#x20;         row,

&#x20;         "Creato " + newId

&#x20;       );



&#x20;     }



&#x20;   }







/\* =====================================================

ENTITY CONFIRMATION

===================================================== \*/



&#x20;   const entityInput = sheet.getRange(row, entityInputColumn).getValue();

&#x20;   const entityId = sheet.getRange(row, entityIdColumn).getValue();



&#x20;   if (entityInput \&\& !entityId) {



&#x20;     const confirmSheet = ss.getSheetByName("ENTITY\_CONFIRMATION");

&#x20;     if (!confirmSheet) return;



&#x20;     const lastRowConfirm = confirmSheet.getLastRow();



&#x20;     const existingInputs = lastRowConfirm > 1

&#x20;       ? confirmSheet.getRange("A2:A" + lastRowConfirm).getValues().flat()

&#x20;       : \[];



&#x20;     const normalizedInput = entityInput.toString().trim().toLowerCase();



&#x20;     const exists = existingInputs.some(v =>

&#x20;       v \&\& v.toString().trim().toLowerCase() === normalizedInput

&#x20;     );



&#x20;     if (!exists) {



&#x20;       confirmSheet.appendRow(\[

&#x20;         entityInput,

&#x20;         "",

&#x20;         "",

&#x20;         "",

&#x20;         "",

&#x20;         new Date(),

&#x20;         "PENDING"

&#x20;       ]);



&#x20;       logEvent(

&#x20;         "ENTITY\_CONFIRMATION\_REQUIRED",

&#x20;         sheetName,

&#x20;         row,

&#x20;         "Nuovo soggetto rilevato: " + entityInput

&#x20;       );



&#x20;     }



&#x20;   }



&#x20; }







/\* =====================================================

PROJECTS — CREAZIONE PROGETTO

===================================================== \*/



&#x20; if (sheetName === "PROJECTS") {



&#x20;   const idColumn = 1;

&#x20;   const nameColumn = 2;

&#x20;   const parentColumn = 3;

&#x20;   const createdColumn = 8;



&#x20;   if (col === nameColumn \&\& e.value) {



&#x20;     const idCell = sheet.getRange(row, idColumn);

&#x20;     const createdCell = sheet.getRange(row, createdColumn);



&#x20;     if (!idCell.getValue()) {



&#x20;       let lastProject = parseInt(props.getProperty("LAST\_PRJ\_NUMBER")) || 0;



&#x20;       const nextProject = lastProject + 1;



&#x20;       props.setProperty("LAST\_PRJ\_NUMBER", nextProject);



&#x20;       const newProjectId = "PRJ\_" + String(nextProject).padStart(6, "0");



&#x20;       idCell.setValue(newProjectId);

&#x20;       createdCell.setValue(new Date());



&#x20;       logEvent(

&#x20;         "PROJECT\_CREATED",

&#x20;         sheetName,

&#x20;         row,

&#x20;         newProjectId

&#x20;       );



&#x20;     }



&#x20;   }







/\* ---- Blocco auto-parent ---- \*/



&#x20;   if (col === parentColumn) {



&#x20;     const currentId = sheet.getRange(row, idColumn).getValue();

&#x20;     const parentValue = e.value;



&#x20;     if (currentId \&\& parentValue === currentId) {



&#x20;       e.range.clearContent();



&#x20;       SpreadsheetApp.getActive().toast(

&#x20;         "Un progetto non può essere padre di sé stesso",

&#x20;         "Errore Gerarchia",

&#x20;         3

&#x20;       );



&#x20;       logEvent(

&#x20;         "PROJECT\_HIERARCHY\_ERROR",

&#x20;         sheetName,

&#x20;         row,

&#x20;         "Auto-parent"

&#x20;       );



&#x20;     }



&#x20;   }



&#x20; }







/\* =====================================================

ENTITIES — CREAZIONE ENTITÀ

===================================================== \*/



&#x20; if (sheetName === "ENTITIES") {



&#x20;   const idColumn = 1;

&#x20;   const typeColumn = 2;

&#x20;   const parentColumn = 14;

&#x20;   const createdColumn = 15;

&#x20;   const modifiedColumn = 16;



&#x20;   const idCell = sheet.getRange(row, idColumn);

&#x20;   const createdCell = sheet.getRange(row, createdColumn);

&#x20;   const modifiedCell = sheet.getRange(row, modifiedColumn);







/\* ---- Generazione ID entità ---- \*/



&#x20;   if (col === typeColumn \&\& e.value \&\& !idCell.getValue()) {



&#x20;     let lastEntity = parseInt(props.getProperty("LAST\_ENT\_NUMBER")) || 0;



&#x20;     const nextEntity = lastEntity + 1;



&#x20;     props.setProperty("LAST\_ENT\_NUMBER", nextEntity);



&#x20;     const newEntityId = "ENT\_" + String(nextEntity).padStart(6, "0");



&#x20;     idCell.setValue(newEntityId);

&#x20;     createdCell.setValue(new Date());



&#x20;     logEvent(

&#x20;       "ENTITY\_CREATED",

&#x20;       sheetName,

&#x20;       row,

&#x20;       newEntityId

&#x20;     );



&#x20;     return;



&#x20;   }







/\* ---- Blocco auto-parent ---- \*/



&#x20;   if (col === parentColumn) {



&#x20;     const currentId = idCell.getValue();

&#x20;     const parentValue = e.value;



&#x20;     if (currentId \&\& parentValue === currentId) {



&#x20;       e.range.clearContent();



&#x20;       SpreadsheetApp.getActive().toast(

&#x20;         "Un'entità non può essere padre di sé stessa",

&#x20;         "Errore Gerarchia",

&#x20;         3

&#x20;       );



&#x20;       logEvent(

&#x20;         "ENTITY\_HIERARCHY\_ERROR",

&#x20;         sheetName,

&#x20;         row,

&#x20;         "Auto-parent"

&#x20;       );



&#x20;       return;



&#x20;     }



&#x20;   }







/\* ---- Timestamp modifica ---- \*/



&#x20;   if (

&#x20;     idCell.getValue() \&\&

&#x20;     col !== idColumn \&\&

&#x20;     col !== createdColumn \&\&

&#x20;     col !== modifiedColumn

&#x20;   ) {



&#x20;     modifiedCell.setValue(new Date());



&#x20;   }



&#x20; }



}


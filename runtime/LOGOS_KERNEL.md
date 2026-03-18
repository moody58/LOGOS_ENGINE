/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS — Apps Script Kernel CLEAN

Versione: v2.0 (Baserow-ready)

Ruolo: Orchestrazione eventi + protezioni

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/





/\* =====================================================

SYSTEM LOG

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

INPUT TRIGGER (UNICA AUTORITÀ PROCESSOR)

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

===================================================== \*/



function onEdit(e) {



&#x20; if (!e || !e.range) return;



&#x20; const sheet = e.range.getSheet();

&#x20; const sheetName = sheet.getName();

&#x20; const row = e.range.getRow();

&#x20; const col = e.range.getColumn();

&#x20; const props = PropertiesService.getDocumentProperties();

&#x20; const ss = e.source;



&#x20; if (row === 1) return;





&#x20; /\* =====================================================

&#x20; RAW\_INPUT → TRIGGER PROCESSOR

&#x20; ===================================================== \*/



&#x20; if (sheetName === "RAW\_INPUT") {

&#x20;   LOGOS\_inputTrigger(e);

&#x20;   return;

&#x20; }





&#x20; /\* =====================================================

&#x20; PROTEZIONE CHIAVI PRIMARIE

&#x20; ===================================================== \*/



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





&#x20; /\* =====================================================

&#x20; RAW\_DATA — ENTITY CONFIRMATION

&#x20; ===================================================== \*/



&#x20; if (sheetName === "RAW\_DATA") {



&#x20;   const entityInputColumn = 4;

&#x20;   const entityIdColumn = 6;



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





&#x20; /\* =====================================================

&#x20; ENTITY\_CONFIRMATION — COMMIT + REPROCESS

&#x20; ===================================================== \*/



&#x20; if (sheetName === "ENTITY\_CONFIRMATION") {



&#x20;   if (col !== 4 || row < 2) return;



&#x20;   const value = (e.value || "").toString().trim();

&#x20;   if (value !== "Confirm") return;



&#x20;   LOGOS\_commitSingleEntity(row);



&#x20;   logEvent(

&#x20;     "AUTO\_REPROCESS\_TRIGGER",

&#x20;     "ENTITY\_CONFIRMATION",

&#x20;     row,

&#x20;     "Reprocess automatico avviato"

&#x20;   );



&#x20;   processRawInputV2();



&#x20;   return;

&#x20; }





&#x20; /\* =====================================================

&#x20; PROJECTS — CREAZIONE PROGETTO

&#x20; ===================================================== \*/



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



&#x20;       logEvent("PROJECT\_CREATED", sheetName, row, newProjectId);

&#x20;     }

&#x20;   }



&#x20;   if (col === parentColumn) {



&#x20;     const currentId = sheet.getRange(row, idColumn).getValue();



&#x20;     if (currentId \&\& e.value === currentId) {



&#x20;       e.range.clearContent();



&#x20;       SpreadsheetApp.getActive().toast(

&#x20;         "Un progetto non può essere padre di sé stesso",

&#x20;         "Errore Gerarchia",

&#x20;         3

&#x20;       );



&#x20;       logEvent("PROJECT\_HIERARCHY\_ERROR", sheetName, row, "Auto-parent");

&#x20;     }

&#x20;   }

&#x20; }





&#x20; /\* =====================================================

&#x20; ENTITIES — METADATA

&#x20; ===================================================== \*/



&#x20; if (sheetName === "ENTITIES") {



&#x20;   const idColumn = 1;

&#x20;   const parentColumn = 14;

&#x20;   const createdColumn = 15;

&#x20;   const modifiedColumn = 16;



&#x20;   const idCell = sheet.getRange(row, idColumn);

&#x20;   const modifiedCell = sheet.getRange(row, modifiedColumn);



&#x20;   if (col === parentColumn) {



&#x20;     const currentId = idCell.getValue();



&#x20;     if (currentId \&\& e.value === currentId) {



&#x20;       e.range.clearContent();



&#x20;       SpreadsheetApp.getActive().toast(

&#x20;         "Un'entità non può essere padre di sé stessa",

&#x20;         "Errore Gerarchia",

&#x20;         3

&#x20;       );



&#x20;       logEvent("ENTITY\_HIERARCHY\_ERROR", sheetName, row, "Auto-parent");

&#x20;       return;

&#x20;     }

&#x20;   }



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


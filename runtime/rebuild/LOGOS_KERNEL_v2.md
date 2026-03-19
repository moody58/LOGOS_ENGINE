/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS — Apps Script Kernel (CLEAN)

Versione: v2.2 HARDENED SAFE

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/



/\* ================= LOG ================= \*/



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





/\* ================= INPUT TRIGGER ================= \*/



function LOGOS\_inputTrigger(e) {



&#x20; if (!e || !e.range) return;



&#x20; const sheet = e.range.getSheet();

&#x20; const name = sheet.getName();

&#x20; const row = e.range.getRow();



&#x20; if (name !== "RAW\_INPUT") return;

&#x20; if (row < 2) return;



&#x20; const status = (sheet.getRange(row, 12).getValue() || "")

&#x20;   .toString()

&#x20;   .trim()

&#x20;   .toUpperCase();



&#x20; // PROCESSA SOLO SE NEW

&#x20; if (status === "NEW" || status === "ENTITY\_PENDING") {



&#x20;   logEvent(

&#x20;     "INPUT\_TRIGGER",

&#x20;     "RAW\_INPUT",

&#x20;     row,

&#x20;     "Nuovo evento"

&#x20;   );



&#x20;   processRawInputV2();

&#x20; }

}





/\* ================= MAIN HANDLER ================= \*/



function onEdit(e) {



&#x20; if (!e || !e.range) return;



&#x20; const sheet = e.range.getSheet();

&#x20; const name = sheet.getName();

&#x20; const row = e.range.getRow();

&#x20; const col = e.range.getColumn();



&#x20; if (row < 2) return;



&#x20; /\* ================= RAW INPUT ================= \*/



&#x20; if (name === "RAW\_INPUT") {

&#x20;   LOGOS\_inputTrigger(e);

&#x20;   return;

&#x20; }



&#x20; /\* ================= ENTITY CONFIRMATION ================= \*/



&#x20; if (name === "ENTITY\_CONFIRMATION") {



&#x20;   // sicurezza: solo colonna Confirm (D = 4)

&#x20;   if (col !== 4) return;



&#x20;   const value = (e.value || "")

&#x20;     .toString()

&#x20;     .trim();



&#x20;   if (value !== "Confirm") return;



&#x20;   logEvent(

&#x20;     "ENTITY\_CONFIRM\_TRIGGER",

&#x20;     "ENTITY\_CONFIRMATION",

&#x20;     row,

&#x20;     "Confirm ricevuto"

&#x20;   );



&#x20;   // commit entità

&#x20;   LOGOS\_commitSingleEntity(row);



&#x20;   logEvent(

&#x20;     "AUTO\_REPROCESS\_TRIGGER",

&#x20;     "ENTITY\_CONFIRMATION",

&#x20;     row,

&#x20;     "Reprocess automatico"

&#x20;   );



&#x20;   // rilancia processor

&#x20;   processRawInputV2();



&#x20;   return;

&#x20; }

}


function LOGOS\_ingestEvent(event) {



&#x20; const ss = SpreadsheetApp.getActiveSpreadsheet();

&#x20; const sheet = ss.getSheetByName("RAW\_INPUT");



&#x20; const now = new Date();



&#x20; /\* ================= NORMALIZATION ================= \*/



&#x20; const normalized = LOGOS\_normalizeEvent(event);



&#x20; /\* ================= VALIDATION (SOFT) ================= \*/



&#x20; const validation = LOGOS\_validateEvent(normalized);



&#x20; if (validation.warnings.length > 0) {



&#x20;   logEvent(

&#x20;     "INPUT\_WARNING",

&#x20;     "RAW\_INPUT",

&#x20;     0,

&#x20;     validation.warnings.join("|")

&#x20;   );

&#x20; }



&#x20; /\* ================= WRITE RAW\_INPUT ================= \*/



&#x20; sheet.appendRow(\[

&#x20;   now,

&#x20;   normalized.project\_name,

&#x20;   normalized.tipo,

&#x20;   normalized.valore,

&#x20;   normalized.data\_evento,

&#x20;   normalized.causale,

&#x20;   normalized.riferimento,

&#x20;   normalized.metodo\_pagamento,

&#x20;   normalized.note,

&#x20;   normalized.soggetto,

&#x20;   normalized.source,

&#x20;   "NEW"

&#x20; ]);

}





/\* ================= TEST ================= \*/



function LOGOS\_testInsert() {



&#x20; LOGOS\_ingestEvent({

&#x20;   project\_name: "TEST\_PROJECT",

&#x20;   tipo: "Spesa",

&#x20;   valore: 100,

&#x20;   data\_evento: new Date(),

&#x20;   causale: "Test",

&#x20;   soggetto: "Test User"

&#x20; });

}



function TEST\_validationWarning() {



&#x20; LOGOS\_ingestEvent({

&#x20;   project\_name: "",

&#x20;   soggetto: "",

&#x20;   valore: ""

&#x20; });



}



function TEST\_STP\_1\_dirtyInput() {



&#x20; LOGOS\_ingestEvent({

&#x20;   project\_name: "  TEST\_PROJECT  ",

&#x20;   tipo: "  Spesa  ",

&#x20;   valore: "10,5",

&#x20;   data\_evento: "2026-03-19 15:30",

&#x20;   causale: "  Test sporco  ",

&#x20;   soggetto: "  Mario   ",

&#x20;   source: "TEST\_STP"

&#x20; });



}


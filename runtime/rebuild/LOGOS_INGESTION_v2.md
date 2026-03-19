function LOGOS\_ingestEvent(event) {



&#x20; const ss = SpreadsheetApp.getActiveSpreadsheet();

&#x20; const sheet = ss.getSheetByName("RAW\_INPUT");



&#x20; const now = new Date();



&#x20; /\* ================= NORMALIZATION ================= \*/



&#x20; let normalized = {};



&#x20; if (typeof LOGOS\_normalizeEvent === "function") {

&#x20;   normalized = LOGOS\_normalizeEvent(event);

&#x20; } else {

&#x20;   // fallback di sicurezza (non rompe sistema)

&#x20;   normalized = event || {};

&#x20; }



&#x20; /\* ================= VALIDATION (SOFT) ================= \*/



&#x20; let validation = { warnings: \[] };



&#x20; if (typeof LOGOS\_validateEvent === "function") {

&#x20;   validation = LOGOS\_validateEvent(normalized);

&#x20; }



&#x20; if (validation.warnings \&\& validation.warnings.length > 0) {



&#x20;   logEvent(

&#x20;     "INPUT\_WARNING",

&#x20;     "RAW\_INPUT",

&#x20;     0,

&#x20;     validation.warnings.join("|")

&#x20;   );

&#x20; }



&#x20; /\* ================= SAFE VALUES ================= \*/



&#x20; const safe = (v) => v || "";



&#x20; /\* ================= WRITE RAW\_INPUT ================= \*/



&#x20; sheet.appendRow(\[

&#x20;   now,

&#x20;   safe(normalized.project\_name),

&#x20;   safe(normalized.tipo),

&#x20;   safe(normalized.valore),

&#x20;   safe(normalized.data\_evento),

&#x20;   safe(normalized.causale),

&#x20;   safe(normalized.riferimento),

&#x20;   safe(normalized.metodo\_pagamento),

&#x20;   safe(normalized.note),

&#x20;   safe(normalized.soggetto),

&#x20;   safe(normalized.source || "MANUAL"),

&#x20;   "NEW"

&#x20; ]);



&#x20; processRawInputV2();

&#x20; 

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


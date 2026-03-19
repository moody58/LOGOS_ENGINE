/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS ENGINE — INPUT PROCESSOR

Versione: v1.8 FINAL STABLE

Pipeline: RAW\_INPUT → PROCESSOR → RAW\_DATA

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/



function processRawInputV2() {



&#x20; logEvent("INPUT\_PROCESSOR\_START","RAW\_INPUT",0,"Processor avviato");



&#x20; const MAX\_EVENTS\_PER\_RUN = 50;

&#x20; let processedCount = 0;



&#x20; const ss = SpreadsheetApp.getActive();



&#x20; const inputSheet = ss.getSheetByName("RAW\_INPUT");

&#x20; const ledgerSheet = ss.getSheetByName("RAW\_DATA");

&#x20; const projectsSheet = ss.getSheetByName("PROJECTS");

&#x20; const entitiesSheet = ss.getSheetByName("ENTITIES");



&#x20; const props = PropertiesService.getDocumentProperties();



&#x20; const lastRow = inputSheet.getLastRow();

&#x20; const lastCol = inputSheet.getLastColumn();



&#x20; if (lastRow < 2) return;



&#x20; const startRow = 2;

&#x20; const numRows = lastRow - startRow + 1;



&#x20; if (numRows <= 0 || startRow > lastRow) {

&#x20;   logEvent("INPUT\_BATCH\_COMPLETED","RAW\_INPUT",0,"0 eventi processati");

&#x20;   return;

&#x20; }



&#x20; const headers = inputSheet.getRange(1,1,1,lastCol).getValues()\[0];

&#x20; const inputData = inputSheet.getRange(startRow,1,numRows,lastCol).getValues();



&#x20; const headerIndex = {};

&#x20; headers.forEach((h,i)=> headerIndex\[h] = i);



&#x20; /\* ================= MAPPE ================= \*/



&#x20; let projects = \[];

&#x20; const projectsLastRow = projectsSheet.getLastRow();



&#x20; if (projectsLastRow > 1) {

&#x20;   projects = projectsSheet.getRange(2,1,projectsLastRow - 1,2).getValues();

&#x20; }



&#x20; const projectMap = buildProjectMap(projects);



&#x20; let entities = \[];

&#x20; const entitiesLastRow = entitiesSheet.getLastRow();



&#x20; if (entitiesLastRow > 1) {

&#x20;   entities = entitiesSheet.getRange(2,1,entitiesLastRow - 1,6).getValues();

&#x20; }



&#x20; const entityMap = buildEntityMap(entities);



&#x20; /\* ================= BUFFER ================= \*/



&#x20; const batchFingerprints = new Set();

&#x20; const ledgerBuffer = \[];

&#x20; const statusUpdates = \[];



&#x20; /\* ================= LOOP ================= \*/



&#x20; for (let r = 0; r < inputData.length; r++) {



&#x20;   if (processedCount >= MAX\_EVENTS\_PER\_RUN) break;



&#x20;   const row = inputData\[r];



&#x20;   const status = (row\[headerIndex\["Status"]] || "").toString().trim();



&#x20;   if (status !== "NEW" \&\& status !== "ENTITY\_PENDING") continue;



&#x20;   const event = {

&#x20;     projectName: row\[headerIndex\["Project\_Name"]],

&#x20;     tipo: row\[headerIndex\["Tipo\_Movimento"]],

&#x20;     valore: row\[headerIndex\["Valore"]],

&#x20;     dataEvento: row\[headerIndex\["Data\_Evento"]],

&#x20;     causale: row\[headerIndex\["Causale"]],

&#x20;     riferimento: row\[headerIndex\["Riferimento\_ID"]],

&#x20;     metodoPagamento: row\[headerIndex\["Metodo\_Pagamento"]],

&#x20;     note: row\[headerIndex\["Note"]],

&#x20;     soggetto: row\[headerIndex\["Soggetto"]],

&#x20;     source: row\[headerIndex\["Source"]]

&#x20;   };



&#x20;   const normalizedEvent = normalizeEvent({

&#x20;     project\_name: event.projectName,

&#x20;     tipo: event.tipo,

&#x20;     valore: event.valore,

&#x20;     data\_evento: event.dataEvento,

&#x20;     causale: event.causale,

&#x20;     riferimento: event.riferimento,

&#x20;     metodo\_pagamento: event.metodoPagamento,

&#x20;     note: event.note,

&#x20;     soggetto: event.soggetto,

&#x20;     source: event.source

&#x20;   });



&#x20;   /\* ================= VALIDAZIONE ================= \*/



&#x20;   const validation = validationEngine(normalizedEvent, projectMap);



&#x20;   if (!validation.valid) {

&#x20;     logEvent("INPUT\_ERROR\_PROJECT","RAW\_INPUT",startRow + r,"Progetto non trovato: " + event.projectName);

&#x20;     statusUpdates.push(\[startRow + r, "ERROR\_PROJECT"]);

&#x20;     continue;

&#x20;   }



&#x20;   /\* ================= ENTITY RESOLUTION ================= \*/



&#x20;   const inputText = (normalizedEvent.soggetto || "").toString().trim();

&#x20;   const inputKey = inputText.toLowerCase();



&#x20;   let suggestions = null;



&#x20;   if (inputText) {

&#x20;     suggestions = suggestEntity(inputText, entityMap);

&#x20;   }



&#x20;   let entityResult = entityResolutionEngine(normalizedEvent, entityMap);



&#x20;   /\* ---- AUTO MATCH ---- \*/



&#x20;   if (entityResult.status !== "OK" \&\& suggestions \&\& suggestions.length > 0) {



&#x20;     const bestMatch = suggestions\[0];



&#x20;     if (bestMatch.score >= 0.85) {



&#x20;       entityResult = {

&#x20;         status: "OK",

&#x20;         entityId: bestMatch.id

&#x20;       };



&#x20;       logEvent(

&#x20;         "ENTITY\_AUTO\_MATCH",

&#x20;         "RAW\_INPUT",

&#x20;         startRow + r,

&#x20;         "Match automatico: " + inputText + " → " + bestMatch.name

&#x20;       );

&#x20;     }

&#x20;   }



&#x20;   /\* ---- ENTITY PENDING ---- \*/



&#x20;   if (entityResult.status !== "OK") {



&#x20;     if (status === "NEW") {



&#x20;       logEvent(

&#x20;         "INPUT\_ENTITY\_PENDING",

&#x20;         "RAW\_INPUT",

&#x20;         startRow + r,

&#x20;         "Entità non trovata: " + inputText

&#x20;       );



&#x20;       const entitySheet = ss.getSheetByName("ENTITY\_CONFIRMATION");



&#x20;       if (entitySheet) {



&#x20;         const lastRowEntity = entitySheet.getLastRow();



&#x20;         let existing = \[];



&#x20;         if (lastRowEntity > 1) {

&#x20;           existing = entitySheet.getRange(2,1,lastRowEntity - 1,1).getValues();

&#x20;         }



&#x20;         let alreadyExists = false;



&#x20;         for (let i = 0; i < existing.length; i++) {



&#x20;           const existingText = (existing\[i]\[0] || "")

&#x20;             .toString()

&#x20;             .trim()

&#x20;             .toLowerCase();



&#x20;           if (existingText === inputKey) {

&#x20;             alreadyExists = true;

&#x20;             break;

&#x20;           }

&#x20;         }



&#x20;         if (!alreadyExists \&\& inputText !== "") {



&#x20;           let suggestedName = "";

&#x20;           let suggestedId = "";



&#x20;           if (suggestions \&\& suggestions.length > 0) {

&#x20;             suggestedName = suggestions\[0].name;

&#x20;             suggestedId = suggestions\[0].id;

&#x20;           }



&#x20;           entitySheet.appendRow(\[

&#x20;             inputText,

&#x20;             suggestedName,

&#x20;             suggestedId,

&#x20;             "",

&#x20;             "",

&#x20;             new Date(),

&#x20;             suggestedId ? "SUGGESTED" : "PENDING"

&#x20;           ]);

&#x20;         }

&#x20;       }



&#x20;       statusUpdates.push(\[startRow + r, "ENTITY\_PENDING"]);

&#x20;       continue;

&#x20;     }



&#x20;     if (status === "ENTITY\_PENDING") {

&#x20;       continue;

&#x20;     }

&#x20;   }



&#x20;   /\* ================= FINGERPRINT ================= \*/



&#x20;   const fingerprint = generateEventFingerprint(

&#x20;     normalizedEvent,

&#x20;     validation.projectId,

&#x20;     entityResult.entityId

&#x20;   );



&#x20;   if (batchFingerprints.has(fingerprint)) {



&#x20;     logEvent("INPUT\_DUPLICATE\_SKIPPED","RAW\_INPUT",startRow + r,"Duplicato tecnico nello stesso batch");

&#x20;     statusUpdates.push(\[startRow + r, "DUPLICATE"]);

&#x20;     continue;

&#x20;   }



&#x20;   batchFingerprints.add(fingerprint);



&#x20;   /\* ================= LEDGER ================= \*/



&#x20;   ledgerBuffer.push(\[

&#x20;     LOGOS\_generateMovementID(),

&#x20;     new Date(),

&#x20;     normalizedEvent.dataEvento,

&#x20;     normalizedEvent.soggetto,

&#x20;     validation.projectId,

&#x20;     entityResult.entityId,

&#x20;     normalizedEvent.tipo,

&#x20;     normalizedEvent.valore,

&#x20;     "",

&#x20;     normalizedEvent.causale,

&#x20;     "",

&#x20;     normalizedEvent.riferimento,

&#x20;     normalizedEvent.source,

&#x20;     normalizedEvent.metodoPagamento,

&#x20;     normalizedEvent.note

&#x20;   ]);



&#x20;   statusUpdates.push(\[startRow + r, "WRITTEN"]);

&#x20;   processedCount++;



&#x20; }



&#x20; /\* ================= SCRITTURA ================= \*/



&#x20; if (ledgerBuffer.length > 0) {



&#x20;   const startRowLedger = ledgerSheet.getLastRow() + 1;



&#x20;   ledgerSheet

&#x20;     .getRange(startRowLedger,1,ledgerBuffer.length,ledgerBuffer\[0].length)

&#x20;     .setValues(ledgerBuffer);



&#x20;   logEvent("INPUT\_WRITTEN","RAW\_DATA",startRowLedger,ledgerBuffer.length + " eventi scritti");

&#x20; }



&#x20; /\* ================= STATUS ================= \*/



&#x20; if (statusUpdates.length > 0) {



&#x20;   const statusCol = headerIndex\["Status"] + 1;



&#x20;   const statusRange = inputSheet

&#x20;     .getRange(startRow, statusCol, inputData.length, 1)

&#x20;     .getValues();



&#x20;   statusUpdates.forEach(function(update){

&#x20;     const rowIndex = update\[0] - startRow;

&#x20;     statusRange\[rowIndex]\[0] = update\[1];

&#x20;   });



&#x20;   inputSheet

&#x20;     .getRange(startRow, statusCol, inputData.length, 1)

&#x20;     .setValues(statusRange);

&#x20; }



&#x20; logEvent("INPUT\_BATCH\_COMPLETED","RAW\_INPUT",0,processedCount + " eventi processati");

}


/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS ENGINE — INPUT PROCESSOR

Versione: v1.8

Pipeline: RAW\_INPUT → PROCESSOR → RAW\_DATA

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/





/\* =====================================================

PROCESSOR PRINCIPALE

===================================================== \*/



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





/\* =====================================================

QUEUE CURSOR

===================================================== \*/



&#x20; const lastRow = inputSheet.getLastRow();

&#x20; const lastCol = inputSheet.getLastColumn();



&#x20; if (lastRow < 2) return;



&#x20; const startRow = 2;

&#x20; const numRows = lastRow - startRow + 1;



// =====================================================

// SAFETY CHECK — evita range invalidi

// =====================================================



if (numRows <= 0 || startRow > lastRow) {



&#x20; logEvent(

&#x20;   "INPUT\_BATCH\_COMPLETED",

&#x20;   "RAW\_INPUT",

&#x20;   0,

&#x20;   "0 eventi processati"

&#x20; );



&#x20; return;



}





/\* =====================================================

HEADER MAPPING

===================================================== \*/



&#x20; const headers = inputSheet.getRange(1,1,1,lastCol).getValues()\[0];

&#x20; const inputData = inputSheet.getRange(startRow,1,numRows,lastCol).getValues();



&#x20; const headerIndex = {};

&#x20; headers.forEach((h,i)=> headerIndex\[h] = i);





/\* =====================================================

MAPPE PROGETTI / ENTITÀ (ottimizzato)

===================================================== \*/



let projects = \[];



const projectsLastRow = projectsSheet.getLastRow();



if (projectsLastRow > 1) {



&#x20; projects = projectsSheet

&#x20;   .getRange(2,1,projectsLastRow - 1,2)

&#x20;   .getValues();



}



// SEMPRE fuori dall'if

const projectMap = buildProjectMap(projects);



let entities = \[];



const entitiesLastRow = entitiesSheet.getLastRow();



if (entitiesLastRow > 1) {



&#x20; entities = entitiesSheet

&#x20;   .getRange(2,1,entitiesLastRow - 1,6)

&#x20;   .getValues();



}



// SEMPRE fuori dall'if

const entityMap = buildEntityMap(entities);





/\* =====================================================

FINGERPRINT SET

===================================================== \*/



&#x20; const batchFingerprints = new Set();





/\* =====================================================

BUFFER SCRITTURA

===================================================== \*/



&#x20; const ledgerBuffer = \[];

&#x20; const statusUpdates = \[];





/\* =====================================================

PROCESSAMENTO CODA

===================================================== \*/



&#x20; for (let r = 0; r < inputData.length; r++) {



&#x20;   if (processedCount >= MAX\_EVENTS\_PER\_RUN) break;



&#x20;   const row = inputData\[r];



&#x20;   const status = (row\[headerIndex\["Status"]] || "")

&#x20;     .toString()

&#x20;     .trim();



// =====================================================

// STATUS FILTER (NEW + ENTITY\_PENDING)

// =====================================================



if (status !== "NEW" \&\& status !== "ENTITY\_PENDING") continue;



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





/\* =====================================================

VALIDAZIONE PROGETTO

===================================================== \*/



&#x20;   const validation = validationEngine(event, projectMap);



&#x20;   if (!validation.valid) {



&#x20;     logEvent(

&#x20;       "INPUT\_ERROR\_PROJECT",

&#x20;       "RAW\_INPUT",

&#x20;       startRow + r,

&#x20;       "Progetto non trovato: " + event.projectName

&#x20;     );



&#x20;     statusUpdates.push(\[startRow + r, "ERROR\_PROJECT"]);

&#x20;     continue;



&#x20;   }





/\* =====================================================

RISOLUZIONE ENTITÀ

===================================================== \*/



const entityResult = entityResolutionEngine(event, entityMap);



if (entityResult.status !== "OK") {



&#x20; // =====================================================

&#x20; // CASO 1 → NUOVO EVENTO (NEW)

&#x20; // =====================================================

&#x20; if (status === "NEW") {



&#x20;   logEvent(

&#x20;     "INPUT\_ENTITY\_PENDING",

&#x20;     "RAW\_INPUT",

&#x20;     startRow + r,

&#x20;     "Entità non trovata: " + event.soggetto

&#x20;   );



&#x20;   const entitySheet = ss.getSheetByName("ENTITY\_CONFIRMATION");



&#x20;   if (entitySheet) {



&#x20;     const lastRowEntity = entitySheet.getLastRow();



&#x20;     let existing = \[];



&#x20;     if (lastRowEntity > 1) {

&#x20;       existing = entitySheet

&#x20;         .getRange(2, 1, lastRowEntity - 1, 1)

&#x20;         .getValues();

&#x20;     }



&#x20;     const inputTextRaw = event.soggetto || "";

&#x20;     const inputText = inputTextRaw.toString().trim();

&#x20;     const inputKey = inputText.toLowerCase();



&#x20;     let alreadyExists = false;



&#x20;     for (let i = 0; i < existing.length; i++) {



&#x20;       const existingText = (existing\[i]\[0] || "")

&#x20;         .toString()

&#x20;         .trim()

&#x20;         .toLowerCase();



&#x20;       if (existingText === inputKey) {

&#x20;         alreadyExists = true;

&#x20;         break;

&#x20;       }



&#x20;     }



&#x20;     if (!alreadyExists \&\& inputText !== "") {



&#x20;       let suggestedName = "";

&#x20;       let suggestedId = "";



&#x20;       if (entityMap\[inputKey]) {



&#x20;         suggestedName = inputText;

&#x20;         suggestedId = entityMap\[inputKey];



&#x20;       } else {



&#x20;         for (let key in entityMap) {



&#x20;           if (

&#x20;             key.includes(inputKey) ||

&#x20;             inputKey.includes(key)

&#x20;           ) {



&#x20;             suggestedName = key;

&#x20;             suggestedId = entityMap\[key];

&#x20;             break;



&#x20;           }



&#x20;         }



&#x20;       }



&#x20;       entitySheet.appendRow(\[

&#x20;         inputText,

&#x20;         suggestedName,

&#x20;         suggestedId,

&#x20;         "",

&#x20;         "",

&#x20;         new Date(),

&#x20;         suggestedId ? "SUGGESTED" : "PENDING"

&#x20;       ]);



&#x20;     }



&#x20;   } // 



&#x20;   statusUpdates.push(\[startRow + r, "ENTITY\_PENDING"]);

&#x20;   continue;



&#x20; }



&#x20; // =====================================================

&#x20; // CASO 2 → GIÀ ENTITY\_PENDING

&#x20; // =====================================================

&#x20; if (status === "ENTITY\_PENDING") {

&#x20;   continue;

&#x20; }



}





/\* =====================================================

FINGERPRINT EVENTO

===================================================== \*/



&#x20;   const fingerprint = generateEventFingerprint(

&#x20;     event,

&#x20;     validation.projectId,

&#x20;     entityResult.entityId

&#x20;   );



&#x20;   if (batchFingerprints.has(fingerprint)) {



&#x20;     logEvent(

&#x20;       "INPUT\_DUPLICATE\_SKIPPED",

&#x20;       "RAW\_INPUT",

&#x20;       startRow + r,

&#x20;       "Duplicato tecnico nello stesso batch"

&#x20;     );



&#x20;     statusUpdates.push(\[startRow + r, "DUPLICATE"]);

&#x20;     continue;



&#x20;   }



&#x20;   batchFingerprints.add(fingerprint);





/\* =====================================================

BUFFER LEDGER

===================================================== \*/



&#x20;   ledgerBuffer.push(\[



&#x20;     LOGOS\_generateMovementID(),

&#x20;     new Date(),

&#x20;     event.dataEvento,

&#x20;     event.soggetto,

&#x20;     validation.projectId,

&#x20;     entityResult.entityId,

&#x20;     event.tipo,

&#x20;     event.valore,

&#x20;     "",

&#x20;     event.causale,

&#x20;     "",

&#x20;     event.riferimento,

&#x20;     event.source,

&#x20;     event.metodoPagamento,

&#x20;     event.note



&#x20;   ]);



&#x20;   statusUpdates.push(\[startRow + r, "WRITTEN"]);

&#x20;   processedCount++;



&#x20; }





/\* =====================================================

SCRITTURA LEDGER

===================================================== \*/



&#x20; if (ledgerBuffer.length > 0) {



&#x20;   const startRowLedger = ledgerSheet.getLastRow() + 1;



&#x20;   ledgerSheet

&#x20;     .getRange(

&#x20;       startRowLedger,

&#x20;       1,

&#x20;       ledgerBuffer.length,

&#x20;       ledgerBuffer\[0].length

&#x20;     )

&#x20;     .setValues(ledgerBuffer);



&#x20;   logEvent(

&#x20;     "INPUT\_WRITTEN",

&#x20;     "RAW\_DATA",

&#x20;     startRowLedger,

&#x20;     ledgerBuffer.length + " eventi scritti"

&#x20;   );



&#x20; }





/\* =====================================================

AGGIORNAMENTO STATUS CODA (batch)

===================================================== \*/



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





/\* =====================================================

AGGIORNAMENTO CURSORE

===================================================== \*/



&#x20; //props.setProperty("LOGOS\_QUEUE\_CURSOR", lastRow);





&#x20; logEvent(

&#x20;   "INPUT\_BATCH\_COMPLETED",

&#x20;   "RAW\_INPUT",

&#x20;   0,

&#x20;   processedCount + " eventi processati"

&#x20; );



}







/\* =====================================================

VALIDATION ENGINE

===================================================== \*/



function validationEngine(event, projectMap) {



&#x20; const key = event.projectName

&#x20;   ? event.projectName.toString().trim().toLowerCase()

&#x20;   : "";



&#x20; const projectId = projectMap\[key];



&#x20; if (!projectId) {

&#x20;   return { valid:false };

&#x20; }



&#x20; return { valid:true, projectId:projectId };



}







/\* =====================================================

ENTITY RESOLUTION ENGINE

===================================================== \*/



function entityResolutionEngine(event, entityMap) {



&#x20; const key = event.soggetto

&#x20;   ? event.soggetto.toString().trim().toLowerCase()

&#x20;   : "";



&#x20; const entityId = entityMap\[key];



&#x20; if (!entityId) {

&#x20;   return { status:"ENTITY\_PENDING" };

&#x20; }



&#x20; return { status:"OK", entityId:entityId };



}







/\* =====================================================

BUILD PROJECT MAP

===================================================== \*/



function buildProjectMap(projects) {



&#x20; const map = {};



&#x20; for (let i = 0; i < projects.length; i++) {



&#x20;   const id = projects\[i]\[0];

&#x20;   const name = projects\[i]\[1];



&#x20;   if (name) {

&#x20;     map\[name.toString().trim().toLowerCase()] = id;

&#x20;   }



&#x20; }



&#x20; return map;



}







/\* =====================================================

BUILD ENTITY MAP

===================================================== \*/



function buildEntityMap(entities) {



&#x20; const map = {};



&#x20; for (let i = 0; i < entities.length; i++) {



&#x20;   const id = entities\[i]\[0];

&#x20;   const firstName = entities\[i]\[2];

&#x20;   const displayName = entities\[i]\[5];



&#x20;   if (firstName) {

&#x20;     map\[firstName.toString().trim().toLowerCase()] = id;

&#x20;   }



&#x20;   if (displayName) {

&#x20;     map\[displayName.toString().trim().toLowerCase()] = id;

&#x20;   }



&#x20; }



&#x20; return map;



}







/\* =====================================================

EVENT FINGERPRINT

===================================================== \*/



function generateEventFingerprint(event, projectId, entityId) {



&#x20; return \[

&#x20;   projectId,

&#x20;   entityId,

&#x20;   event.dataEvento,

&#x20;   event.tipo,

&#x20;   event.valore,

&#x20;   event.causale,

&#x20;   event.source

&#x20; ].join("|");



}







/\* =====================================================

MOVEMENT ID GENERATOR

===================================================== \*/



function LOGOS\_generateMovementID() {



&#x20; const props = PropertiesService.getDocumentProperties();



&#x20; let lastNumber = parseInt(props.getProperty("LAST\_MOV\_NUMBER")) || 0;



&#x20; const nextNumber = lastNumber + 1;



&#x20; props.setProperty("LAST\_MOV\_NUMBER", nextNumber);



&#x20; const newId = "MOV\_" + String(nextNumber).padStart(6, "0");



&#x20; return newId;



}


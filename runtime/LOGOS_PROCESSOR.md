# LOGOS\_PROCESSOR.md

\#DOC.LOGOS\_PROCESSOR

Pipeline:

RAW\_INPUT → Processor → RAW\_DATA

Funzione principale:

processRawInputV2()

Fasi:

1. Lettura coda
2. Header mapping
3. Validazione progetto
4. Risoluzione entità
5. Generazione fingerprint
6. Deduplicazione batch
7. Scrittura ledger
8. Aggiornamento stato

Stati RAW\_INPUT:

NEW
WRITTEN
ERROR\_PROJECT
ENTITY\_PENDING
DUPLICATE

Buffer di scrittura:

ledgerBuffer → append RAW\_DATA

Controlli:

MAX\_EVENTS\_PER\_RUN = 50

Motore fingerprint:

projectId | entityId | dataEvento | tipo | valore | causale

----------- Script completo



/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS ENGINE — INPUT PROCESSOR

Versione: v1.6

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

Legge solo nuove righe inserite nella coda

===================================================== \*/



&#x20; const lastRow = inputSheet.getLastRow();

&#x20; const lastCol = inputSheet.getLastColumn();



&#x20; if (lastRow < 2) return;



&#x20; let queueCursor = parseInt(props.getProperty("LOGOS\_QUEUE\_CURSOR")) || 1;



&#x20; const startRow = Math.max(2, queueCursor + 1);

&#x20; const numRows = lastRow - startRow + 1;



&#x20; if (numRows <= 0) {



&#x20;   logEvent(

&#x20;     "INPUT\_BATCH\_COMPLETED",

&#x20;     "RAW\_INPUT",

&#x20;     0,

&#x20;     "0 eventi processati"

&#x20;   );



&#x20;   return;



&#x20; }





/\* =====================================================

HEADER MAPPING

Identifica colonne tramite nome

===================================================== \*/



&#x20; const headers = inputSheet.getRange(1,1,1,lastCol).getValues()\[0];

&#x20; const inputData = inputSheet.getRange(startRow,1,numRows,lastCol).getValues();



&#x20; const headerIndex = {};

&#x20; headers.forEach((h,i)=> headerIndex\[h] = i);





/\* =====================================================

MAPPE PROGETTI / ENTITÀ

===================================================== \*/



&#x20; const projects = projectsSheet.getDataRange().getValues();

&#x20; const entities = entitiesSheet.getDataRange().getValues();



&#x20; const projectMap = buildProjectMap(projects);

&#x20; const entityMap = buildEntityMap(entities);





/\* =====================================================

FINGERPRINT SET (solo per questo batch)

===================================================== \*/



&#x20; const batchFingerprints = new Set();





/\* =====================================================

BUFFER DI SCRITTURA

===================================================== \*/



&#x20; const ledgerBuffer = \[];

&#x20; const statusUpdates = \[];





/\* =====================================================

PROCESSAMENTO CODA

===================================================== \*/



&#x20; for (let r = 0; r < inputData.length; r++) {



&#x20;   if (processedCount >= MAX\_EVENTS\_PER\_RUN) break;



&#x20;   const row = inputData\[r];

&#x20;   const status = row\[headerIndex\["Status"]];



&#x20;   if (!status || status !== "NEW") continue;



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



&#x20;   const entityResult = entityResolutionEngine(event, entityMap);



&#x20;   if (entityResult.status !== "OK") {



&#x20;     logEvent(

&#x20;       "INPUT\_ENTITY\_PENDING",

&#x20;       "RAW\_INPUT",

&#x20;       startRow + r,

&#x20;       "Entità non trovata: " + event.soggetto

&#x20;     );



&#x20;     statusUpdates.push(\[startRow + r, "ENTITY\_PENDING"]);

&#x20;     continue;



&#x20;   }





/\* =====================================================

GENERAZIONE FINGERPRINT EVENTO

===================================================== \*/



&#x20;   const fingerprint = generateEventFingerprint(event, validation.projectId, entityResult.entityId);



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

BUFFER EVENTO PER SCRITTURA LEDGER

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

SCRITTURA LEDGER (APPEND-ONLY)

===================================================== \*/



&#x20; if (ledgerBuffer.length > 0) {



&#x20;   const startRowLedger = ledgerSheet.getLastRow() + 1;



&#x20;   ledgerSheet

&#x20;     .getRange(startRowLedger,1,ledgerBuffer.length,ledgerBuffer\[0].length)

&#x20;     .setValues(ledgerBuffer);



&#x20;   logEvent(

&#x20;     "INPUT\_WRITTEN",

&#x20;     "RAW\_DATA",

&#x20;     startRowLedger,

&#x20;     ledgerBuffer.length + " eventi scritti"

&#x20;   );



&#x20; }





/\* =====================================================

AGGIORNAMENTO STATUS CODA

===================================================== \*/



&#x20; statusUpdates.forEach(function(update) {



&#x20;   inputSheet

&#x20;     .getRange(update\[0],headerIndex\["Status"]+1)

&#x20;     .setValue(update\[1]);



&#x20; });





/\* =====================================================

AGGIORNAMENTO CURSORE CODA

===================================================== \*/



&#x20; props.setProperty("LOGOS\_QUEUE\_CURSOR", lastRow);





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



&#x20; const projectId = projectMap\[event.projectName];



&#x20; if (!projectId) {

&#x20;   return { valid:false };

&#x20; }



&#x20; return { valid:true, projectId:projectId };



}







/\* =====================================================

ENTITY RESOLUTION ENGINE

===================================================== \*/



function entityResolutionEngine(event, entityMap) {



&#x20; const entityId = entityMap\[event.soggetto];



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



&#x20; for (let i = 1; i < projects.length; i++) {

&#x20;   map\[projects\[i]\[1]] = projects\[i]\[0];

&#x20; }



&#x20; return map;



}







/\* =====================================================

BUILD ENTITY MAP

===================================================== \*/



function buildEntityMap(entities) {



&#x20; const map = {};



&#x20; for (let i = 1; i < entities.length; i++) {

&#x20;   map\[entities\[i]\[2]] = entities\[i]\[0];

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

&#x20;   event.causale

&#x20; ].join("|");



}


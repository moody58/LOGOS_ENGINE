function processRawInputV2() {



&#x20; logEvent("INPUT\_PROCESSOR\_START","RAW\_INPUT",0,"Processor avviato");



&#x20; const ss = SpreadsheetApp.getActive();



&#x20; const input = ss.getSheetByName("RAW\_INPUT");

&#x20; const ledger = ss.getSheetByName("RAW\_DATA");

&#x20; const projects = ss.getSheetByName("PROJECTS");

&#x20; const entities = ss.getSheetByName("ENTITIES");

&#x20; const entitySheet = ss.getSheetByName("ENTITY\_CONFIRMATION");



&#x20; /\* ================= SAFETY ================= \*/



&#x20; if (input.getLastRow() < 2) return;



&#x20; const lastCol = input.getLastColumn();

&#x20; const headers = input.getRange(1,1,1,lastCol).getValues()\[0];

&#x20; const data = input.getRange(2,1,input.getLastRow()-1,lastCol).getValues();



&#x20; /\* ================= HEADER MAP ================= \*/



&#x20; const headerIndex = {};

&#x20; headers.forEach((h,i)=> headerIndex\[h] = i);



&#x20; const get = (row, field) => {

&#x20;   const i = headerIndex\[field];

&#x20;   return i !== undefined ? row\[i] : "";

&#x20; };



&#x20; /\* ================= MAP ================= \*/



&#x20; const proj = projects.getLastRow() > 1

&#x20;   ? projects.getRange(2,1,projects.getLastRow()-1,2).getValues()

&#x20;   : \[];



&#x20; const ent = entities.getLastRow() > 1

&#x20;   ? entities.getRange(2,1,entities.getLastRow()-1,6).getValues()

&#x20;   : \[];



&#x20; const pMap = {};

&#x20; proj.forEach(r => {

&#x20;   if (r\[1]) pMap\[r\[1].toString().trim().toLowerCase()] = r\[0];

&#x20; });



&#x20; const eMap = {};

&#x20; ent.forEach(r => {

&#x20;   if (r\[2]) eMap\[r\[2].toString().trim().toLowerCase()] = r\[0];

&#x20;   if (r\[5]) eMap\[r\[5].toString().trim().toLowerCase()] = r\[0];

&#x20; });



&#x20; /\* ================= ENTITY CACHE ================= \*/



&#x20; const existingEntities = new Set();



&#x20; if (entitySheet.getLastRow() > 1) {

&#x20;   entitySheet

&#x20;     .getRange(2,1,entitySheet.getLastRow()-1,1)

&#x20;     .getValues()

&#x20;     .flat()

&#x20;     .forEach(v => {

&#x20;       if (v) existingEntities.add(v.toString().trim().toLowerCase());

&#x20;     });

&#x20; }



&#x20; /\* ================= BUFFER ================= \*/



&#x20; const statusUpdates = \[];

&#x20; const ledgerBuffer = \[];

&#x20; const entityBuffer = \[];



&#x20; /\* ================= BATCH CONTROL ================= \*/



&#x20; const MAX\_EVENTS = 50;

&#x20; let processed = 0;



&#x20; /\* ================= LOOP ================= \*/



&#x20; for (let i = 0; i < data.length; i++) {



&#x20;   if (processed >= MAX\_EVENTS) break;



&#x20;   const row = data\[i];



&#x20;   const status = (get(row,"Status") || "")

&#x20;     .toString()

&#x20;     .trim()

&#x20;     .toUpperCase();



&#x20;   if (status !== "NEW" \&\& status !== "ENTITY\_PENDING") continue;



&#x20;   /\* ================= PROJECT ================= \*/



&#x20;   const projectKey = (get(row,"Project\_Name") || "")

&#x20;     .toString()

&#x20;     .trim()

&#x20;     .toLowerCase();



&#x20;   const projectId = pMap\[projectKey];



&#x20;   if (!projectId) {



&#x20;     logEvent("INPUT\_ERROR\_PROJECT","RAW\_INPUT",i+2,projectKey);



&#x20;     statusUpdates.push(\[i+2, "ERROR\_PROJECT"]);

&#x20;     continue;

&#x20;   }



&#x20;   /\* ================= ENTITY ================= \*/



&#x20;   const entityKey = (get(row,"Soggetto") || "")

&#x20;     .toString()

&#x20;     .trim()

&#x20;     .toLowerCase();



&#x20;   const entityId = eMap\[entityKey];



&#x20;   if (!entityId) {



&#x20;     if (!existingEntities.has(entityKey) \&\& entityKey !== "") {



&#x20;       entityBuffer.push(\[

&#x20;         get(row,"Soggetto"),

&#x20;         "",

&#x20;         "",

&#x20;         "",

&#x20;         "",

&#x20;         new Date(),

&#x20;         "PENDING"

&#x20;       ]);



&#x20;       existingEntities.add(entityKey);



&#x20;       logEvent("ENTITY\_CREATED\_PENDING","ENTITY\_CONFIRMATION",i+2,entityKey);

&#x20;     }



&#x20;     statusUpdates.push(\[i+2, "ENTITY\_PENDING"]);

&#x20;     continue;

&#x20;   }



&#x20;   /\* ================= WRITE ================= \*/



&#x20;   ledgerBuffer.push(\[

&#x20;     LOGOS\_generateMovementID(),

&#x20;     new Date(),

&#x20;     get(row,"Data\_Evento"),

&#x20;     get(row,"Soggetto"),

&#x20;     projectId,

&#x20;     entityId,

&#x20;     get(row,"Tipo\_Movimento"),

&#x20;     get(row,"Valore"),

&#x20;     "",

&#x20;     get(row,"Causale"),

&#x20;     "",

&#x20;     get(row,"Riferimento\_ID"),

&#x20;     get(row,"Source"),

&#x20;     get(row,"Metodo\_Pagamento"),

&#x20;     get(row,"Note")

&#x20;   ]);



&#x20;   statusUpdates.push(\[i+2, "WRITTEN"]);



&#x20;   processed++;

&#x20; }



&#x20; /\* ================= WRITE ENTITY ================= \*/



&#x20; if (entityBuffer.length > 0) {

&#x20;   entitySheet

&#x20;     .getRange(entitySheet.getLastRow()+1,1,entityBuffer.length,7)

&#x20;     .setValues(entityBuffer);

&#x20; }



&#x20; /\* ================= WRITE LEDGER ================= \*/



&#x20; if (ledgerBuffer.length > 0) {



&#x20;   const startRow = ledger.getLastRow()+1;



&#x20;   ledger

&#x20;     .getRange(startRow,1,ledgerBuffer.length,ledgerBuffer\[0].length)

&#x20;     .setValues(ledgerBuffer);



&#x20;   logEvent("INPUT\_WRITTEN","RAW\_DATA",startRow,ledgerBuffer.length + " eventi scritti");

&#x20; }



&#x20; /\* ================= STATUS ================= \*/



&#x20; const statusCol = headerIndex\["Status"] + 1;

&#x20; const timestampCol = 1;



&#x20; const statusRange = input.getRange(2, statusCol, data.length, 1).getValues();

&#x20; const timeRange = input.getRange(2, timestampCol, data.length, 1).getValues();



&#x20; statusUpdates.forEach(s => {

&#x20;   const index = s\[0] - 2;

&#x20;   statusRange\[index]\[0] = s\[1];

&#x20;   timeRange\[index]\[0] = new Date();

&#x20; });



&#x20; input.getRange(2, statusCol, data.length, 1).setValues(statusRange);

&#x20; input.getRange(2, timestampCol, data.length, 1).setValues(timeRange);



&#x20; logEvent("INPUT\_BATCH\_COMPLETED","RAW\_INPUT",0,processed + " eventi processati");

}





/\* ================= ID ================= \*/



function LOGOS\_generateMovementID() {



&#x20; const props = PropertiesService.getDocumentProperties();



&#x20; let last = parseInt(props.getProperty("LAST\_MOV\_NUMBER")) || 0;



&#x20; last++;



&#x20; props.setProperty("LAST\_MOV\_NUMBER", last);



&#x20; return "MOV\_" + String(last).padStart(6,"0");

}


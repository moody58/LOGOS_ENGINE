/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

&#x20;LOGOS — INGESTION LAYER

&#x20;File: ingestion.gs

&#x20;Sistema: LOGOS Engine

&#x20;Ruolo: Entry point per scrittura eventi in RAW\_INPUT



&#x20;NOTE ARCHITETTURALI:

&#x20;- Questo layer NON contiene logica di business

&#x20;- ENTITY COMMIT è TEMPORANEO (verrà spostato in Entity Engine)

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/





/\* =====================================================

INGEST EVENT

===================================================== \*/



function LOGOS\_ingestEvent(event) {



&#x20; const ss = SpreadsheetApp.getActiveSpreadsheet();

&#x20; const sheet = ss.getSheetByName("RAW\_INPUT");



&#x20; if (!sheet) {

&#x20;   throw new Error("Sheet RAW\_INPUT non trovato");

&#x20; }



&#x20; const now = new Date();



&#x20; const row = \[

&#x20;   now,

&#x20;   event.project\_name || "",

&#x20;   event.tipo || "",

&#x20;   event.valore || "",

&#x20;   event.data\_evento || "",

&#x20;   event.causale || "",

&#x20;   event.riferimento || "",

&#x20;   event.metodo\_pagamento || "",

&#x20;   event.note || "",

&#x20;   event.soggetto || "",

&#x20;   event.source || "WEBAPP",

&#x20;   "NEW"

&#x20; ];



&#x20; sheet.appendRow(row);



&#x20; // Trigger gestito dal KERNEL (non qui)

}





/\* =====================================================

TEST MANUALE

===================================================== \*/



function LOGOS\_testInsert() {



&#x20; const testEvent = {

&#x20;   project\_name: "TEST\_PROJECT",

&#x20;   tipo: "Spesa",

&#x20;   valore: 100,

&#x20;   data\_evento: new Date(),

&#x20;   causale: "Test ingestione",

&#x20;   riferimento: "TEST\_001",

&#x20;   metodo\_pagamento: "Contanti",

&#x20;   note: "Evento di test",

&#x20;   soggetto: "Test User",

&#x20;   source: "TEST"

&#x20; };



&#x20; LOGOS\_ingestEvent(testEvent);

}





/\* =====================================================

RESET CURSORE PROCESSOR

===================================================== \*/



function LOGOS\_resetQueueCursor() {



&#x20; const props = PropertiesService.getDocumentProperties();

&#x20; props.setProperty("LOGOS\_QUEUE\_CURSOR", 1);



&#x20; Logger.log("Queue cursor resettato a 1");

}





/\* =====================================================

ENTITY COMMIT (TEMPORANEO — DA SPOSTARE)

===================================================== \*/



function LOGOS\_commitSingleEntity(row) {



&#x20; const ss = SpreadsheetApp.getActive();

&#x20; const entitySheet = ss.getSheetByName("ENTITY\_CONFIRMATION");

&#x20; const entitiesSheet = ss.getSheetByName("ENTITIES");



&#x20; const data = entitySheet.getRange(row,1,1,7).getValues()\[0];



&#x20; const inputText = (data\[0] || "").toString().trim();

&#x20; const confirmedId = data\[4];



&#x20; if (confirmedId || !inputText) return;



&#x20; const newId = generateEntityId();



&#x20; entitiesSheet.appendRow(\[

&#x20;   newId,

&#x20;   "",

&#x20;   inputText,

&#x20;   "",

&#x20;   "",

&#x20;   inputText

&#x20; ]);



&#x20; entitySheet.getRange(row,5).setValue(newId);

&#x20; entitySheet.getRange(row,7).setValue("CONFIRMED");



&#x20; logEvent(

&#x20;   "ENTITY\_COMMITTED",

&#x20;   "ENTITIES",

&#x20;   row,

&#x20;   "Nuova entità creata: " + inputText

&#x20; );

}





/\* =====================================================

ENTITY ID GENERATOR (UNICO)

===================================================== \*/



function generateEntityId() {



&#x20; const props = PropertiesService.getDocumentProperties();



&#x20; let last = parseInt(props.getProperty("LAST\_ENTITY\_ID") || "0");



&#x20; if (isNaN(last)) last = 0;



&#x20; const next = last + 1;



&#x20; props.setProperty("LAST\_ENTITY\_ID", next);



&#x20; return "ENT\_" + String(next).padStart(6,"0");

}


/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

&#x20;LOGOS — INGESTION LAYER (CLEAN)

&#x20;Versione: v2.0 STABLE

&#x20;Ruolo: Entry point eventi → RAW\_INPUT

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/



function LOGOS\_ingestEvent(event) {



&#x20; const ss = SpreadsheetApp.getActiveSpreadsheet();

&#x20; const sheet = ss.getSheetByName("RAW\_INPUT");



&#x20; if (!sheet) {

&#x20;   throw new Error("Sheet RAW\_INPUT non trovato");

&#x20; }



&#x20; const now = new Date();



&#x20; const row = \[

&#x20;   now,                                      // Timestamp\_Form

&#x20;   event.project\_name || "",                  // Project\_Name

&#x20;   event.tipo || "",                         // Tipo\_Movimento

&#x20;   event.valore || "",                       // Valore

&#x20;   event.data\_evento || "",                  // Data\_Evento

&#x20;   event.causale || "",                      // Causale

&#x20;   event.riferimento || "",                  // Riferimento\_ID

&#x20;   event.metodo\_pagamento || "",             // Metodo\_Pagamento

&#x20;   event.note || "",                         // Note

&#x20;   event.soggetto || "",                     // Soggetto

&#x20;   event.source || "WEBAPP",                 // Source

&#x20;   "NEW"                                     // Status

&#x20; ];



&#x20; sheet.appendRow(row);



}





/\* =====================================================

TEST MANUALE — Inserimento evento

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

RESET CURSORE (LEGACY / DEBUG)

===================================================== \*/



function LOGOS\_resetQueueCursor() {



&#x20; const props = PropertiesService.getDocumentProperties();

&#x20; props.setProperty("LOGOS\_QUEUE\_CURSOR", 1);



&#x20; Logger.log("Queue cursor resettato a 1");



}


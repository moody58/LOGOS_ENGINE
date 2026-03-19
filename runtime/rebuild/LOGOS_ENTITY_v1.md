function LOGOS\_commitSingleEntity(row) {



&#x20; const ss = SpreadsheetApp.getActive();



&#x20; const confirmSheet = ss.getSheetByName("ENTITY\_CONFIRMATION");

&#x20; const entitiesSheet = ss.getSheetByName("ENTITIES");



&#x20; const data = confirmSheet.getRange(row,1,1,7).getValues()\[0];



&#x20; const inputText = data\[0];

&#x20; const confirmedId = data\[4];



&#x20; // già confermato → skip

&#x20; if (confirmedId) return;



&#x20; const props = PropertiesService.getDocumentProperties();



&#x20; let last = parseInt(props.getProperty("LAST\_ENT\_NUMBER")) || 0;

&#x20; const next = last + 1;



&#x20; props.setProperty("LAST\_ENT\_NUMBER", next);



&#x20; const newId = "ENT\_" + String(next).padStart(6,"0");



&#x20; // scrive ENTITIES

&#x20; entitiesSheet.appendRow(\[

&#x20;   newId,

&#x20;   "PERSON",

&#x20;   inputText,

&#x20;   "",

&#x20;   "",

&#x20;   inputText

&#x20; ]);



&#x20; // aggiorna confirmation

&#x20; confirmSheet.getRange(row,5).setValue(newId);

&#x20; confirmSheet.getRange(row,7).setValue("CONFIRMED");



&#x20; logEvent("ENTITY\_CREATED","ENTITIES",row,"Nuova entità: " + inputText);

}


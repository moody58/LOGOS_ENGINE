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



&#x20; // === ALLINEAMENTO CONTATORE CON FOGLIO ===

&#x20; let existingIds = \[];



&#x20; if (entitiesSheet.getLastRow() > 1) {

&#x20;   existingIds = entitiesSheet

&#x20;     .getRange(2,1,entitiesSheet.getLastRow()-1,1)

&#x20;     .getValues()

&#x20;     .flat()

&#x20;     .filter(v => v)

&#x20;     .map(v => {

&#x20;       const match = v.toString().match(/^ENT\_(\\d{6})$/);

&#x20;       return match ? parseInt(match\[1]) : 0;

&#x20;     });

&#x20; }



&#x20; const maxExisting = existingIds.length > 0 ? Math.max(...existingIds) : 0;



&#x20; let lastStored = parseInt(props.getProperty("LAST\_ENT\_NUMBER")) || 0;



&#x20; let last = Math.max(maxExisting, lastStored);



&#x20; const next = last + 1;



&#x20; props.setProperty("LAST\_ENT\_NUMBER", next);



&#x20; const newId = "ENT\_" + String(next).padStart(6,"0");



&#x20; // scrive ENTITIES (INVARIATO)

&#x20; entitiesSheet.appendRow(\[

&#x20;   newId,

&#x20;   "PERSON",

&#x20;   inputText,

&#x20;   "",

&#x20;   "",

&#x20;   inputText

&#x20; ]);



&#x20; // aggiorna confirmation (INVARIATO)

&#x20; confirmSheet.getRange(row,5).setValue(newId);

&#x20; confirmSheet.getRange(row,7).setValue("CONFIRMED");



&#x20; logEvent("ENTITY\_CREATED","ENTITIES",row,"Nuova entità: " + inputText);

}


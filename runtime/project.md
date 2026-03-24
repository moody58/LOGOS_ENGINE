function LOGOS\_commitSingleProject(row) {



&#x20; const ss = SpreadsheetApp.getActive();



&#x20; // === SHEETS ===

&#x20; const confirmSheet = ss.getSheetByName("PROJECT\_CONFIRMATION");

&#x20; const projectsSheet = ss.getSheetByName("PROJECTS");



&#x20; // === READ ROW DATA ===

&#x20; // legge la riga della tabella PROJECT\_CONFIRMATION

&#x20; const data = confirmSheet.getRange(row,1,1,7).getValues()\[0];



&#x20; const inputText = data\[0];        // nome progetto inserito dall'utente

&#x20; const confirmedId = data\[4];      // ID già assegnato (se esiste)



&#x20; // === SKIP SE GIÀ CONFERMATO ===

&#x20; // evita duplicazioni di progetto

&#x20; if (confirmedId) return;



&#x20; // === NORMALIZZAZIONE NOME PROGETTO ===

&#x20; // fondamentale per il matching con il processor

&#x20; const normalizedProjectName = inputText

&#x20; .toString()

&#x20; .trim()

&#x20; .toLowerCase()

&#x20; .replace(/\\s+/g, " ");



&#x20; // === GENERAZIONE ID PROGETTO ===

&#x20; // usa PropertiesService per mantenere contatore persistente

&#x20; const props = PropertiesService.getDocumentProperties();



// === ALLINEAMENTO CONTATORE CON FOGLIO ===



const existingIds = projectsSheet

&#x20; .getRange(2,1,projectsSheet.getLastRow()-1,1)

&#x20; .getValues()

&#x20; .flat()

&#x20; .filter(v => v)

&#x20; .map(v => parseInt(v.toString().replace("PRJ\_","")) || 0);



const maxExisting = existingIds.length > 0 ? Math.max(...existingIds) : 0;



let lastStored = parseInt(props.getProperty("LAST\_PROJ\_NUMBER")) || 0;



// usa il massimo tra sheet e proprietà

let last = Math.max(maxExisting, lastStored);



const next = last + 1;



props.setProperty("LAST\_PROJ\_NUMBER", next);



const newId = "PRJ\_" + String(next).padStart(6,"0");



&#x20; // === SCRITTURA SU PROJECTS ===

&#x20; // struttura minima coerente con schema attuale

&#x20; projectsSheet.appendRow(\[

&#x20;   newId,

&#x20;   normalizedProjectName, // <-- USARE SEMPRE NORMALIZZATO

&#x20;   "",                   // Parent\_Project\_ID (vuoto per ora)

&#x20;   "STANDARD",           // Project\_Type (default)

&#x20;   "ACTIVE",              // Status (default)

&#x20;   "", // Start\_Date

&#x20;   "", // End\_Date

&#x20;   new Date(), // Created\_Timestamp ✅

&#x20;   ""  // Notes

&#x20; ]);



&#x20; // === UPDATE PROJECT\_CONFIRMATION ===

&#x20; // scrive ID e stato confermato

&#x20; confirmSheet.getRange(row,5).setValue(newId);       // Confirmed\_Project\_ID

&#x20; confirmSheet.getRange(row,7).setValue("CONFIRMED"); // Status



// === REPROCESS AUTOMATICO (DOUBLE PASS) ===

processRawInputV2();



Utilities.sleep(200);



// secondo passaggio (ora pMap aggiornata)

processRawInputV2();



logEvent("REPROCESS\_TRIGGER","PROJECT\_CONFIRMATION",row,"Reprocess avviato");

}





// === TEST MANUALE ===

// usa questa funzione per testare senza trigger automatici

function TEST\_commitProject() {

&#x20; LOGOS\_commitSingleProject(10); // ← metti qui la riga reale

}


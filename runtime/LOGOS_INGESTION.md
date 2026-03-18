/*******************************************************
 LOGOS — INGESTION LAYER
 File: ingestion.gs
 Sistema: LOGOS Engine
 Ruolo: Entry point per scrittura eventi in RAW_INPUT

 RESPONSABILITÀ:
 - ricevere evento da WebApp/API
 - normalizzare dati minimi
 - scrivere su RAW_INPUT
 - attivare il processor (via Status = NEW)

 NOTE:
 - NON genera ID (delegato a Processor/Kernel)
 - NON risolve entità (delegato a Processor)
 - NON valida in modo rigido (UX-first)

*******************************************************/


function LOGOS_ingestEvent(event) {

  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getSheetByName("RAW_INPUT");

  if (!sheet) {
    throw new Error("Sheet RAW_INPUT non trovato");
  }

  const now = new Date();

  const row = [
    now,
    event.project_name || "",
    event.tipo || "",
    event.valore || "",
    event.data_evento || "",
    event.causale || "",
    event.riferimento || "",
    event.metodo_pagamento || "",
    event.note || "",
    event.soggetto || "",
    event.source || "WEBAPP",
    "NEW"
  ];

  sheet.appendRow(row);

  // =====================================================
  // TRIGGER MANUALE PROCESSOR (FIX CRITICO)
  // =====================================================

  processRawInputV2();

}

/**
 * TEST MANUALE — Inserimento evento di prova
 * Serve per verificare pipeline completa LOGOS
 */
function LOGOS_testInsert() {

  const testEvent = {
    project_name: "TEST_PROJECT",
    tipo: "Spesa",
    valore: 100,
    data_evento: new Date(),
    causale: "Test ingestione",
    riferimento: "TEST_001",
    metodo_pagamento: "Contanti",
    note: "Evento di test",
    soggetto: "Test User",
    source: "TEST"
  };

  LOGOS_ingestEvent(testEvent);

}

function LOGOS_resetQueueCursor() {

  const props = PropertiesService.getDocumentProperties();

  props.setProperty("LOGOS_QUEUE_CURSOR", 1);

  Logger.log("Queue cursor resettato a 1");

}

function LOGOS_commitConfirmedEntities() {

  const ss = SpreadsheetApp.getActive();

  const entitySheet = ss.getSheetByName("ENTITY_CONFIRMATION");
  const entitiesSheet = ss.getSheetByName("ENTITIES");

  const data = entitySheet.getDataRange().getValues();

  for (let i = 1; i < data.length; i++) {

    const row = data[i];

    const inputText = row[0];
    const userConfirm = row[3];
    const status = row[6];

    // Processa solo confermati non ancora gestiti
    if (
      userConfirm === "Confirm" &&
      status !== "CONFIRMED"
    ) {

      const newEntityId = "ENT_" + new Date().getTime();

      // Scrive su ENTITIES
      entitiesSheet.appendRow([
       newId,        // Entity_ID
       "PERSON",     // Entity_Type (default)
        inputText,    // First_Name
       "",           // Last_Name
       "",           // Company
       inputText,    // Display_Name
       "", "", "", "", "", "", "", "", new Date()
    ]);

      // Aggiorna ENTITY_CONFIRMATION
      entitySheet.getRange(i + 1, 5).setValue(newEntityId); // Confirmed_Entity_ID
      entitySheet.getRange(i + 1, 7).setValue("CONFIRMED"); // Status

      logEvent(
        "ENTITY_CREATED",
        "ENTITIES",
        i + 1,
        "Nuova entità: " + inputText
      );

    }

  }

}

function onEdit(e) {

  const sheet = e.source.getActiveSheet();
  const range = e.range;

  if (sheet.getName() !== "ENTITY_CONFIRMATION") return;

  const col = range.getColumn();
  const row = range.getRow();

  if (col !== 4 || row < 2) return;

  const value = (range.getValue() || "").toString().trim();

  if (value !== "Confirm") return;

  // STEP 1 → commit entity
  LOGOS_commitSingleEntity(row);

  // STEP 2 → reprocess automatico
  processRawInputV2();

}

function LOGOS_commitSingleEntity(row) {

  const ss = SpreadsheetApp.getActive();

  const entitySheet = ss.getSheetByName("ENTITY_CONFIRMATION");
  const entitiesSheet = ss.getSheetByName("ENTITIES");

  const data = entitySheet.getRange(row,1,1,7).getValues()[0];

  const inputText = data[0];
  const confirmedId = data[4];

  // se già confermato → skip
  if (confirmedId) return;

  // genera ID
  const newId = generateEntityId();

  // scrivi in ENTITIES
  entitiesSheet.appendRow([
    newId,
    "",
    inputText,
    "",
    "",
    inputText
  ]);

  // aggiorna riga ENTITY_CONFIRMATION
  entitySheet.getRange(row,5).setValue(newId); // Confirmed_Entity_ID
  entitySheet.getRange(row,7).setValue("CONFIRMED");

  logEvent(
    "ENTITY_COMMITTED",
    "ENTITIES",
    row,
    "Nuova entità creata: " + inputText
  );

}

function generateEntityId() {

  const props = PropertiesService.getDocumentProperties();

  let last = parseInt(props.getProperty("LAST_ENTITY_ID")) || 0;

  const next = last + 1;

  props.setProperty("LAST_ENTITY_ID", next);

  return "ENT_" + String(next).padStart(6,"0");

}
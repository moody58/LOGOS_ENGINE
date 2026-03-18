/*******************************************************
 LOGOS — INGESTION LAYER
 File: ingestion.gs
 Sistema: LOGOS Engine
 Ruolo: Entry point per scrittura eventi in RAW_INPUT
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
}

/**
 * TEST MANUALE — Inserimento evento di prova
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

/**
 * RESET CURSORE PROCESSOR
 */
function LOGOS_resetQueueCursor() {

  const props = PropertiesService.getDocumentProperties();
  props.setProperty("LOGOS_QUEUE_CURSOR", 1);

  Logger.log("Queue cursor resettato a 1");
}

/* =====================================================
ENTITY COMMIT (SINGLE — STANDARD)
⚠️ TEMPORANEO: resterà qui fino a refactor completo
===================================================== */

function LOGOS_commitSingleEntity(row) {

  const ss = SpreadsheetApp.getActive();
  const entitySheet = ss.getSheetByName("ENTITY_CONFIRMATION");
  const entitiesSheet = ss.getSheetByName("ENTITIES");

  const data = entitySheet.getRange(row,1,1,7).getValues()[0];

  const inputText = data[0];
  const confirmedId = data[4];

  if (confirmedId) return;

  const newId = generateEntityId();

  entitiesSheet.appendRow([
    newId,
    "",
    inputText,
    "",
    "",
    inputText
  ]);

  entitySheet.getRange(row,5).setValue(newId);
  entitySheet.getRange(row,7).setValue("CONFIRMED");

  logEvent(
    "ENTITY_COMMITTED",
    "ENTITIES",
    row,
    "Nuova entità creata: " + inputText
  );
}

/* =====================================================
ENTITY ID GENERATOR (UNICO)
===================================================== */

function generateEntityId() {

  const props = PropertiesService.getDocumentProperties();

  let last = parseInt(props.getProperty("LAST_ENTITY_ID")) || 0;

  const next = last + 1;

  props.setProperty("LAST_ENTITY_ID", next);

  return "ENT_" + String(next).padStart(6,"0");
}
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS — NORMALIZATION LAYER (FOUNDATION)

Versione: v0.1 SAFE

Ruolo: normalizzare input senza rompere pipeline

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/



function LOGOS\_normalizeEvent(event) {



&#x20; return {

&#x20;   project\_name: normalizeText(event.project\_name),

&#x20;   tipo: normalizeText(event.tipo),

&#x20;   valore: normalizeNumber(event.valore),

&#x20;   data\_evento: normalizeDate(event.data\_evento),

&#x20;   causale: normalizeText(event.causale),

&#x20;   riferimento: normalizeText(event.riferimento),

&#x20;   metodo\_pagamento: normalizeText(event.metodo\_pagamento),

&#x20;   note: normalizeText(event.note),

&#x20;   soggetto: normalizeText(event.soggetto),

&#x20;   source: event.source || "WEBAPP"

&#x20; };

}





/\* ================= HELPERS ================= \*/



function normalizeText(value) {

&#x20; if (!value) return "";

&#x20; return value.toString().trim();

}





function normalizeNumber(value) {

&#x20; if (value === "" || value === null || value === undefined) return "";



&#x20; if (typeof value === "number") return value;



&#x20; let v = value.toString().replace(",", ".").trim();



&#x20; let parsed = parseFloat(v);



&#x20; return isNaN(parsed) ? "" : parsed;

}





function normalizeDate(value) {



&#x20; if (!value) return "";



&#x20; if (value instanceof Date) return value;



&#x20; let parsed = new Date(value);



&#x20; return isNaN(parsed.getTime()) ? "" : parsed;

}



/\* ================= MAPPING ================= \*/



function mapEntityTypeToLabel(type) {



&#x20; const map = {

&#x20;   "PERSON": "Persona",

&#x20;   "COMPANY": "Azienda",

&#x20;   "ORGANIZATION": "Organizzazione",

&#x20;   "INTERNAL": "Sistema"

&#x20; };



&#x20; return map\[type] || type;

}


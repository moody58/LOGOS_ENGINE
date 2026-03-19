/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

LOGOS — VALIDATION LAYER (SOFT)

Versione: v0.1 SAFE

Ruolo: controlli leggeri NON bloccanti

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/



function LOGOS\_validateEvent(event) {



&#x20; const warnings = \[];



&#x20; /\* ================= PROJECT ================= \*/



&#x20; if (!event.project\_name) {

&#x20;   warnings.push("PROJECT\_MISSING");

&#x20; }



&#x20; /\* ================= ENTITY ================= \*/



&#x20; if (!event.soggetto) {

&#x20;   warnings.push("ENTITY\_EMPTY");

&#x20; }



&#x20; /\* ================= VALORE ================= \*/



&#x20; if (event.valore === "") {

&#x20;   warnings.push("VALORE\_EMPTY");

&#x20; }



&#x20; /\* ================= DATA ================= \*/



&#x20; if (!event.data\_evento) {

&#x20;   warnings.push("DATA\_EMPTY");

&#x20; }



&#x20; return {

&#x20;   valid: true, // NON blocchiamo mai

&#x20;   warnings: warnings

&#x20; };

}


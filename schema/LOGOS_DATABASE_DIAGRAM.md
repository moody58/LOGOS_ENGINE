
# LOGOS_DATABASE_DIAGRAM.md

## System: LOGOS Event Ledger Architecture

This document describes the relational and logical structure of the LOGOS system.
The diagram reflects the runtime architecture implemented in Google Sheets and Apps Script.

---

## High Level Pipeline

RAW_INPUT → Processor.gs → RAW_DATA → Analytics / Dimensions

---

## Mermaid ER Diagram

```mermaid
erDiagram

RAW_INPUT {
string Timestamp_Form
string Project_Name
string Tipo_Movimento
number Valore
date Data_Evento
string Causale
string Riferimento_ID
string Metodo_Pagamento
string Note
string Soggetto
string Source
string Status
}

RAW_DATA {
string ID_Movimento
datetime Timestamp
date Data_Evento
string Soggetto_Input
string Project_ID
string Entity_ID
string Tipo_Movimento
number Valore
string Unita
string Causale
string Categoria_Derivata
string Riferimento_ID
string Fonte
string Metodo_Pagamento
string Note
}

PROJECTS {
string Project_ID
string Project_Name
string Parent_Project_ID
string Project_Type
string Status
}

ENTITIES {
string Entity_ID
string Entity_Type
string First_Name
string Last_Name
string Company_Name
string Display_Name
string Status
string Parent_Entity_ID
datetime Created_Timestamp
datetime Modified_Timestamp
}

ENTITY_CONFIRMATION {
string Input_Text
string Suggested_Entity
string Suggested_Entity_ID
string User_Confirmation
string Confirmed_Entity_ID
datetime Timestamp
string Status
}

CONTROLLO_PROJECT_DETTAGLIO {
string Parent_Project_ID
string Project_ID
number Ore
number Spese
number Incassi
number Movimenti
}

CONTROLLO_PROJECT {
string Project_ID
number Ore_Diretto
number Spese_Diretto
number Incassi_Diretto
number Movimenti_Diretto
number Ore_Figli
number Spese_Figli
number Incassi_Figli
number Movimenti_Figli
}

CONTROLLO_ENTITY_DETTAGLIO {
string Parent_Entity_ID
string Entity_ID
number Ore
number Spese
number Incassi
number Movimenti
}

CONTROLLO_ENTITY {
string Entity_ID
number Totale_Ore
number Totale_Spese
number Totale_Incassi
number Numero_Movimenti
number Ore_Figli
number Spese_Figli
number Incassi_Figli
number Numero_Movimenti_Figli
}

SYSTEM_LOG {
datetime Timestamp
string Event_Type
string Sheet
number Row
string User
string Details
}

RAW_INPUT ||--o{ RAW_DATA : processed_into
PROJECTS ||--o{ RAW_DATA : referenced_by
ENTITIES ||--o{ RAW_DATA : referenced_by

PROJECTS ||--o{ CONTROLLO_PROJECT_DETTAGLIO : parent_child
CONTROLLO_PROJECT_DETTAGLIO ||--o{ CONTROLLO_PROJECT : aggregated_into

ENTITIES ||--o{ CONTROLLO_ENTITY_DETTAGLIO : parent_child
CONTROLLO_ENTITY_DETTAGLIO ||--o{ CONTROLLO_ENTITY : aggregated_into

RAW_DATA ||--o{ CONTROLLO_PROJECT : analytics_source
RAW_DATA ||--o{ CONTROLLO_ENTITY : analytics_source

RAW_DATA ||--o{ ENTITY_CONFIRMATION : unresolved_entities
```

# TruPharma Snowflake Setup — One-Pager Reference

**File:** `TruPharma_Snowflake_OnePager.docx`  
**Project:** TruPharma Clinical Intelligence  
**Course:** CS 5588 · Spring 2026  
**Team:** Salman Mirza, Amy Ngo, Nithin Songala  

---

## What This Document Is

A single-page landscape reference guide covering the complete Snowflake data warehouse setup for the TruPharma 90-Day Adverse Event Disparity Analysis. Designed to be printed or shared alongside the project as a quick-reference companion.

---

## What It Covers

**Environment Setup**
- Step-by-step creation of the Snowflake warehouse, database, and schema
- Quick reference table of all object names used in the project

**Tables & Views**
- Both loaded tables (`DRUG_ADVERSE_EVENTS`, `QUERY_INTERACTION_LOGS`) with row counts and key columns
- All four analysis views with descriptions of their purpose
- Common errors and fixes encountered during setup

**Loading Data**
- 6-step walkthrough for uploading CSVs via the Snowflake UI
- File format and stage configuration

**Disparity Index Explainer**
- Plain-language breakdown of how the severity disparity index is calculated
- Worked example using Lisinopril/Angioedema (Black patients vs. White baseline)
- HIGH / MODERATE / COMPARABLE threshold definitions

---

## Snowflake Object Reference

| Object | Name |
|--------|------|
| Warehouse | `TRUPHARMA_WH` |
| Database | `TRUPHARMA_DB` |
| Schema | `TRUPHARMA_DB.CLINICAL` |
| Table 1 | `DRUG_ADVERSE_EVENTS` (64 rows) |
| Table 2 | `QUERY_INTERACTION_LOGS` (40 rows) |
| File Format | `TRUPHARMA_CSV_FORMAT` |
| Stage | `TRUPHARMA_STAGE` |
| View 1 | `V_90DAY_DISPARITY_SUMMARY` |
| View 2 | `V_RACIAL_DISPARITY_INDEX` |
| View 3 | `V_90DAY_WEEKLY_TREND` |
| View 4 | `V_QUERY_DISPARITY_ACTIVITY` |

---

## Related Files

| File | Description |
|------|-------------|
| `snowflake_setup.sql` | Full SQL script — run this in a Snowflake worksheet to build the entire environment |
| `drug_adverse_events.csv` | Primary fact table source data (64 rows) |
| `query_interaction_logs.csv` | RAG system interaction log source data (40 rows) |
| `TruPharma_Snowflake_Setup_Guide.docx` | Full beginner walkthrough with detailed instructions |
| `TruPharma_Dashboard_Guide.docx` | Step-by-step Snowsight dashboard build guide for all 5 charts |

---

## How to Use

1. Open `TruPharma_Snowflake_OnePager.docx` in Microsoft Word or Google Docs
2. Print landscape on a single page (File → Print → Layout: Landscape)
3. Use alongside `snowflake_setup.sql` when setting up or presenting the Snowflake environment

---

## Data Source

Adverse event data is modeled after the FDA FAERS (FDA Adverse Event Reporting System) reporting structure. Drug labels sourced via the [openFDA Drug Label API](https://open.fda.gov/apis/drug/label/).

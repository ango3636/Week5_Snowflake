# TruPharma Clinical Intelligence — Snowflake Setup

## Overview

Demo Video on Youtube: https://youtu.be/_0AVp3bdrY4
---

## System Architecture

### Compute Layer

| Component | Configuration |
|---|---|
| Warehouse | `TRUPHARMA_WH` — X-SMALL size |

The warehouse is sized for analytical workloads and cost efficiency. Auto-suspend/resume ensures compute costs are minimized during idle periods.

### Storage Layer

| Component | Name | Purpose |
|---|---|---|
| Database | `TRUPHARMA_DB` | Root container for all clinical data |
| Schema | `CLINICAL` | Logical grouping for adverse event and query log data |
| Stage | `TRUPHARMA_STAGE` | Internal Snowflake-managed upload area for CSV files |
| File Format | `TRUPHARMA_CSV_FORMAT` | Standardized CSV parser (comma-delimited, quoted fields, null handling) |

---

## Data Model

### Table 1: `DRUG_ADVERSE_EVENTS`
**Source:** `drug_adverse_events.csv`  
**Purpose:** Core fact table for disparity analysis — records adverse event reports segmented by drug, demographic group, severity, and geography.

Key columns:
- `DRUG_NAME`, `ADVERSE_EVENT` — Drug-event pair identifiers
- `RACE_ETHNICITY`, `SEX`, `AGE_GROUP` — Demographic dimensions
- `REPORT_COUNT`, `SERIOUS_COUNT`, `SEVERITY_SCORE` — Quantitative metrics
- `REGION`, `QUARTER`, `REPORT_DATE` — Temporal and geographic context
- `DAYS_SINCE_FIRST_REPORT` — Elapsed time metric for trend analysis

### Table 2: `QUERY_INTERACTION_LOGS`
**Source:** `query_interaction_logs.csv`  
**Purpose:** Tracks RAG pipeline activity — logs user queries, retrieval method, model confidence, latency, and whether queries relate to demographic disparities.

Key columns:
- `QUERY`, `TIMESTAMP`, `SESSION_ID` — Query identity and timing
- `RETRIEVAL_METHOD` — `hybrid`, `dense`, or `sparse`
- `LLM_USED` — Whether Google Gemini was invoked
- `CONFIDENCE`, `LATENCY_MS` — Performance metrics
- `DISPARITY_FLAG` — Boolean flag for disparity-related queries
- `DRUG_MENTIONED`, `ADVERSE_EVENT_FLAGGED` — Extracted entities

---

## Data Pipeline

```
CSV Files (local)
      │
      ▼
TRUPHARMA_STAGE  ← PUT command uploads files here
      │
      ▼  COPY INTO (with ON_ERROR = CONTINUE)
      │
      ├──► DRUG_ADVERSE_EVENTS
      └──► QUERY_INTERACTION_LOGS
```

---

## Analytical Views

Four views are pre-built in the `CLINICAL` schema to power dashboards and BI tools.

### `V_90DAY_DISPARITY_SUMMARY`
Aggregates adverse event reports by drug, event, demographic group, region, and quarter. Returns total reports, serious event counts, average severity, and percentage serious — the primary view for disparity visualization.

### `V_RACIAL_DISPARITY_INDEX`
Computes a disparity index for each racial/ethnic group relative to the White/non-Hispanic baseline. Outputs a `DISPARITY_FLAG` for each drug-event-group combination:
- **HIGH DISPARITY** — severity index ≥ 1.2×  
- **MODERATE DISPARITY** — severity index ≥ 1.05×  
- **COMPARABLE** — within 5% of baseline

### `V_90DAY_WEEKLY_TREND`
Buckets adverse event data into weekly intervals over a 90-day observation window. Supports trend line charts and time-series analysis in BI tools (Tableau, Looker, Snowsight, Power BI).

### `V_QUERY_DISPARITY_ACTIVITY`
Aggregates RAG query logs by day, drug, and adverse event. Surfaces which topics are being researched, average model confidence, average latency, and the percentage of queries flagged as disparity-related.

---

## Extensions Implemented

### Disparity Indexing
The `V_RACIAL_DISPARITY_INDEX` view introduces a calculated **Severity Disparity Index** and **Seriousness Disparity Index**, benchmarking every racial/ethnic group against the White/non-Hispanic baseline. This enables equity-focused reporting aligned with FDA pharmacovigilance standards.

---

## Setup Instructions

```sql
-- 1. Create and activate warehouse
USE WAREHOUSE TRUPHARMA_WH;

-- 2. Set database and schema context
USE DATABASE TRUPHARMA_DB;
USE SCHEMA CLINICAL;

-- 3. Upload CSV files to stage (run via SnowSQL CLI)
-- PUT file://drug_adverse_events.csv @TRUPHARMA_STAGE;
-- PUT file://query_interaction_logs.csv @TRUPHARMA_STAGE;

-- 4. Load data
COPY INTO DRUG_ADVERSE_EVENTS
    FROM @TRUPHARMA_STAGE/drug_adverse_events.csv
    FILE_FORMAT = (FORMAT_NAME = 'TRUPHARMA_CSV_FORMAT')
    ON_ERROR = 'CONTINUE';

COPY INTO QUERY_INTERACTION_LOGS
    FROM @TRUPHARMA_STAGE/query_interaction_logs.csv
    FILE_FORMAT = (FORMAT_NAME = 'TRUPHARMA_CSV_FORMAT')
    ON_ERROR = 'CONTINUE';

-- 5. Verify row counts
SELECT 'DRUG_ADVERSE_EVENTS' AS table_name, COUNT(*) AS row_count FROM DRUG_ADVERSE_EVENTS
UNION ALL
SELECT 'QUERY_INTERACTION_LOGS', COUNT(*) FROM QUERY_INTERACTION_LOGS;
```



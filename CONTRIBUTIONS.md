# CONTRIBUTIONS.md — TruPharma Clinical Intelligence

## Team Member Responsibilities

### Salman — Snowflake Infrastructure, Data Pipeline & Analytical Views

| Area | Details |
|------|---------|
| Snowflake Environment | Created `TRUPHARMA_WH` warehouse, `TRUPHARMA_DB` database, `CLINICAL` schema |
| Schema Design | Designed and implemented DDL for `DRUG_ADVERSE_EVENTS` and `QUERY_INTERACTION_LOGS` tables with full column-level metadata |
| Data Ingestion Pipeline | Configured `TRUPHARMA_CSV_FORMAT` file format, `TRUPHARMA_STAGE` internal stage, and `PUT → COPY INTO` loading workflow |
| Analytical Views | Authored `V_90DAY_DISPARITY_SUMMARY`, `V_RACIAL_DISPARITY_INDEX`, `V_90DAY_WEEKLY_TREND`, `V_QUERY_DISPARITY_ACTIVITY` |
| Analysis Queries | Wrote key analysis queries (disparity ranking, 90-day trends, sex-based comparisons, dashboard KPIs, top findings) |
| Documentation | Authored project README, CONTRIBUTIONS.md, pipeline_logs.csv |

### Amy Ngo — Snowsight Dashboard & Visualization

| Area | Details |
|------|---------|
| Dashboard Design | Built the five-tile Snowsight disparity dashboard |
| KPI Scorecards | Total reports, serious events, overall average severity tiles |
| Disparity Bar Chart | Horizontal bar chart of disparity index by race/ethnicity grouped by drug |
| Trend Visualization | Weekly trend line chart with interactive drug name filter |
| Severity Heat Map | Drug-event severity heat map across demographic groups |
| High-Disparity Table | SQL-driven table with emoji-coded severity labels for conditional formatting |
| Debugging | Resolved data gap in disparity index caused by insufficient White patient baseline records |

---

## Extensions Implemented (3 of 3 required)

| # | Extension | Owner | Description |
|---|-----------|-------|-------------|
| 1 | Disparity Indexing | Sal | `V_RACIAL_DISPARITY_INDEX` computes severity and seriousness disparity indices benchmarked against White/non-Hispanic baseline with tiered flagging (HIGH / MODERATE / COMPARABLE) |
| 2 | Interactive Analytics Dashboard | Amy | Five-tile Snowsight dashboard with KPI scorecards, bar charts, trend lines, heat maps, and high-disparity findings table with interactive filtering |
| 3 | Query Performance Monitoring | Sal | `V_QUERY_DISPARITY_ACTIVITY` aggregates RAG pipeline query logs by day, drug, and event — surfaces research patterns, average confidence/latency, and disparity query percentages |

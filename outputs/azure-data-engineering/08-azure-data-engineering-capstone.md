# Azure Data Engineering — Capstone Project

## 1. Project Title

`Global Geo-Economic Stress Indicators — Capstone Project`

## 2. Scenario & Business Problem

An international development agency requires a repeatable, serverless data pipeline to monitor economic stress across 200+ countries over the past six decades. The agency's analysts currently download individual CSV files from the World Bank and FAOSTAT, clean them in Excel, and produce static reports — a process that takes two weeks, cannot be reproduced, and contains no version control.

The agency needs an automated pipeline that ingests the Kaggle Global Geo-Economic Stress Indicators dataset, cleans and normalises the data into a queryable Silver layer, aggregates stress indicators into a Gold layer for dashboarding, and produces a Power BI dashboard showing how inflation, unemployment, GDP growth, and food security have evolved by region and income group over time.

Success means the agency can refresh the entire pipeline — from raw CSV to published dashboard — in under 30 minutes with zero manual steps, at a cost below USD 0.10 per run.

## 3. Dataset Reference

| Field | Value |
|---|---|
| **Name** | Global Geo-Economic Stress Indicators |
| **Source** | [Kaggle — Global Geo-Economic Stress Indicators](https://www.kaggle.com/datasets/sateasinpedas/global-geo-economic-stress-indicators) (CC BY-NC 4.0) |
| **Description** | Country-year panel data covering 1960–2025 for 200+ countries. 5 files (CSV + JSON), ~1 MB compressed. Core file `country_year_indicators.csv` contains 18 columns: country_code, country_name, iso3, region, income_group, year, gdp_growth, inflation, unemployment, gdp_per_capita, population, food_production_index, cereal_yield, cereal_production_tonnes, agricultural_land_pct, dietary_energy_supply_adequacy, economic_stress_score, data_completeness_score. Supporting files include `indicator_dictionary.csv` (column metadata), `country_metadata.csv` (geo-coordinates), `economic_stress_score.csv` (decomposed sub-scores), and `validation_report.json`. |
| **Preprocessing Required** | The raw CSV contains missing values and heterogeneous column types. Learners must handle nulls, cast year to integer, and remove aggregate rows (region/income-group summaries) before writing to Silver. |
| **Storage Location** | ADLS Gen2 — `bronze/` container, raw CSV as ingested |

## 4. Architecture Requirements

The pipeline must include the following Azure services, each performing the specified role:

| Service | Role in the Pipeline | Required Configuration |
|---|---|---|
| **ADLS Gen2** | Storage layer for all medallion zones | HNS enabled, Hot → Cool lifecycle policy |
| **Azure Data Factory** | Orchestration — schedule and execute data movement | At least one pipeline with Copy activity, Tumbling Window trigger, and Key Vault-linked connection |
| **Azure Databricks** | Transformation — PySpark + Delta Lake | Interactive cluster with auto-termination; Job cluster for production run |
| **Synapse Serverless SQL** | Query Silver layer and materialize Gold aggregates | `OPENROWSET` query + `CETAS` write |
| **Power BI Desktop** | Final visualization | Line-chart dashboard from Gold Parquet source |
| **Azure Key Vault** | Secrets management | Storage account key retrieved via Managed Identity, not hardcoded |
| **Microsoft Purview** *(optional)* | Data catalog and lineage | Register ADLS and scan schema |

## 5. Medallion Architecture Mapping

| Layer | Location | Contents | Format |
|---|---|---|---|
| **Bronze** | `bronze/` | Raw ingested CSV — unmodified, timestamped | CSV |
| **Silver** | `silver/stress_indicators/` | Cleaned, deduplicated, nulls handled, cast year to int, aggregate rows removed, partitioned by year | Delta Lake |
| **Gold** | `gold/` | Aggregated views — average stress score by region, 5-year rolling average of inflation by income group, top-10 most stressed countries per decade | Parquet (via CETAS) |

## 6. Expected Pipeline Flow

```
Kaggle Dataset → ADLS Bronze (CSV)
    → ADF Copy Activity with Tumbling Window trigger → bronze/ container
    → Databricks PySpark notebook:
        read Bronze → drop aggregate rows → handle nulls → cast types
        → partition by year → write Delta → OPTIMIZE + ZORDER BY (year, country_code)
        → Delta table in silver/ layer
    → Synapse Serverless SQL:
        OPENROWSET against Silver Delta → aggregate by region/income_group/year
        → CETAS → Gold Parquet files
    → Power BI Desktop:
        connect to Gold → line-chart dashboard with region slicer
```

## 7. Deliverables Checklist

The learner must submit the following:

| # | Deliverable | Format | Description |
|---|---|---|---|
| 1 | **Architecture Diagram** | PNG or draw.io | End-to-end pipeline diagram showing all Azure services, data flow arrows, and medallion layer boundaries |
| 2 | **Data Model** | ERD or schema diagram | Bronze / Silver / Gold table schemas with column names, types, partition keys, and relationships |
| 3 | **Metadata List** | Markdown or CSV | Data catalog entries for each table/column: name, source, description, data type, sensitivity classification, owner |
| 4 | **ADF Pipeline Definition** | ARM template or JSON | Pipeline with Copy activity, Tumbling Window trigger, and Key Vault-linked connection |
| 5 | **Databricks Transformation Notebook** | `.ipynb` or `.html` | PySpark notebook: read Bronze, clean, write Silver Delta, OPTIMIZE, ZORDER BY, verify Delta time travel |
| 6 | **Synapse Gold Aggregation Script** | `.sql` | `OPENROWSET` query against Silver + `CETAS` to write Gold Parquet |
| 7 | **Power BI Dashboard** | `.pbix` or screenshot | Time-series line chart from Gold source with region/income-group slicers, axis labels, and tooltip |
| 8 | **Cost Analysis Report** | PDF or Markdown | Estimated total execution cost of the end-to-end pipeline with breakdown by service |
| 9 | **GitHub Repository** | URL | Public or private repo containing all deliverables, a `README.md` with setup instructions, and a documented project structure |

## 8. Success Criteria

Each deliverable is evaluated against these criteria:

| Deliverable | Pass | Distinction |
|---|---|---|
| Architecture Diagram | Shows all 7 services with arrows connecting them | Includes medallion layer boundaries, data format labels (CSV → Delta → Parquet), and trigger annotations |
| Data Model | Documents columns, types, and partition keys for all 3 layers | Includes row estimates, column-level nullability, and a data dictionary cross-reference |
| Metadata List | Lists all tables and key columns with source and description | Includes sensitivity classification (Public/Internal/Confidential) and owner tag |
| ADF Pipeline | Runs without hardcoded secrets; trigger backfills at least one missed window | Pipeline includes parameterized source path and dynamic error handling |
| Databricks Notebook | Executes in under 10 min on a Job cluster; Delta history shows ≥ 2 versions | Includes `OPTIMIZE` and `ZORDER BY` with documented improvement in file count |
| Synapse Script | Completes in under 30 s scanning < 1 GB; CETAS output is re-queryable | Uses external table instead of inline OPENROWSET; includes partition pruning |
| Power BI Dashboard | Line chart renders correctly; date axis is sorted | Dashboard includes a second visual (map or bar chart) with region and income-group slicers |
| Cost Report | Total cost reported and < USD 0.10 | Cost breakdown by service with recommendations for reduction |
| GitHub Repository | Link provided; README includes project title, dataset reference, and setup steps | README includes a directory map, prerequisites section, and CI badge |

## 9. Submission & Timeline

| Milestone | Suggested Deadline | Deliverable |
|---|---|---|
| Architecture & design approved | End of Day 1 | Architecture diagram + data model + metadata list |
| Environment provisioned | End of Day 2 | ADLS + ADF + Databricks workspace running |
| Raw data ingested | End of Day 3 | Bronze CSV loaded via ADF pipeline with trigger |
| Silver layer complete | End of Day 4 | Delta table with OPTIMIZE run |
| Gold aggregation | End of Day 5 | Synapse CETAS produces Gold Parquet |
| Dashboard & cost report | End of Day 6 | Power BI file + cost analysis |
| Repository finalised | End of Day 7 | All 9 deliverables in GitHub repo |
| Final submission | End of Day 8 | Repo URL submitted for review |

## 10. Stretch Goals (Optional)

- Add an incremental load pattern using a watermark table
- Register the data pipeline in Microsoft Purview and submit a screenshot of the lineage graph
- Replace the interactive cluster with a Job cluster for every notebook execution
- Join the country_metadata.csv geo-coordinates into Silver to enable map-based Power BI visuals
- Build a second Gold aggregation: quarter-over-quarter change in economic_stress_score by region
- Add a GitHub Actions workflow that runs a schema validation script on every push to the repo

---

**Constraints:**
- All deliverables must reference the Global Geo-Economic Stress Indicators dataset
- No hardcoded secrets — all connections use Managed Identities
- Resources must carry `Project:ClimatePipeline` and `Environment:Training` tags
- The pipeline must be fully serverless — no VMs or provisioned DWUs
- The scenario must be distinct from the individual day exercises — it is a synthesis, not a repeat
- 3rd person throughout — no you / I / we / your / our

---

*Capstone project brief is complete. The brief is ready for learner distribution.*

tags:
  topic-slug: azure-data-engineering
  skill-domains: [data-engineering, cloud-computing, data-pipelines, data-warehousing]
  audience: junior-data-engineers
  difficulty: beginner
  total-active-days: 12
  total-theory-days: 3
---

# Azure Data Engineering — Hands-On Activities

## Metadata

| Field | Value |
|---|---|
| **Title** | Azure Data Engineering Training |
| **Version** | v1.0 |
| **Date Created** | 2026-07-06 |
| **Target Audience** | Junior data engineers with foundational data knowledge |
| **Prerequisites** | Medallion Architecture, ETL/ELT fundamentals, Apache Spark & SQL basics, cloud computing fundamentals |
| **Tools Needed** | Azure subscription (trial or sandbox), ADLS Gen2, Azure Data Factory, Azure Databricks, Synapse Analytics, Power BI Desktop, Azure Key Vault |
| **Dataset** | [Climate Change Global Temperature Data](https://www.kaggle.com/datasets/sachinsarkar/climate-change-global-temperature-data) — Kaggle, derived from Berkeley Earth (CC BY-NC 4.0) |
| **Total Duration** | 15 days (12 hands-on, 3 theory) |
| **Suggested Pace** | 8 hours per day — mix of lecture, workshop, and hands-on lab |

---

## Hands-On Activities

---

### Activity 1 — Day 2: ADLS Gen2 — Storage Tiers, Lifecycle Management & File Formats

| Field | Content |
|---|---|
| **Day** | Day 2 — ADLS Gen2 — Storage Tiers, Lifecycle Management & File Formats |
| **Learning Objective** | Provision an ADLS Gen2 storage account and configure storage tiers, lifecycle policies, and file format transformations. |
| **Activity** | **Provision ADLS Gen2 and Stage Raw Data** — Learners create a storage account with HNS enabled, configure Hot → Cool lifecycle policies, upload the Kaggle climate temperature CSV (`GlobalTemperatures.csv`) to a `bronze/` container, and inspect the HNS directory structure using Storage Browser. |
| **Tools / Services** | Azure Storage Account (ADLS Gen2), Storage Browser |
| **Expected Output** | A configured storage layer (ADLS Gen2 with HNS, bronze container, lifecycle policy) ready for the Day 3 hands-on. |
| **Prerequisite** | None (first hands-on activity) |

---

### Activity 2 — Day 3: Batch vs Streaming Data Storage, Data Lake Design & Partitioning

| Field | Content |
|---|---|
| **Day** | Day 3 — Batch vs Streaming Data Storage, Data Lake Design & Partitioning |
| **Learning Objective** | Design a data lake directory structure with Hive-style partitioning strategies and evaluate batch versus streaming storage requirements. |
| **Activity** | **Design Partitioned Layout and Convert to Parquet** — Using the Day 2 storage account, learners design a Hive-partitioned directory structure under `silver/climate/year=*/` using the `dt` column from `GlobalTemperatures.csv`, load a subset of the Kaggle dataset into the partitioned directory, and convert CSV to Parquet format using ADF Copy Activity. |
| **Tools / Services** | ADLS Gen2, Azure Data Factory |
| **Expected Output** | A partitioned Silver layer (Parquet, Hive-partitioned by year) ready for Day 4 pipeline orchestration. |
| **Prerequisite** | Day 2 (ADLS Gen2 provisioned) |

---

### Activity 3 — Day 4: Azure Data Factory — Pipelines, Activities & Debugging

| Field | Content |
|---|---|
| **Day** | Day 4 — Azure Data Factory — Pipelines, Activities & Debugging |
| **Learning Objective** | Build and debug a basic ADF pipeline with copy activity and dataset configuration. |
| **Activity** | **Build ADF Pipeline for Bronze Ingestion** — Learners create an ADF instance, configure linked services pointing to the Day 2 storage account, build a Copy Data pipeline that moves the raw CSV from `bronze/` to a staging location, run debug mode, and validate output in Storage Browser. |
| **Tools / Services** | Azure Data Factory, ADLS Gen2, Storage Browser |
| **Expected Output** | A reusable ADF pipeline that feeds the Day 5 parameterization exercise. |
| **Prerequisite** | Day 2 (ADLS Gen2 with bronze container) |

---

### Activity 4 — Day 5: ADF Triggers, Variables & Integration Runtimes

| Field | Content |
|---|---|
| **Day** | Day 5 — ADF Triggers, Variables & Integration Runtimes |
| **Learning Objective** | Configure schedule and event-based triggers, parameterize pipelines with variables, and manage Integration Runtimes. |
| **Activity** | **Parameterize Pipeline and Add Tumbling Window Trigger** — Learners take the Day 4 pipeline and add parameters for source file path and year filter, create a tumbling window trigger that runs daily, integrate Key Vault for the storage account key (preview of Day 13), and test backfill execution. |
| **Tools / Services** | Azure Data Factory, Azure Key Vault (preview) |
| **Expected Output** | A parameterized, scheduled ADF pipeline that feeds the Day 6 monitoring exercise. |
| **Prerequisite** | Day 4 (basic ADF pipeline) |

---

### Activity 5 — Day 6: Pipeline Monitoring, Troubleshooting & Batch Ingestion Patterns

| Field | Content |
|---|---|
| **Day** | Day 6 — Pipeline Monitoring, Troubleshooting & Batch Ingestion Patterns |
| **Learning Objective** | Monitor ADF pipeline runs, diagnose failures, and implement batch ingestion patterns for incremental and full loads. |
| **Activity** | **Create Failure Alert and Build Watermark Table** — Learners set up email alerts on the Day 5 pipeline, intentionally introduce a failure (wrong file path), diagnose the error in Monitor hub, then implement a watermark table in ADLS (stored as JSON) that tracks last processed date, and modify the pipeline to read only new records since the watermark timestamp using the Kaggle dataset ingestion. |
| **Tools / Services** | Azure Data Factory, ADLS Gen2, Azure Monitor |
| **Expected Output** | A monitored, incremental-load pipeline feeding the Day 7 Databricks workspace. |
| **Prerequisite** | Day 5 (parameterized pipeline with trigger) |

---

### Activity 6 — Day 7: Azure Databricks Environments & Cluster Configuration

| Field | Content |
|---|---|
| **Day** | Day 7 — Azure Databricks Environments & Cluster Configuration |
| **Learning Objective** | Set up an Azure Databricks workspace, configure clusters with auto-termination policies, and navigate the notebook environment. |
| **Activity** | **Create Databricks Workspace and Mount ADLS** — Learners provision a Databricks workspace, create an interactive cluster with 20-minute auto-termination and Spot VMs, mount the Day 2 ADLS Gen2 account to DBFS using service principal credentials, and navigate the notebook interface. |
| **Tools / Services** | Azure Databricks, ADLS Gen2 |
| **Expected Output** | A configured Databricks environment with mounted storage ready for the Day 8 PySpark exercise. |
| **Prerequisite** | Day 2 (ADLS Gen2) |

---

### Activity 7 — Day 8: PySpark Data Transformation with Delta Lake

| Field | Content |
|---|---|
| **Day** | Day 8 — PySpark Data Transformation with Delta Lake |
| **Learning Objective** | Write PySpark transformations using DataFrames and Delta Lake operations to read, clean, and write batch data. |
| **Activity** | **Transform Climate Data with PySpark and Delta Lake** — Learners read the raw `GlobalTemperatures.csv` from the ADLS mount, clean null values and uncertainty columns, enforce schema, write to a Bronze Delta table, read Bronze and apply de-duplication and date formatting, write to a Silver Delta table partitioned by year, run `OPTIMIZE` and `ZORDER BY` on the Silver table, and verify time travel by querying table history. |
| **Tools / Services** | Azure Databricks, Delta Lake, ADLS Gen2 |
| **Expected Output** | A cleaned Silver Delta table that feeds the Day 9 virtualization comparison. |
| **Prerequisite** | Day 7 (Databricks workspace with ADLS mount) |

---

### Activity 8 — Day 9: Data Virtualization vs Physical Ingestion, Job Clusters & Cost Optimization

| Field | Content |
|---|---|
| **Day** | Day 9 — Data Virtualization vs Physical Ingestion, Job Clusters & Cost Optimization |
| **Learning Objective** | Compare data virtualization and physical ingestion strategies, and configure Databricks Job clusters for production pipelines. |
| **Activity** | **Compare Virtualization vs Ingestion and Create Job Cluster** — Learners query the raw CSV directly (virtualization) and time the execution, compare it to querying the Day 8 Silver Delta table (physical ingestion), document the latency and cost difference using estimated DBU consumption, then create a Databricks Job that runs the Day 8 notebook on a Job cluster. |
| **Tools / Services** | Azure Databricks (interactive + Job clusters) |
| **Expected Output** | A cost comparison report and a scheduled Databricks Job that feeds the Day 10 Synapse exercise. |
| **Prerequisite** | Day 8 (Silver Delta table) |

---

### Activity 9 — Day 10: Synapse Analytics — Serverless SQL & Data Virtualization

| Field | Content |
|---|---|
| **Day** | Day 10 — Synapse Analytics — Serverless SQL & Data Virtualization |
| **Learning Objective** | Query data lakes using Synapse Serverless SQL and practice data virtualization without provisioning dedicated infrastructure. |
| **Activity** | **Query Silver Delta Table with Serverless SQL and Write Gold Aggregation** — Learners create a Synapse workspace, write an `OPENROWSET` query against the Silver Delta table from Day 8, aggregate average temperature by year across the Kaggle dataset, use `CETAS` to write the aggregation as a Gold Parquet file, and compare the cost of scanning the full table vs scanning only the Gold aggregated file. |
| **Tools / Services** | Synapse Analytics (Serverless SQL), ADLS Gen2 |
| **Expected Output** | A Gold layer dataset (Parquet, aggregated by year) and a cost comparison report feeding Day 11. |
| **Prerequisite** | Day 8 (Silver Delta table) |

---

### Activity 10 — Day 13: Security — Key Vault, RBAC, ACLs & Private Endpoints

| Field | Content |
|---|---|
| **Day** | Day 13 — Security — Key Vault, RBAC, ACLs & Private Endpoints |
| **Learning Objective** | Configure Azure Key Vault for secrets management, assign RBAC roles, and set up managed private endpoints for secure data access. |
| **Activity** | **Secure the Pipeline with Key Vault and Managed Identity** — Learners create a Key Vault, store the ADLS storage account key as a secret, assign a Managed Identity to the ADF instance, grant the identity Key Vault access, modify the Day 5 pipeline to retrieve the storage key from Key Vault instead of hardcoding, and verify the pipeline runs successfully. |
| **Tools / Services** | Azure Key Vault, Azure Data Factory, Managed Identity |
| **Expected Output** | A fully secured ADF pipeline that feeds the Day 14 governance exercise. |
| **Prerequisite** | Day 5 (ADF pipeline) |

---

### Activity 11 — Day 14: Governance & FinOps — Purview, Cost Management & Budget Alerts

| Field | Content |
|---|---|
| **Day** | Day 14 — Governance & FinOps — Purview, Cost Management & Budget Alerts |
| **Learning Objective** | Register data assets in Microsoft Purview, configure Azure Cost Management budgets, and apply cost optimization strategies. |
| **Activity** | **Tag Resources, Set Budget Alert, and Register in Purview** — Learners tag all provisioned resources with `Project:ClimatePipeline` and `Environment:Training`, create a $50 monthly budget alert in Cost Management, register the ADLS Gen2 and Dedicated SQL pool in Purview, run a scan, and view the auto-generated lineage map showing ADF → Databricks → Synapse data flow across the Kaggle climate pipeline. |
| **Tools / Services** | Azure Cost Management, Microsoft Purview, ADLS Gen2 |
| **Expected Output** | A tagged, budget-monitored, and cataloged environment ready for the Day 15 capstone. |
| **Prerequisite** | Day 13 (secured pipeline) |

---

### Theory Days (Excluded from Activities)

The following days are theory-only and contain no hands-on activity:

| Day | Topic |
|---|---|
| Day 1 | Azure Data Engineering Overview, Cloud & Lakehouse Concepts |
| Day 11 | Data Warehousing Design (Star Schema) & Dedicated SQL Pools |
| Day 12 | Microsoft Fabric — OneLake, Notebooks & Warehouse |

---

## Activity Dependency Map

```
Day 2 ──► Day 3 ──► Day 4 ──► Day 5 ──► Day 6
                                                  │
Day 2 ──────────────────────────────────────────► Day 7 ──► Day 8 ──► Day 9
                                                                       │
                                                              Day 8 ──► Day 10
                                                                       
Day 5 ───────────────────────────────────────────────────────────────► Day 13 ──► Day 14
```

## Consolidated Checklist

| # | Day | Activity | Output |
|---|---|---|---|
| 1 | Day 2 | Provision ADLS Gen2 and Stage Raw Data | Configured storage layer with bronze container |
| 2 | Day 3 | Design Partitioned Layout and Convert to Parquet | Silver layer — Parquet, Hive-partitioned by year |
| 3 | Day 4 | Build ADF Pipeline for Bronze Ingestion | Reusable ADF pipeline |
| 4 | Day 5 | Parameterize Pipeline and Add Tumbling Window Trigger | Parameterized, scheduled ADF pipeline |
| 5 | Day 6 | Create Failure Alert and Build Watermark Table | Monitored, incremental-load pipeline |
| 6 | Day 7 | Create Databricks Workspace and Mount ADLS | Databricks workspace with ADLS mount |
| 7 | Day 8 | Transform Climate Data with PySpark and Delta Lake | Silver Delta table (cleaned, partitioned) |
| 8 | Day 9 | Compare Virtualization vs Ingestion and Create Job Cluster | Cost comparison report + Databricks Job |
| 9 | Day 10 | Query Silver Delta with Serverless SQL and Write Gold Aggregation | Gold Parquet dataset + cost report |
| 10 | Day 13 | Secure the Pipeline with Key Vault and Managed Identity | Secured ADF pipeline |
| 11 | Day 14 | Tag Resources, Set Budget Alert, and Register in Purview | Tagged, monitored, cataloged environment |

---

*Hands-on activity document is complete. Distribute to learners or trigger Skill 7 to publish as HTML.*

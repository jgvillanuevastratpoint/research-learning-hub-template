#review: DRAFT

# Azure Data Engineering Training
### 04 — Azure Data Engineering — Visuals

---

## Visuals

---

### Day 1 — Azure Data Engineering Overview, Cloud & Lakehouse Concepts

**Diagram type:** Architecture Diagram

**Caption/Alt-text:**
> This architecture diagram illustrates the end-to-end Azure data engineering ecosystem. Data flows from sources through Azure Data Factory (ingestion) into ADLS Gen2 (storage), then to Azure Databricks (processing) and Synapse Analytics (warehousing), and finally to Power BI (visualization). Security and governance are provided by Azure Key Vault and Microsoft Purview, while Azure Monitor handles observability across all layers.

```mermaid
graph LR
    subgraph "Sources"
        ONPREM["On-Premises Databases"]
        SAAS["SaaS Applications"]
        APIS["APIs & Event Hubs"]
    end

    subgraph "Ingestion & Orchestration"
        ADF["Azure Data Factory"]
    end

    subgraph "Storage"
        ADLS["ADLS Gen2<br/>Hierarchical Namespace"]
    end

    subgraph "Processing & Transformation"
        DB["Azure Databricks<br/>Apache Spark"]
        SYNAPSE["Azure Synapse Analytics"]
    end

    subgraph "Visualization"
        PBI["Power BI"]
    end

    subgraph "Security & Governance"
        KV["Azure Key Vault"]
        PURVIEW["Microsoft Purview"]
    end

    subgraph "Monitoring"
        MONITOR["Azure Monitor"]
    end

    ONPREM --> ADF
    SAAS --> ADF
    APIS --> ADF
    ADF --> ADLS
    ADLS --> DB
    ADLS --> SYNAPSE
    DB --> SYNAPSE
    SYNAPSE --> PBI
    KV -.->|secrets| ADF
    KV -.->|secrets| DB
    PURVIEW -.->|lineage| ADLS
    MONITOR -.->|alerts| ADF
```

---

### Day 2 — ADLS Gen2: Storage Tiers, Lifecycle Management & File Formats

**Diagram type:** Architecture Diagram

**Caption/Alt-text:**
> This diagram shows the structure of an ADLS Gen2 storage account with Hierarchical Namespace enabled. Data is organized into directories and containers, with lifecycle policies automatically transitioning data across Hot, Cool, and Archive tiers. Supported file formats include CSV, Parquet, and Delta Lake (Parquet with a transaction log).

```mermaid
graph TD
    subgraph "ADLS Gen2 Storage Account"
        HNS["Hierarchical Namespace (HNS)"]
        CONTAINER["Container: jobmarket"]
        CONTAINER --> DIR1["bronze/"]
        CONTAINER --> DIR2["silver/"]
        CONTAINER --> DIR3["gold/"]
    end

    subgraph "Access Tiers"
        HOT["Hot Tier<br/>~$0.023/GB/month"]
        COOL["Cool Tier<br/>~$0.0125/GB/month"]
        ARCHIVE["Archive Tier<br/>~$0.002/GB/month"]
    end

    subgraph "Lifecycle Management"
        POLICY["Lifecycle Policy"]
        POLICY -->|"age > 30 days"| HOT2COOL["Hot → Cool"]
        POLICY -->|"age > 90 days"| COOL2ARCHIVE["Cool → Archive"]
    end

    subgraph "File Formats"
        CSV["CSV (Row-based)"]
        PARQUET["Parquet (Columnar)"]
        DELTA["Delta Lake<br/>Parquet + _delta_log"]
    end

    DIR1 --> CSV
    DIR2 --> PARQUET
    DIR3 --> DELTA
    HNS --> CONTAINER
    HOT --> COOL
    COOL --> ARCHIVE
```

---

### Day 3 — Batch vs Streaming Data Storage, Data Lake Design & Partitioning

**Diagram type:** Flowchart

**Caption/Alt-text:**
> This flowchart shows the data lake directory design process, starting with the batch vs streaming decision. The batch path follows Hive-style partitioning (`source/layer/year=YYYY/month=MM/day=DD/`) and includes compaction logic to address the small file problem. The streaming path routes to event-based storage for continuous ingestion.

```mermaid
graph TD
    DECISION["Data Source Type?"]
    BATCH["Batch Data<br/>Scheduled intervals"]
    STREAMING["Streaming Data<br/>Continuous events"]
    
    subgraph "Batch Path"
        HIVE["Hive-Style Partitioning<br/>source/layer/year=*/month=*/"]
        PARTITION["Partition Pruning<br/>Skip irrelevant directories"]
        SMALL["Small File Problem?"]
        COMPACT["Run Compaction<br/>Merge to 128-512MB blocks"]
        QUERY["Optimized Query Performance"]
    end

    subgraph "Streaming Path"
        EVENTS["Event Hubs / IoT Hub"]
        STORAGE["Stream-Storage<br/>Append blobs"]
    end

    DECISION -->|"Batch"| BATCH
    DECISION -->|"Streaming"| STREAMING
    BATCH --> HIVE
    HIVE --> PARTITION
    PARTITION --> SMALL
    SMALL -->|"Yes"| COMPACT
    SMALL -->|"No"| QUERY
    COMPACT --> QUERY
    STREAMING --> EVENTS
    EVENTS --> STORAGE
```

---

### Day 4 — Azure Data Factory: Pipelines, Activities & Debugging

**Diagram type:** Flowchart

**Caption/Alt-text:**
> This flowchart shows the structure of an Azure Data Factory pipeline. A trigger initiates execution, the pipeline runs activities in dependency order with linked services and datasets defining connections and data references. Integration Runtimes handle connectivity, and each run is logged to the Monitor hub for debugging and validation.

```mermaid
graph LR
    TRIGGER["Trigger<br/>Schedule / Event / Tumbling"]
    PIPELINE["ADF Pipeline"]
    
    subgraph "Activities"
        COPY["Copy Data<br/>Source → Sink"]
        WEB["Web Activity"]
        NOTEBOOK["Execute Notebook"]
    end

    subgraph "Connectivity"
        LS["Linked Service<br/>Connection String"]
        DS["Dataset<br/>Data Reference"]
        IR["Integration Runtime<br/>Azure IR / SHIR"]
    end

    subgraph "Monitoring"
        MONITOR["Monitor Hub"]
        LOGS["Activity Run Logs"]
        ERRORS["Error Messages"]
    end

    TRIGGER --> PIPELINE
    PIPELINE --> COPY
    PIPELINE --> WEB
    PIPELINE --> NOTEBOOK
    COPY --> LS
    COPY --> DS
    LS --> IR
    PIPELINE --> MONITOR
    MONITOR --> LOGS
    MONITOR --> ERRORS
```

---

### Day 5 — ADF Triggers, Variables & Integration Runtimes

**Diagram type:** Flowchart

**Caption/Alt-text:**
> This flowchart illustrates how ADF triggers initiate pipeline execution. Schedule triggers run on clock intervals, Tumbling Window triggers maintain non-overlapping historical windows with built-in retry, and Event triggers react to storage blob creation. Parameters and variables are passed dynamically into pipeline activities.

```mermaid
graph TD
    subgraph "Trigger Types"
        SCHEDULE["Schedule Trigger<br/>Every 24 hours"]
        TW["Tumbling Window Trigger<br/>Non-overlapping windows"]
        EVENT["Event Trigger<br/>Blob created"]
    end

    subgraph "Pipeline Execution"
        PARAMS["Pipeline Parameters<br/>@pipeline().parameters"]
        VARS["Variables"]
        ACTIVITIES["Activities<br/>Copy / Web / Notebook"]
    end

    subgraph "Integration Runtimes"
        AIR["Azure IR<br/>Cloud-to-cloud"]
        SHIR["Self-Hosted IR<br/>On-premises gateway"]
    end

    subgraph "Output"
        SINK["ADLS Gen2 Sink"]
        LOGS["Execution Logs"]
    end

    SCHEDULE --> PARAMS
    TW --> PARAMS
    EVENT --> PARAMS
    PARAMS --> ACTIVITIES
    VARS --> ACTIVITIES
    ACTIVITIES --> AIR
    ACTIVITIES --> SHIR
    AIR --> SINK
    SHIR --> SINK
    ACTIVITIES --> LOGS
```

---

### Day 6 — Pipeline Monitoring, Troubleshooting & Batch Ingestion Patterns

**Diagram type:** Concept Map

**Caption/Alt-text:**
> This concept map shows the three key areas of Day 6 learning: monitoring via ADF Monitor hub and alert rules, troubleshooting through activity log analysis, and batch ingestion patterns including full load, incremental load with watermark tables, and upsert using Delta Lake merge operations.

```mermaid
graph TD
    MONITORING["Pipeline Monitoring<br/>& Troubleshooting"]
    
    subgraph "Monitor"
        MHUB["ADF Monitor Hub"]
        STATUS["Run Status<br/>Succeeded / Failed"]
        ALERTS["Alert Rules<br/>Email / Webhook"]
    end

    subgraph "Troubleshoot"
        LOGS["Activity Run Logs"]
        DIAG["Error Diagnostics"]
        RETRY["Retry Policies"]
    end

    subgraph "Ingestion Patterns"
        FULL["Full Load<br/>Reload entire table"]
        INCREMENTAL["Incremental Load<br/>Watermark table"]
        UPSERT["Upsert / Merge<br/>Delta Lake MERGE"]
    end

    subgraph "Watermark Mechanism"
        WT["Watermark Table<br/>last_processed_timestamp"]
        EXTRACT["Extract WHERE timestamp > watermark"]
        UPDATE["Update watermark on success"]
    end

    MONITORING --> MONITOR
    MONITORING --> Troubleshoot
    MONITORING --> INCREMENTAL
    FULL --> INCREMENTAL
    INCREMENTAL --> UPSERT
    INCREMENTAL --> WT
    WT --> EXTRACT
    EXTRACT --> UPDATE
```

---

### Day 7 — Azure Databricks Environments & Cluster Configuration

**Diagram type:** Architecture Diagram

**Caption/Alt-text:**
> This architecture diagram shows the Azure Databricks workspace structure. Users create Interactive clusters for ad-hoc development (with auto-termination and Spot VMs for cost control) and Job clusters for production execution. Clusters mount ADLS Gen2 via service principal credentials, and notebooks run PySpark code against the mounted data.

```mermaid
graph TD
    subgraph "Azure Databricks Workspace"
        WS["Databricks Workspace"]
    end

    subgraph "Cluster Types"
        INTERACTIVE["Interactive Cluster<br/>Manual start/stop<br/>Higher cost"]
        JOB["Job Cluster<br/>Auto-provisioned<br/>Lower cost"]
    end

    subgraph "Cost Optimization"
        TERM["Auto-Termination<br/>20-min idle timeout"]
        SPOT["Spot VMs<br/>Up to 80% discount"]
        SCALE["Auto-Scaling<br/>2-8 nodes"]
    end

    subgraph "Data Access"
        MOUNT["ADLS Mount<br/>/mnt/weatherdata"]
        SP["Service Principal<br/>Azure AD Auth"]
    end

    subgraph "Notebooks"
        NB1["Ingestion Notebook"]
        NB2["Transform Notebook"]
        NB3["Aggregation Notebook"]
    end

    WS --> INTERACTIVE
    WS --> JOB
    INTERACTIVE --> TERM
    INTERACTIVE --> SPOT
    JOB --> SCALE
    INTERACTIVE --> MOUNT
    JOB --> MOUNT
    MOUNT --> SP
    INTERACTIVE --> NB1
    INTERACTIVE --> NB2
    JOB --> NB3
```

---

### Day 8 — PySpark Data Transformation with Delta Lake

**Diagram type:** Flowchart

**Caption/Alt-text:**
> This flowchart shows the PySpark data transformation pipeline using Delta Lake. Raw CSV data is read from Bronze, cleaned and transformed through DataFrame operations (filter, dropNulls, withColumn), then written to a Silver Delta table. The Delta transaction log enables time travel. Maintenance operations include OPTIMIZE for compaction, ZORDER BY for data co-location, and VACUUM for old version cleanup.

```mermaid
graph LR
    subgraph "Bronze"
        CSV["Raw CSV<br/>ADLS Bronze"]
    end

    subgraph "Transformations"
        READ["spark.read.csv(...)"]
        CLEAN["Filter nulls<br/>Drop duplicates"]
        TRANSFORM["withColumn<br/>Select / GroupBy"]
        WRITE["write.format('delta')"]
    end

    subgraph "Delta Lake"
        DT["Delta Table<br/>Silver Layer"]
        TLOG["_delta_log<br/>Transaction Log"]
        TIMETRAVEL["Time Travel<br/>VERSION AS OF"]
    end

    subgraph "Maintenance"
        OPT["OPTIMIZE<br/>Compact small files"]
        ZORDER["ZORDER BY<br/>Co-locate columns"]
        VACUUM["VACUUM<br/>Purge old versions"]
    end

    CSV --> READ
    READ --> CLEAN
    CLEAN --> TRANSFORM
    TRANSFORM --> WRITE
    WRITE --> DT
    DT --> TLOG
    TLOG --> TIMETRAVEL
    DT --> OPT
    OPT --> ZORDER
    ZORDER --> VACUUM
```

---

### Day 9 — Data Virtualization vs Physical Ingestion, Job Clusters & Cost Optimization

**Diagram type:** Comparison Diagram (Flowchart)

**Caption/Alt-text:**
> This comparison flowchart shows the two data access strategies. The virtualization path queries external sources directly without copying data — faster to develop but slower at query time. The physical ingestion path copies data into local Delta tables — slower to set up but faster for repeated queries. A cost comparison table shows the trade-offs in storage, compute, and query performance.

```mermaid
graph LR
    subgraph "Source"
        SRC["Raw Data in ADLS Gen2<br/>CSV / Parquet Files"]
    end

    subgraph "Virtualization Path"
        V1["Spark reads external path<br/>spark.read.format('csv')"]
        V2["No data copy<br/>Zero storage cost"]
        V3["Slower queries<br/>Repeat scans on source"]
    end

    subgraph "Physical Ingestion Path"
        P1["Copy to Delta Table<br/>df.write.format('delta')"]
        P2["Data stored locally<br/>Storage cost incurred"]
        P3["Fast queries<br/>Local file access + indexing"]
    end

    SRC --> V1
    SRC --> P1
    V1 --> V2
    V2 --> V3
    P1 --> P2
    P2 --> P3

    subgraph "Cost Comparison"
        C1["| Criteria | Virtualization | Physical |"]
        C2["| Storage | None | Delta size |"]
        C3["| Query Latency | Higher | Lower |"]
        C4["| Dev Speed | Fast | Setup time |"]
        C5["| Best For | Exploration | Production |"]
    end
```

---

### Day 10 — Synapse Analytics: Serverless SQL & Data Virtualization

**Diagram type:** Architecture Diagram

**Caption/Alt-text:**
> This architecture diagram shows how Synapse Serverless SQL pool executes T-SQL queries directly against files in ADLS Gen2 without provisioned infrastructure. Users connect via Synapse Studio or SSMS, write OPENROWSET queries against Parquet or Delta files, and pay only for data scanned (~$5.00/TB). CETAS materializes query results into persistent Parquet files in the Gold layer.

```mermaid
graph TD
    subgraph "Users & Tools"
        STUDIO["Synapse Studio"]
        SSMS["SSMS / Azure Data Studio"]
    end

    subgraph "Synapse Serverless SQL"
        ENDPOINT["Serverless SQL Endpoint"]
        QUERY["OPENROWSET(BULK...)"]
        CETAS["CETAS<br/>Create External Table As Select"]
    end

    subgraph "ADLS Gen2 Data Lake"
        BRONZE["Bronze Layer<br/>Raw CSV / Parquet"]
        SILVER["Silver Layer<br/>Cleaned Delta"]
        GOLD["Gold Layer<br/>Aggregated Parquet"]
    end

    subgraph "Cost Model"
        BILL["$5.00 per TB scanned"]
        FILTER["Partition Pruning<br/>Reduces scanned bytes"]
    end

    STUDIO --> ENDPOINT
    SSMS --> ENDPOINT
    ENDPOINT --> QUERY
    ENDPOINT --> CETAS
    QUERY --> BRONZE
    QUERY --> SILVER
    CETAS --> GOLD
    ENDPOINT --> BILL
    QUERY --> FILTER
```

---

### Day 11 — Data Warehousing Design (Star Schema) & Dedicated SQL Pools

**Diagram type:** Concept Map

**Caption/Alt-text:**
> This concept map shows the structure of a star schema within a Synapse Dedicated SQL pool. A central fact table (e.g., temperature readings) is surrounded by dimension tables (date, location, station). Distribution strategies — Hash (fact table), Replicate (small dimensions), and Round-Robin (staging) — are applied to optimize query performance in the MPP architecture.

```mermaid
graph TD
    subgraph "Dedicated SQL Pool (MPP)"
        MPP["60 Compute Nodes<br/>Massively Parallel Processing"]
    end

    subgraph "Fact Table"
        FACT["FactTemperature<br/>DISTRIBUTION = HASH(location_key)"]
        F1["temperature_value"]
        F2["location_key"]
        F3["date_key"]
        F4["station_key"]
    end

    subgraph "Dimension Tables"
        DIM1["DimLocation<br/>DISTRIBUTION = REPLICATE"]
        DIM2["DimDate<br/>DISTRIBUTION = REPLICATE"]
        DIM3["DimStation<br/>DISTRIBUTION = REPLICATE"]
    end

    subgraph "Distribution Types"
        HASH["Hash Distribution<br/>Shard on join key"]
        REP["Replicated<br/>Full copy on each node<br/>(< 2 GB)"]
        RR["Round-Robin<br/>Even distribution<br/>Staging tables"]
    end

    FACT --> DIM1
    FACT --> DIM2
    FACT --> DIM3
    FACT --> HASH
    DIM1 --> REP
    DIM2 --> REP
    DIM3 --> REP
    MPP --> HASH
    MPP --> REP
    MPP --> RR
```

---

### Day 12 — Microsoft Fabric: OneLake, Notebooks & Warehouse

**Diagram type:** Architecture Diagram

**Caption/Alt-text:**
> This diagram shows Microsoft Fabric's unified SaaS architecture centered on OneLake, a multi-cloud data lake. Lakehouses, Warehouses, Notebooks, Pipelines, and Real-Time Analytics all read and write the same Delta Parquet data from OneLake. Power BI connects via Direct Lake mode — querying OneLake data directly without importing or caching.

```mermaid
graph TD
    ONELAKE["OneLake<br/>Multi-Cloud Data Lake"]
    
    subgraph "Fabric Services"
        LH["Lakehouse<br/>Managed Delta Tables + Spark"]
        WH["Warehouse<br/>T-SQL Endpoint"]
        NB["Notebooks<br/>Spark Code Environment"]
        PL["Data Pipelines<br/>Code-Free Orchestration"]
        RT["Real-Time Analytics<br/>Event Processing"]
        PBI["Power BI<br/>Direct Lake Mode"]
    end

    subgraph "Data Format"
        DELTA["Delta Parquet Format"]
        ONELAKE --> DELTA
    end

    subgraph "Benefits"
        B1["No Data Movement<br/>Single copy for all workloads"]
        B2["Direct Lake<br/>Import performance + Live freshness"]
        B3["SaaS Simplicity<br/>No infrastructure provisioning"]
    end

    ONELAKE --> LH
    ONELAKE --> WH
    ONELAKE --> NB
    ONELAKE --> PL
    ONELAKE --> RT
    ONELAKE --> PBI
    PBI -.->|"Direct Lake"| ONELAKE
```

---

### Day 13 — Security: Key Vault, RBAC, ACLs & Private Endpoints

**Diagram type:** Concept Map

**Caption/Alt-text:**
> This concept map shows the four security layers in an Azure data pipeline. Key Vault stores secrets accessed via Managed Identities at runtime. RBAC controls broad resource management access (control plane). Data Lake ACLs provide granular file-level permissions (data plane). Managed Private Endpoints isolate traffic within Azure Virtual Networks, bypassing the public internet.

```mermaid
graph TD
    subgraph "Layer 1: Secrets Management"
        KV["Azure Key Vault"]
        MI["Managed Identity<br/>Azure AD Authentication"]
        KV -->|"retrieve"| MI
    end

    subgraph "Layer 2: Control Plane"
        RBAC["Role-Based Access Control"]
        ROLES["Storage Blob Data Contributor<br/>Contributor / Reader"]
        RBAC --> ROLES
    end

    subgraph "Layer 3: Data Plane"
        ACL["Data Lake ACLs<br/>POSIX Permissions"]
        ACL1["Read (r)<br/>Execute (x)"]
        ACL2["Write (w)"]
        ACL --> ACL1
        ACL --> ACL2
    end

    subgraph "Layer 4: Network"
        PE["Managed Private Endpoint"]
        VNET["Azure Virtual Network"]
        PE -->|"traffic inside"| VNET
    end

    subgraph "Secured Pipeline"
        ADF["ADF Pipeline"]
        DB["Databricks"]
        STORAGE["ADLS Gen2"]
    end

    MI --> ADF
    MI --> DB
    ROLES --> STORAGE
    ACL --> STORAGE
    PE --> STORAGE
```

---

### Day 14 — Governance & FinOps: Purview, Cost Management & Budget Alerts

**Diagram type:** Flowchart

**Caption/Alt-text:**
> This flowchart shows the FinOps lifecycle for Azure data pipelines. Resources are tagged with metadata (environment, project, owner) at provisioning time. Cost Management evaluates tagged resources against budget thresholds (50%, 90%, 100%), triggering alerts or automated actions. Purview scans data sources for classification and lineage, creating a searchable data catalog.

```mermaid
graph LR
    subgraph "Provision"
        TAG["Tag Resources<br/>Environment:Training<br/>Project:WeatherPipeline"]
    end

    subgraph "Track"
        CM["Azure Cost Management"]
        ANALYZE["Cost Analysis by Tag"]
        BUDGET["Budget: $50/month"]
    end

    subgraph "Alert"
        A50["Alert: 50% Used<br/>Notification"]
        A90["Alert: 90% Used<br/>Notification"]
        A100["Alert: 100% Used<br/>Auto-shutdown"]
    end

    subgraph "Govern"
        PURVIEW["Microsoft Purview"]
        SCAN["Scan Data Sources"]
        CLASSIFY["Classify Sensitive Data"]
        LINEAGE["Track Data Lineage<br/>ADF → Databricks → Synapse"]
    end

    TAG --> CM
    CM --> ANALYZE
    ANALYZE --> BUDGET
    BUDGET --> A50
    A50 --> A90
    A90 --> A100
    TAG --> PURVIEW
    PURVIEW --> SCAN
    SCAN --> CLASSIFY
    SCAN --> LINEAGE
```

---

*Version: v1.0 | Created: 2026-07-06 | Author: Technical Curriculum Designer*

Visual complete. Copy the Mermaid code blocks into the relevant modules in the learning path document. Trigger Skill 4 again for any remaining flagged modules, or proceed to Skill 5: Quiz Generator when all visuals are done.

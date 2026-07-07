# Azure Data Engineering Training Curriculum & Costing Guide

This curriculum guide outlines the comprehensive roadmap for Azure Data Engineering training. It balances foundational architectural concepts, hands-on engineering workflows, and critical cloud governance/FinOps principles.

---

## Complete Curriculum & Costing Matrix

| Module & Lifecycle Stage | Core Concepts to Teach | Azure / Fabric Service | Costing Model & Pricing Mechanism (Approx. Retail) | FinOps & Optimization Best Practices | Remarks & Training Tips |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Storage & Architecture Foundations** | • Medallion Architecture (Bronze/Silver/Gold)<br>• Parquet, Delta Lake, CSV, JSON formats<br>• Hierarchical Namespaces<br>• Batch vs. Streaming data storage | • **ADLS Gen2**<br>• **Fabric OneLake** | **Consumption-based:** Volume stored + Transaction fees.<br>• *Hot Tier:* ~\$0.023 per GB/month<br>• *Cool Tier:* ~\$0.0125 per GB/month | • Use **Lifecycle Management Policies** to automatically archive old data to cheaper tiers.<br>• Optimize file sizes (avoid the "small file problem"). | Critical dependency for all subsequent labs. Ensure students master the difference between ADLS Gen2 (IaaS) and Fabric OneLake (SaaS) early on. Medallion architeture should be added as prerequisite and out of scope ing this training.Batch vs streaming data storage is required to be added. |
| **2. Ingestion & Orchestration (ETL/ELT)** | • ETL vs. ELT architectures<br>• Pipelines, activities, triggers, & variables<br>• Integration Runtimes (IR) for on-prem connectivity | • **Azure Data Factory (ADF)**<br>• **Fabric Data Factory** | **Consumption-based:** Per activity run + processing time.<br>• *Orchestration:* \$1.00 per 1,000 runs<br>• *Data Movement:* \$0.25 per DIU-hour<br>• *Data Flows:* ~\$0.274 per vCore-hour | • Avoid massive nested `ForEach` loops to prevent runaway orchestration bills.<br>• Suspend or adjust pipeline triggers in non-production environments. | Provide an on-premises mockup environment or a simulated local gateway database to teach Self-Hosted Integration Runtimes (SHIR). Out of scope/Prerequisite: ETL and ELT. Required: Pipeline activities, pipeline monitoring and troubleshooting. ADF and Fabric Data Factory |
| **3. Big Data Processing & Transformation** | • Distributed Computing (Apache Spark architecture)<br>• PySpark, Spark SQL, and Scala languages<br>• Data Virtualization vs. Physical ingestion | • **Azure Databricks**<br>• **Synapse Spark Pools**<br>• **Fabric Notebooks** | **Two-Part Billing:** Databricks Units (DBU) + Azure VM costs.<br>• *Jobs Compute (ETL):* ~\$0.30 per DBU/hour<br>• *All-Purpose (Interactive):* ~\$0.55 per DBU/hour | • **Mandatory:** Set a 15–20 minute **Auto-Termination** policy on all interactive clusters.<br>• Use cheaper Single-Node or Spot VMs for development and automated Job clusters for production. | Databricks is the market leader for custom Spark engineering. Spend more time on PySpark and cluster sizing best practices. Out of scope/ Prerequisite: Apache Spark and SQL. Required: Data Virtualization vs Physical ingestion. Which one is cheaper for the training? Recommend a cheaper data architecture for the whole training and best practices too.|
| **4. Enterprise Data Warehousing** | • Star & Snowflake schemas (Facts/Dimensions)<br>• Massively Parallel Processing (MPP)<br>• Data distributions (Hash, Round-Robin, Replicated) | • **Synapse Dedicated SQL**<br>• **Fabric Warehouse**<br>• **Azure SQL Database** | **Provisioned Capacity or Unified SKU:**<br>• *Fabric F2 SKU:* ~\$0.36/hour (Entry-level)<br>• *Fabric F64 SKU:* ~\$11.52/hour (Prod standard)<br>• *Synapse Dedicated:* Scaled by DWUs | • Pause Fabric capacities or Synapse pools during weekends and off-business hours.<br>• Use Serverless SQL pools for ad-hoc exploration to pay strictly per query (~\$5.00 per TB scanned). | Emphasize data distribution strategies. Poor distribution design is the #1 reason enterprise SQL warehouses run slowly and expensively. |
| **5. Real-Time Analytics & Streaming** | • Windowing Functions (Tumbling, Hopping, Sliding)<br>• Event Ingestion at scale<br>• Telemetry & Log analysis | • **Azure Event Hubs**<br>• **Stream Analytics**<br>• **Fabric Real-Time Intel** | **Throughput / Processing Units:**<br>• *Event Hubs:* Billed per Throughput Unit (TU) per hour + ingress events.<br>• *Stream Analytics:* Billed per Streaming Unit (SU) used. | • Match Throughput Units closely to actual peak event volumes.<br>• Implement auto-inflate features cautiously to control sudden traffic-spike costs. | Use a free real-time mock tool (like a Python script sending dummy IoT logs) so students can see streaming events update instantly. Out of scope: this section is not to be included. focus the training for batch data processing.  |
| **6. Security, Governance, & Monitoring** | • Managed Private Endpoints & VNets<br>• RBAC vs. Data Lake ACLs<br>• Data Lineage & Data Masking<br>• Alerts & Log queries | • **Azure Key Vault**<br>• **Microsoft Purview**<br>• **Azure Monitor** | **Mixed Models:** Key Vault is fraction-of-a-cent per transaction; Purview is billed per asset/scanning hour; Monitor is billed per GB of logs ingested. | • Filter diagnostic logging to only record critical events and pipeline failures.<br>• Set automated billing alerts and budget thresholds in **Azure Cost Management** to halt environments if budgets are breached. | Do not save this for last. Integrate Key Vault configuration into Module 2 to build industry-standard security hygiene immediately. |

---

## Curriculum Blueprint Details

### 1. Architectural Foundations
Before processing or modeling data, data engineers must understand where storage lives, how it is structures, and file formatting paradigms.
* **The Medallion Framework:** Transitioning files reliably from unstructured raw states (`Bronze`), cleaned/conformed granular records (`Silver`), to highly aggregated, business-ready data assets (`Gold`).
* **Format Optimization:** Why column-oriented structures like **Apache Parquet** and transactional layers like **Delta Lake** outperform row-based structures (CSV, JSON) for scalable enterprise query operations.

### 2. Strategic FinOps Concepts
A pipeline's commercial performance matters as much as its runtime stability. Training elements should emphasize these critical patterns:
* **Decoupled Architecture:** Storing data efficiently long-term and activating compute instances only on an on-demand basis.
* **Commitment Strategies:** Understanding when to choose Pay-As-You-Go models versus reserving capacities (1 or 3-year timelines) for baseline infrastructure loads to capture up to 40% cost reductions.

---

### Suggested Capstone Project
To validate your student's capability, assign a final **"Pipeline Optimization & FinOps Audit"** challenge:
1. Provide an intentional anti-pattern layout: an over-provisioned cluster running queries continuously without auto-termination thresholds.
2. Have students utilize the **Azure Pricing Calculator** to document baseline costs.
3. Challenge them to enforce partition pruning, switch interactive compute configurations to Job-scoped parameters, and build dynamic pipeline schedules.
4. Require them to report their resulting savings metric to validate enterprise readiness.


### Other Notes:
1. Create a architecture project using the cheapest resources. 
2. Create a git project repo structure. 
3. Include the best practices on the learning materials
4. Create a list of verified links from official documentation and medium blogs as references. 
5. Review the Remarks section and clean up. especially the notes tagged out of scope, prerequisite and required
6. For the hands on, get a geolocation dataset in kaggle (make sure that the link is verified.)
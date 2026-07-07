#review: DRAFT

# Azure Data Engineering Training
### 05 — Azure Data Engineering — Quiz

---

## Quiz

1. What feature converts standard Azure Blob Storage into ADLS Gen2 and enables true file system directory operations?

   A. Lifecycle management policies
   B. Hierarchical Namespace (HNS)
   C. Geo-redundant storage replication
   D. Soft delete for blob containers

2. An ADF pipeline needs to run every hour on a strict non-overlapping schedule with built-in retry for missed intervals. Which trigger type should be used?

   A. Schedule trigger
   B. Event trigger
   C. Tumbling Window trigger
   D. Webhook trigger

3. Which Delta Lake maintenance command reorganizes small fragmented files into larger optimized blocks to improve query performance?

   A. VACUUM
   B. ZORDER BY
   C. MERGE
   D. OPTIMIZE

4. How does Synapse Serverless SQL pool charge users for query execution?

   A. A flat hourly rate based on provisioned DWUs
   B. Per Gigabyte of data scanned during query execution
   C. Per number of concurrent users connected to the endpoint
   D. A fixed monthly cost per storage account registered

5. What Azure AD feature allows services such as ADF and Databricks to authenticate to Key Vault without storing credentials directly in code?

   A. Service Principal
   B. Managed Identity
   C. Shared Access Signature
   D. Connection String

6. What is the primary advantage of decoupling storage from compute in Azure data architectures?

   A. Data can only be accessed through a single compute engine
   B. Storage and compute can be scaled independently, reducing costs during idle periods
   C. Compute resources automatically provision storage when needed
   D. Data is encrypted twice for enhanced security

7. In incremental data ingestion, what is the purpose of a watermark table?

   A. To compress data files before storage
   B. To track the last successfully processed timestamp or record identifier
   C. To watermark files with ownership metadata for governance
   D. To transform row-based data into columnar format

8. Which type of Databricks cluster is recommended for production ETL workloads to minimize cost?

   A. Interactive cluster with auto-termination
   B. High-Concurrency cluster
   C. Job cluster
   D. Single-Node cluster

9. Which table distribution strategy in Synapse Dedicated SQL is best suited for small dimension tables under 2 GB?

   A. Hash distribution
   B. Round-Robin distribution
   C. Replicated distribution
   D. Range distribution

10. What is the first action engineers should take when provisioning resources to enable cost attribution in Azure Cost Management?

   A. Set up budget alerts at 50% and 90% thresholds
   B. Assign metadata tags such as environment and project to each resource
   C. Enable diagnostic logging on all storage accounts
   D. Register all resources in Microsoft Purview

---

### ANSWER KEY

1. **B** — Hierarchical Namespace (HNS) converts flat blob storage into a true file system with directories, enabling efficient rename and partition operations.
2. **C** — Tumbling Window triggers run on non-overlapping, fixed-interval windows and automatically backfill missed windows on recovery.
3. **D** — OPTIMIZE compacts small Delta Lake files into larger blocks (128MB–512MB) to reduce metadata overhead and accelerate scans.
4. **B** — Synapse Serverless SQL charges per Terabyte of data scanned (~$5.00/TB), with no cost for idle time or provisioned infrastructure.
5. **B** — Managed Identity assigns an Azure AD identity to the Azure resource itself, allowing it to authenticate to Key Vault without hardcoded credentials.
6. **B** — Decoupled storage and compute means storage persists independently while compute is activated only when needed, eliminating idle compute costs.
7. **B** — A watermark table stores the last processed timestamp or ID, allowing pipelines to extract only new or changed records since the last run.
8. **C** — Job clusters are auto-provisioned by the scheduler, execute a single script, and terminate immediately, costing significantly less than interactive clusters.
9. **C** — Replicated distribution copies the full table onto every compute node, avoiding network shuffles for small dimension tables during joins.
10. **B** — Resource tagging (e.g., Environment, Project, Owner) is a prerequisite for cost analysis, budget alerts, and chargeback reporting in Cost Management.

---

*Version: v1.0 | Created: 2026-07-06 | Author: Technical Curriculum Designer*

Quiz complete. Review all questions against the learning path content and validate that each correct answer is accurately reflected in the material. Proceed to Checkpoint 3 for final review of the full learning path.

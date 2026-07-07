#review: DRAFT

# Azure Data Engineering Training
### 06 — Azure Data Engineering — Rubrics

---

## Rubrics

### Quiz Rubric — Azure Data Engineering Training

**Score: 90-100% (9-10 correct) | Label: Excellent**

**Indicates:** The learner demonstrates comprehensive understanding of Azure data engineering concepts across all six phases — storage, orchestration, processing, warehousing, security, and FinOps — and can apply this knowledge to real pipeline design decisions.

**Recommended Action:** Proceed to advanced topics such as streaming data pipelines, multi-region architectures, or production deployment optimizations.

**Topics to Revisit:** None — proceed.

---

**Score: 75-89% (8 correct) | Label: Satisfactory**

**Indicates:** The learner has a solid grasp of the core services and concepts but may have gaps in one or two areas, such as specific Delta Lake operations or Synapse distribution strategies.

**Recommended Action:** Review the incorrect answers against the answer key, revisit the corresponding day content, and proceed.

**Topics to Revisit:** Check the answer key — revisit the day associated with each incorrect answer (e.g., Day 8 for Delta Lake commands, Day 11 for distribution strategies).

---

**Score: 60-74% (6-7 correct) | Label: Developing**

**Indicates:** The learner understands high-level concepts but struggles with service-specific details, configuration choices, or cost model mechanics — suggesting the hands-on labs were not fully completed or absorbed.

**Recommended Action:** Revisit Days 2-3 (storage configuration), Day 6 (incremental ingestion), and Day 8 (Delta Lake operations) before reattempting the quiz.

**Topics to Revisit:** Day 2 — ADLS Gen2 configuration, Day 3 — Partitioning, Day 6 — Watermark tables, Day 8 — Delta Lake maintenance, Day 13 — Security features.

---

**Score: Below 60% (0-5 correct) | Label: Needs Review**

**Indicates:** The learner has not yet developed sufficient understanding of Azure data engineering fundamentals and would benefit from retaking the full learning path with focused attention on hands-on labs.

**Recommended Action:** Retake the full learning path starting from Day 1, ensuring all hands-on activities are completed before reattempting the quiz.

**Topics to Revisit:** Full learning path — all 15 days.

---

### Hands-On Rubric — Azure Data Engineering Training (Course-Level)

| Criteria | 4 — Advanced | 3 — Proficient | 2 — Developing | 1 — Beginning |
|----------|-------------|----------------|----------------|---------------|
| **Storage & Data Lake Design** (Days 2-3) | ADLS Gen2 is provisioned with HNS, lifecycle policies, and Hive-style partitioning; files are in Parquet/Delta with optimized sizes | ADLS Gen2 is provisioned with HNS and partitioning; lifecycle policies are configured | ADLS Gen2 exists but HNS or partitioning is missing; lifecycle not configured | Storage account is misconfigured or missing HNS |
| **Pipeline Orchestration & Ingestion** (Days 4-6) | ADF pipeline is parameterized, uses Key Vault for secrets, has tumbling window trigger, failure alerts, and watermark-based incremental load | ADF pipeline runs successfully with parameters, trigger, and basic monitoring | ADF pipeline exists but missing parameters, triggers, or monitoring | ADF pipeline fails to run or is not built |
| **Data Transformation with PySpark & Delta Lake** (Days 7-9) | PySpark notebook reads Bronze, writes Silver Delta with OPTIMIZE and ZORDER; Job cluster is configured; virtualization vs ingestion comparison documented | PySpark notebook transforms data and writes Delta table; Job cluster is configured | PySpark notebook runs but Delta operations (OPTIMIZE, ZORDER) are not applied | PySpark notebook fails or produces no Delta output |
| **Data Warehousing & Analytics** (Days 10-12) | Synapse Serverless queries Silver Delta, CETAS creates Gold; star schema design documented with appropriate distribution strategy | Serverless SQL queries return correct results; CETAS creates Gold Parquet | Serverless SQL queries run but do not use CETAS or external tables | Serverless SQL queries fail or no Synapse workspace is created |
| **Security Configuration** (Day 13) | Key Vault stores all secrets; Managed Identity is assigned to ADF; pipeline retrieves secrets at runtime with no hardcoded credentials | Key Vault is created and ADF pipeline retrieves storage key from it | Key Vault exists but pipeline still uses hardcoded or partially secured credentials | No Key Vault or security configuration is implemented |
| **Governance & FinOps** (Day 14) | All resources are tagged; budget alert is set at $50 with 50%/90%/100% thresholds; Purview scan shows lineage across ADF → Databricks → Synapse | Resources are tagged; budget alert is configured; Purview scan runs successfully | Tags are applied but budget alert or Purview scan is incomplete | No tags, budget alerts, or Purview configuration |
| **End-to-End Integration** (Day 15) | Full pipeline runs end-to-end from source to Power BI dashboard; FinOps cost report shows total execution cost under $0.10; architecture diagram is included | Full pipeline runs from source to Power BI dashboard; cost report is produced | Pipeline runs partially (some steps execute) but dashboard or cost report is incomplete | Pipeline does not complete or no dashboard is produced |

**Total possible score:** 28 points (7 criteria × 4 points)
**Passing score:** 21 points (75% of total)

#### Scoring Guide

| Total Score | Label | Recommended Action |
|-------------|-------|-------------------|
| 25-28 | Advanced | Proceed to advanced topics — streaming pipelines, multi-region architectures, or production deployment |
| 21-24 | Proficient | Learning path complete — proceed to the next learning path or capstone project |
| 15-20 | Developing | Review flagged criteria and redo the associated hands-on activities before proceeding |
| Below 15 | Beginning | Revisit the full learning path and reattempt all hands-on activities before advancing |

---

*Version: v1.0 | Created: 2026-07-06 | Author: Technical Curriculum Designer*

Rubric generation complete. Review all criteria and scoring ranges against the learning path content. Rubrics are ready to attach to the relevant module and quiz in the final learning path document.

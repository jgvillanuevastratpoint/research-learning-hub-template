# Decision Log

| # | Date | Decision | Rationale | Made By |
|---|------|----------|-----------|---------|
| 1 | 2026-06-01 | Overwritten index.md and decision-log.md with fresh templates | Required by Skill 1 decision gate | Owner |
| 2 | 2026-06-01 | Modules 18 (Cost Optimization) and 19 (CI/CD) removed from topic list | Owner requested trimming from 20 to 18 modules | Owner |
| 3 | 2026-06-01 | Visuals removed from Days 3, 12, 13, 15 | Owner confirmed no visuals needed for those days | Owner |
| 4 | 2026-06-01 | Visual types selected for each flagged day — Architecture Diagram (Day 1), Architecture/Flowchart (Day 2), Concept Map (Days 4 & 11), Flowchart (Day 8) | Match each topic's structure: branching logic → flowchart, storage architecture → architecture diagram, abstract relationships → concept map | AI (Instructional Designer) |
| 5 | 2026-06-01 | Quiz distribution: 10 questions across 10 distinct days covering Medallion, Crawlers, Iceberg, DynamicFrame, DQDL/Quarantine, Airflow, XComs, IAM, Athena Workgroups, Iceberg MERGE | Ensures no single day dominates and every major pipeline phase is assessed | AI (Instructional Designer) |
| 6 | 2026-06-01 | Course-level hands-on rubric (7 criteria) chosen over individual day rubrics | Covers all pipeline phases in a single evaluation framework at 75% passing threshold | AI (Instructional Designer) |
| 7 | 2026-06-01 | Project completed — all skills finalized | All 6 skills complete. Owner confirmed at Checkpoint 3. | Owner |
| 8 | 2026-06-01 | Visual aid markers removed from Skill 3 output; Skill 4 updated to auto-clean markers on approval | Owner confirmed final cleanup of all `✅ Yes` and `❌ No` visual aid markers after approval | Owner |
| 9 | 2026-06-01 | Skill 1 rerun — added Phase column to topic list and [GAP] tag system | Owner requested pipeline-phase tagging for scannability; 6 phases: Storage, Catalog & Security, ETL & Quality, Orchestration, Analytics & Governance, Capstone | Owner |
| 10 | 2026-06-03 | Skill 7 (HTML Generator) created — standalone HTML page rendered from markdown output | Owner needed a publishable HTML deliverable with styled metadata card, rendered Gantt PNG, and `<table>` TOC with Day/Topic/Domain/Method/Pace columns | AI |
| 11 | 2026-06-03 | Metadata block added to Skill 2 (learning path) and Skill 3 (module content) outputs as visible tables | Provides at-a-glance context: title, version, date, author, audience, prerequisites, tools, domains, duration, pace | AI (Instructional Designer) |
| 12 | 2026-06-03 | Gantt charts use 4 phase blocks (not 20 individual day bars) | Owner approved after confirming day-by-day bars were too dense; 4 blocks match pipeline phases: Storage & Catalog, ETL & Quality, Orchestration, Analytics & Capstone | Owner |
| 13 | 2026-06-03 | Gantt chart initially used real dates (`dateFormat YYYY-MM-DD`), then switched to day numbers (`dateFormat D`, `axisFormat Day %e`) | Owner feedback: day numbers (Day 1, Day 6, etc.) are more intuitive than calendar dates; phases start at days 1, 6, 11, 16 | Owner |
| 14 | 2026-06-03 | Mermaid diagrams rendered server-side to PNG via `mermaid.ink` API at build time | Avoids Mermaid.js CDN dependency in HTML; images are static assets that work offline once built; 5 content diagrams + 1 Gantt chart saved to `images/` | AI |
| 15 | 2026-06-03 | Tags stored as YAML front matter in markdown and as HTML comments (`<!-- tags: ... -->`) in HTML | Machine-parseable format for future backlink scraping without breaking visual rendering | AI |
| 16 | 2026-06-03 | Old `.index-section / .index-list` CSS removed from HTML | Replaced by new `.metadata-card`, `.gantt-block`, `.toc-wrapper / .toc-table` styles — old classes had no remaining elements | AI |

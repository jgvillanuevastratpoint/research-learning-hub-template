#review: DRAFT

# N8n for Developers — Foundation & Week 1
### 08 — N8n Training Week 1 — Capstone Project Brief

---

## 1. Project Title

**Retail Order Intake Pipeline — N8n for Developers Capstone Project**

---

## 2. Scenario & Business Problem

A mid-size e-commerce retailer operates a storefront and sells through two partner marketplaces. Orders arrive from these channels in inconsistent JSON shapes: the storefront sends a single order per payload, while partner channels batch multiple orders in an array, use different field names, and mix casing. The retailer's operations team currently copies order data between systems by hand, which has produced delayed fulfillment, missed fraud checks, and incorrect analytics reporting.

The organization needs an automated order intake layer. Each incoming order must be normalized into a single internal structure, validated for completeness, and routed to the correct downstream system based on its business state. Paid orders must reach the fulfillment service immediately. Orders flagged with a high fraud-risk score must be held for the fraud-review service before any fulfillment work begins. Canceled and refunded orders must be reported to the analytics service so dashboards stay accurate, and malformed or incomplete orders must be rejected entirely rather than silently dropped.

The project requires the learner to build a single self-hosted n8n workflow that acts as this intake layer. The workflow is triggered by a webhook, splits batched payloads into individual orders, normalizes every order with expressions, validates and routes each order with core logic nodes, and delivers the result to the appropriate mock endpoint. Success is measured by clean, consistent order records arriving at each downstream system with no manual intervention and every rejected order visible in the execution history with its reason.

The capstone synthesizes every hands-on concept from the learning path — self-hosted Docker deployment, webhook triggers, HTTP actions, the items data model, per-item execution, expressions, and the Edit Fields, IF, Switch, and Filter logic nodes. It deliberately extends beyond the Week 1 user-onboarding lab into a different business domain so the learner integrates the concepts rather than repeating an exercise.

---

## 3. Dataset Reference

The capstone uses a seed dataset of realistic retail orders modeled on the JSONPlaceholder mock API format already used throughout the training. The seed data is served through a local mock API so every downstream system behaves like a real REST endpoint that records what the workflow delivers.

| Field | Value |
|---|---|
| **Name** | Retail Order Feed (capstone seed dataset) |
| **Source** | [JSONPlaceholder](https://jsonplaceholder.typicode.com/) — public mock API (free for testing, no license restriction). Extended into a local seed file for this capstone. |
| **Description** | 50 order records and 20 customer records covering paid, canceled, refunded, and high-risk orders across domestic and international shipments. Order fields include `order_id`, `customer_id`, `items`, `total`, `currency`, `status`, `payment_method`, `risk_score`, `email`, and `created_at`. Customer fields include `id`, `name`, `email`, `tier`, and `country`. |
| **Preprocessing Required** | None at the source. All cleaning happens inside the n8n workflow: trimming and lowercasing email addresses, deriving display names, formatting monetary values, and rejecting records missing required fields. |
| **Storage Location** | Staged as `db.json` served by a local mock API (JSON Server) running in Docker on port 3001. Incoming webhook payloads are recorded in the n8n execution history. |
| **Simulation Approach** | Run [JSON Server](https://github.com/typicode/json-server) (stable **v0.17.4**; the v1.0.0-beta line is not used because `--routes` and `--watch` are unreliable there) in a Docker container serving `db.json`. This exposes `GET /customers/:id` for order enrichment and `POST /fulfillment`, `/fraud-review`, and `/analytics` endpoints that store whatever the workflow delivers, making verification trivial. Alternative hosted option: [MockAPI.io](https://mockapi.io) if local container setup is unavailable. |

### Recommended Simulation Setup

1. Create `db.json` in the project folder with `customers`, `orders`, `fulfillment`, `fraud-review`, and `analytics` resources. Seed it with the 50 orders / 20 customers described above; the `fulfillment`, `fraud-review`, and `analytics` arrays start empty so the workflow's deliveries are observable.
2. Add a `mock-api` service to `docker-compose.yml` based on `node:22-alpine`, with the project folder mounted and the command `npx json-server@0.17.4 --watch db.json --port 3001`.
3. Bring it up with `docker compose up -d`. The mock API is reachable at `http://localhost:3001`.
4. Verify with:
   ```bash
   curl http://localhost:3001/customers
   curl -X POST http://localhost:3001/analytics \
     -H "Content-Type: application/json" \
     -d '{"order_id":"1001","status":"canceled"}'
   curl http://localhost:3001/analytics
   ```
5. Keep the mock API bound to localhost only. JSON Server exposes no authentication, so the container must not be published to a public port.

---

## 4. Architecture / Tech Stack Requirements

The project must include the tools, platforms, and services covered in the learning path, each performing the specified role:

| Component | Role in the Project | Required Configuration |
|---|---|---|
| **n8n (self-hosted, Docker Compose)** | Executes the intake workflow; records every payload in the execution history | Image `n8nio/n8n`, port 5678, named volume for `/home/node/.n8n` |
| **Webhook node** | Listens for inbound order payloads from the storefront and partner channels | Trigger `POST` on path `capstone/orders`, `Respond to Webhook` off |
| **Item Lists (Split Out Items)** | Converts a batched `orders` array into individual n8n items | Operation `Split Out Items`, field `orders` |
| **Edit Fields (Set) node** | Normalizes every order — trims/lowercases email, derives `display_name` and `formatted_total`, uppercases status | Assignments built with `$json` and `$node` expressions |
| **Filter node** | Rejects orders missing required fields (e.g., no `order_id`, `customer_id`, or `email`) | Condition: all required fields present |
| **IF node** | Splits valid orders from the reject stream | Condition: `status` equals `paid` or `refunded`/`canceled` handling as designed |
| **Switch node** | Routes paid orders to fulfillment, high-risk orders to fraud review, others to analytics | Routes keyed on `risk_score` and `status` |
| **HTTP Request node** | Enriches orders with customer data and delivers them to downstream systems | `GET http://mock-api:3001/customers/:id`; `POST` to `/fulfillment`, `/fraud-review`, `/analytics` |
| **JSON Server (mock API)** | Simulates the storefront data source and all downstream systems | Stable v0.17.4, port 3001, serving `db.json` |
| **cURL or Postman** | Sends test payloads and inspects the mock API | None |
| **Execution History** | Verification and debugging surface for every run | Inspected in the n8n UI |

---

## 5. Pipeline / Workflow Stage Mapping

| Stage | Location | Contents | Format |
|---|---|---|---|
| **Raw** | Webhook trigger (`POST /capstone/orders`) | Incoming storefront and partner-channel order payloads; recorded in execution history | JSON |
| **Processed** | In-memory n8n items after Edit Fields | Normalized orders with clean email, derived fields, validated completeness | JSON (n8n items) |
| **Routed** | IF / Switch / Filter outputs | Orders classified as fulfillment, fraud-review, analytics, or rejected | n8n items |
| **Output** | JSON Server collections `/fulfillment`, `/fraud-review`, `/analytics` | Delivered order records per downstream system | JSON |

---

## 6. Expected Workflow

```
Storefront / partner webhook (POST /capstone/orders)
  → Item Lists (Split Out Items) → individual order items
  → Edit Fields (Set) → normalized order fields
  → Filter → rejects incomplete orders (logged in execution history)
  → IF (status check) → IF false path
      → Switch (risk_score / status routing)
          → Route A: HTTP Request POST /fulfillment
          → Route B: HTTP Request POST /fraud-review
          → Route C: HTTP Request POST /analytics
  → HTTP Request GET /customers/:id (enrichment, per-item)
  → Verification: GET /fulfillment, /fraud-review, /analytics in the mock API
```

---

## 7. Deliverables Checklist

The learner must submit the following:

| # | Deliverable | Format | Description |
|---|---|---|---|
| 1 | Docker Compose project | YAML + Dockerfile | Self-hosted n8n service (port 5678, named volume) and `mock-api` JSON Server service (port 3001), starting with `docker compose up -d` |
| 2 | Seed dataset | JSON | `db.json` with `customers`, `orders`, `fulfillment`, `fraud-review`, and `analytics` resources matching the recommended simulation setup |
| 3 | Capstone workflow | JSON | Exportable n8n workflow (`... .json`) implementing the full intake pipeline |
| 4 | Test payloads | JSON + shell | Payloads covering a paid order, a high-risk order, a canceled/refunded order, and an incomplete order, with matching curl commands |
| 5 | Runbook | Markdown | Documents the pipeline flow, routing rules, verification steps, and how to re-run the project |
| 6 | Verification results | Markdown + screenshots | Execution-history evidence and the resulting records in `/fulfillment`, `/fraud-review`, and `/analytics` |

---

## 8. Success Criteria

Each deliverable is evaluated against these criteria:

| Deliverable | Pass | Distinction |
|---|---|---|
| **Docker Compose project** | Both services start; n8n reachable on 5678 and mock API on 3001 | Named volumes used for n8n data; mock API bound to localhost only; credentials/ports documented |
| **Seed dataset** | `db.json` loads and all five resources respond on the mock API | Data reflects multiple statuses, risk scores, and customer tiers that exercise every routing branch |
| **Capstone workflow** | Paid orders reach `/fulfillment`; high-risk orders reach `/fraud-review`; canceled/refunded reach `/analytics`; incomplete orders are rejected | Enrichment from `/customers/:id` is visible on delivered records; every branch verified in execution history |
| **Test payloads** | All four scenarios produce the expected routing outcome | Test payloads include a batched payload exercising Split Out Items with mixed statuses |
| **Runbook** | Documents pipeline flow, routing rules, and verification commands | Runbook includes troubleshooting notes and explains the per-item execution behavior at each stage |
| **Verification results** | Evidence shows deliveries recorded in all three mock endpoints | Evidence includes a rejected order with its reason and a batched multi-order run |

---

## 9. Stretch Goals (Optional)

- **AI-assisted triage:** Add an AI Agent node that reads free-text order notes and classifies them (e.g., urgent delivery, billing dispute, gift) so the Switch node routes priority cases to a dedicated endpoint — applying the Day 0 AI Agent concept.
- **Reconciliation workflow:** Add a Schedule-triggered workflow that periodically compares delivered orders against the mock `orders` collection and posts a discrepancy report, introducing the Schedule trigger from Day 2.
- **Failure handling:** Add error handling so a failed downstream POST retries the delivery and notifies an operations webhook, mirroring the auto-fixing loop idea from Day 0.

---

**Constraints applied:** All deliverables reference the same seed dataset and tools used in the learning path. Security best practices are followed — no hardcoded credentials, the mock API stays local-only, and no public exposure of the webhook. The scenario is distinct from the individual session exercises. Written in 3rd person throughout.

---

*Version: v1.0 | Created: 2026-08-13 | Author: Jem Villanueva*

Capstone project brief is complete. The brief is ready for learner distribution. Optionally trigger Skill 7 to publish as HTML.

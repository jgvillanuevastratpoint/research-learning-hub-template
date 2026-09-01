#review: DRAFT

# N8n for Developers — Foundation & Week 1
### db.json — Mock API Dataset Reference

---

## Overview

`db.json` is the single source of simulated data for the hands-on project. It is a JSON file that the local mock API reads and writes: the `users` resource seeds the dataset, and the `crm` and `analytics` resources record whatever the n8n workflow delivers. Because the file persists to disk, deliveries can be verified simply by reading it (or by calling the mock API endpoints).

**No database is required.** The file itself acts as the data store. Both supported mock API implementations operate on it:

- **JSON Server (recommended, zero-code)** — `npx json-server@0.17.4 --watch db.json --port 3001` turns each top-level key into a REST resource automatically.
- **Custom Express server (optional)** — `server.js` reads and writes the same file with `fs`.

---

## File Location

```
n8n-hands-on/
└── db.json          # mounted into the mock-api container as /data/db.json
```

The file sits in the project root so `docker-compose.yml` can bind-mount it into the mock API container, and so `scripts/verify.sh` can inspect it locally.

---

## Top-Level Structure

`db.json` contains exactly three resources:

| Resource | Type | Purpose | Initial State |
|---|---|---|---|
| `users` | array of objects | Seed signup records that exercise normalization and routing | 4 records |
| `crm` | array of objects | Records delivered to `POST /crm` (`@corporation.com` signups) | empty |
| `analytics` | array of objects | Records delivered to `POST /analytics` (all other signups) | empty |

```json
{
  "users": [],
  "crm": [],
  "analytics": []
}
```

---

## Resource Schemas

### `users`

Seed signup records. Fields are deliberately inconsistent (mixed casing, stray whitespace) so the Day 3 expression drill and the Day 5 `clean_email` normalization have realistic input.

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | Unique record identifier (JSON Server requires string IDs in watch mode) |
| `name` | string | Yes | Full name of the signup |
| `email` | string | Yes | Email address — may contain mixed casing and leading/trailing whitespace |

### `crm` and `analytics`

Delivery logs. Every record appended by the workflow combines the payload the n8n workflow POSTed with metadata added by the mock API.

| Field | Type | Added By | Description |
|---|---|---|---|
| `id` | string | mock API | Auto-assigned unique identifier |
| `received_at` | string | Express server only | ISO-8601 timestamp of receipt (`YYYY-MM-DDTHH:mm:ss.sssZ`); JSON Server does not add this |
| `name` | string | workflow payload | Normalized signup name |
| `email` | string | workflow payload | Normalized email (after trim + lowercase) |
| `clean_email` | string | workflow payload | The `{{ $json.email.trim().toLowerCase() }}` output, when the Day 5 pipeline is used |

> Any additional fields the workflow sends (for example `role`, `plan`, or `company`) are stored as-is. The two resources are structurally identical; only the routing rule that populates them differs.

---

## Sample Seed

```json
{
  "users": [
    { "id": "1", "name": "Alice Johnson", "email": "  ALICE.JOHNSON@Corporation.com " },
    { "id": "2", "name": "Bob Martin", "email": " bob@example.com " },
    { "id": "3", "name": "Carol Reyes", "email": "carol@corporation.com" },
    { "id": "4", "name": "Danilo Cruz", "email": " danilo@outlook.com " }
  ],
  "crm": [],
  "analytics": []
}
```

| id | name | email | Normalized to | Route |
|---|---|---|---|---|
| 1 | Alice Johnson | `  ALICE.JOHNSON@Corporation.com ` | `alice.johnson@corporation.com` | `/crm` |
| 2 | Bob Martin | ` bob@example.com ` | `bob@example.com` | `/analytics` |
| 3 | Carol Reyes | `carol@corporation.com` | `carol@corporation.com` | `/crm` |
| 4 | Danilo Cruz | ` danilo@outlook.com ` | `danilo@outlook.com` | `/analytics` |

---

## How the Mock API Reads and Writes the File

| Operation | JSON Server | Express server |
|---|---|---|
| Read on GET | Reads the resource on every request | `readDb()` parses the file per request |
| Create on POST | Auto-assigns a string `id` | `deliver()` assigns `id` and appends `received_at` |
| Persist | Writes the change back to `db.json` automatically | `writeDb()` rewrites the file with 2-space indentation |
| Watch mode | `--watch` reloads the file when it changes on disk | n/a (always reads fresh) |

Because the file is the store, `docker compose up -d` after a previous run restores the `crm`/`analytics` records from the last session — reset the simulation by restoring the file to its initial state (empty `crm` and `analytics` arrays).

---

## Endpoint Mapping

| Endpoint | Method | Reads / Writes | Example |
|---|---|---|---|
| `/users` | GET | reads `users` | `curl http://localhost:3001/users` |
| `/crm` | POST | appends to `crm` | corporate signup delivery |
| `/crm` | GET | reads `crm` | `curl http://localhost:3001/crm` |
| `/analytics` | POST | appends to `analytics` | public signup delivery |
| `/analytics` | GET | reads `analytics` | `curl http://localhost:3001/analytics` |

---

## Expected State After a Run

Sending `payloads/users-corporate-public.json` through the complete Day 5 pipeline produces the following `crm` and `analytics` arrays (shown with the Express server's `received_at`):

```json
{
  "users": [
    { "id": "1", "name": "Alice Johnson", "email": "  ALICE.JOHNSON@Corporation.com " },
    { "id": "2", "name": "Bob Martin", "email": " bob@example.com " },
    { "id": "3", "name": "Carol Reyes", "email": "carol@corporation.com" },
    { "id": "4", "name": "Danilo Cruz", "email": " danilo@outlook.com " }
  ],
  "crm": [
    {
      "id": "1",
      "name": "Alice Johnson",
      "email": "alice.johnson@corporation.com",
      "clean_email": "alice.johnson@corporation.com",
      "received_at": "2026-08-14T12:00:00.000Z"
    }
  ],
  "analytics": [
    {
      "id": "1",
      "name": "Bob Martin",
      "email": "bob@example.com",
      "clean_email": "bob@example.com",
      "received_at": "2026-08-14T12:00:01.000Z"
    }
  ]
}
```

---

## Validation & Consistency Notes

- **IDs are strings.** JSON Server v0.17.4 in watch mode treats `id` as a string; the seed uses string IDs accordingly.
- **Keep the three top-level keys.** The mock API and the n8n workflow reference `users`, `crm`, and `analytics` by name; renaming a resource breaks the simulation.
- **Start with empty `crm`/`analytics`.** A fresh state makes deliveries observable; leftover records from a previous run are valid but must be expected when verifying.
- **Do not edit `crm`/`analytics` by hand while the mock API is running** — JSON Server's `--watch` may overwrite manual edits, and concurrent writes can be lost.
- **The mock API exposes no authentication** — the file and endpoints are for local sandbox use only and must not be published to a public port.

---

*Version: v1.0 | Created: 2026-08-14 | Author: Jem Villanueva*

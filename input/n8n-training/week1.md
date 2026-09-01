Pre-requisites/Foundation:
LLM: The core AI brain (OpenAI, Claude, etc.) that you plug into the system to handle all the thinking, processing, and understanding.
Agentic Automation: Swapping out hard-coded rules for an AI that can understand context and make its own choices when handling messy data.
Prompt-to-Workflow: Building automations using plain English. You type out what you want, and the AI automatically maps out and connects the boxes on the screen.
AI Agent Node: The master workspace block where you give the AI a goal, memory, and access to your apps, letting it figure out how to solve the task on its own.
Auto-Fixing Bugs: A system where a crash triggers a loop, the error logs go straight to an AI node, which diagnoses what broke, fixes the settings, and re-runs the workflow by itself.

Part 1: L&D Considerations for this Cohort
Before looking at the syllabus, here is what we must consider when designing the learning experience for junior developers:

The "Low-Code" Skepticism: Developers often resist low-code tools. The training must immediately demonstrate n8n's value proposition (speed of delivery, visual debugging) without making them feel like their coding skills are being sidelined.

The Mental Model Shift: We must explicitly address how n8n handles data. Explaining that nodes execute per item in a JSON array (rather than writing a traditional loop) is the single most critical "aha!" moment they need.

Environment Setup: They should train in a sandboxed, self-hosted Docker environment. This gives them a safe space to break things, triggers, and loops without affecting production.

Learning Modality: 20% Theory / 80% Hands-on. Every conceptual lesson must be followed by a practical lab where they build a functioning workflow.

Version Control Integration: Developers are used to Git. We need to teach them how to version-control visual workflows (via JSON exports or n8n's source control features) early on so they feel secure.

Sandbox set up using docker:

Possible course angles:
- automation architecture (foundation focus)
- n8n as devtool (Foundation focus)
- rapid prototyping and delivery 

N8N Best Practices for Production
Keep Secrets in Credentials: NEVER hardcode API keys in HTTP Request headers or Code nodes. Always use n8n's built-in Credential Vault. It encrypts secrets in the database.

Use Sub-Workflows (Execute Workflow node): Do not build a 50-node monolith. If a piece of logic (like authentication refresh or error logging) is used multiple times, break it into a separate workflow and call it using the "Execute Workflow" node (like calling a function in code).

Error Triggers: Every production n8n instance should have a dedicated Error Workflow. Use the "Error Trigger" node in a new workflow, and configure your other workflows to point to it in their settings. This ensures failed executions pipe alerts directly to Slack, PagerDuty, or Jira.

References
n8n Documentation - Core Concepts: https://docs.n8n.io/getting-started/core-concepts/

n8n Documentation - Item-based Execution: https://docs.n8n.io/data/data-structure/

Docker Official Documentation: https://docs.docker.com/compose/

n8n JavaScript Execution Context (Code Node): https://docs.n8n.io/code/code-node/

JSONPlaceholder (Mock API for Labs): https://jsonplaceholder.typicode.com/


Part 2: The 4-Week "n8n Developer Onboarding" Outline
Week 1: The Paradigm Shift (Core Foundations)
Goal: Map their existing API and JSON knowledge to n8n’s visual interface and understand how data flows between nodes.

Day 1: Welcome to n8n

Architecture overview (Node.js backend, execution engine).

Local setup via Docker.

UI Tour: Canvas, left panel, execution history.

Day 2: Triggers and Actions

Building the first workflow: Webhook (Trigger) → HTTP Request (Action).

Understanding the Output Data: JSON structure and the concept of "Items."

Day 3: Expressions and Dynamic Data

Using the Expression Editor.

Referencing data from previous nodes ($json, $node).

Day 4: Core Logic Nodes

Replacing if/else and switch statements: IF, Switch, and Filter nodes.

Replacing variables: The Set node.

Day 5: Week 1 Lab

Build: A workflow that listens for a webhook payload (e.g., a new user signup), checks if the email domain is corporate or public, and routes the data to different mock endpoints.


# Week 1: The Paradigm Shift (Core Foundations)
**Course:** n8n for Developers
**Goal:** Map your existing API, JSON, and JavaScript knowledge to n8n’s visual interface, and master how data flows between nodes.

---

## Day 1: Welcome to n8n

Welcome to n8n. As developers, you are used to writing custom Node.js/Express or Python/FastAPI microservices for integrations. n8n is not meant to replace your coding skills; it is a visual orchestration layer built on Node.js designed to remove boilerplate code.

### 1. Architecture Overview
*   **Node.js Backend:** n8n is built on Node.js. It executes JavaScript natively.
*   **Execution Engine:** When a workflow triggers, n8n spins up an execution process. Data flows sequentially from left to right. 
*   **The Paradigm Shift:** Instead of writing `app.post('/webhook', (req, res) => {...})`, you place a Webhook node on the canvas. n8n handles the server listener, payload parsing, and HTTP responses under the hood.

### 2. Local Setup via Docker
We develop in a sandboxed container to prevent cross-contamination. 

**Setup Instructions:**
1. Open your terminal and create a directory: `mkdir n8n-local && cd n8n-local`
2. Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n_dev_sandbox
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - NODE_ENV=production
      - WEBHOOK_URL=http://localhost:5678/
      - GENERIC_TIMEZONE=Asia/Manila
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

3. Run `docker-compose up -d`.
4. Open `http://localhost:5678` in your browser and complete the initial local admin setup.

### 3. UI Tour
*   **Canvas:** The visual IDE where you map out logic.
*   **Left Panel:** The node library. Think of these as pre-packaged NPM modules.
*   **Execution History:** On the left sidebar, this is your debugger. It records every payload entering and exiting every node.

---

## Day 2: Triggers and Actions

Workflows are divided into two primary concepts: events that start a process (Triggers) and events that do the work (Actions).

### 1. Triggers vs. Actions
*   **Triggers:** Passive listeners. (e.g., Webhook Node, Cron Job / Schedule Node).
*   **Actions:** Active operational steps. (e.g., HTTP Request, Database queries).

### 2. The Data Model: JSON and "Items"
This is the most critical concept to grasp. In traditional programming, you receive a JSON array and write a `for` or `.map()` loop to process it. **In n8n, you do not write loops for basic iteration.**

n8n passes data between nodes as an array of objects called **Items**. 

```json
[
  {
    "json": {
      "user_id": 1,
      "email": "alice@corporate.com"
    }
  },
  {
    "json": {
      "user_id": 2,
      "email": "bob@gmail.com"
    }
  }
]
```

**The Rule of n8n Execution:** If a node receives an array of 2 items, *that node will execute its core logic twice automatically* (once for each item). 

### 3. Build Your First Workflow
1. Add a **Webhook Node** (Trigger). Set method to `POST`, Path to `test-hook`.
2. Add an **HTTP Request Node** (Action). Connect it to the Webhook.
3. Configure the HTTP Request to `GET` `https://jsonplaceholder.typicode.com/users/1`.
4. Click "Listen for Test Event" on the webhook, and send a `POST` request to the provided Test URL using cURL or Postman.

---

## Day 3: Expressions and Dynamic Data

Hardcoding values is bad practice. We need to pass data dynamically from previous nodes into our current node. We do this using the Expression Editor.

### 1. The Expression Engine
n8n uses standard JavaScript syntax for dynamic expressions, wrapped in double curly braces `{{ }}`.

### 2. Contextual Scope Resolution
*   `{{ $json }}`: Accesses the data of the *current* item being processed by the node.
*   `{{ $json.email }}`: Extracts the email string from the current payload.
*   `{{ $node["Webhook"].json.body.user_id }}`: Reaches back in time to grab the exact output from a specific node named "Webhook".

### 3. Inline Data Transformation
Because it's just JavaScript, you can manipulate data directly in the input fields:
*   `{{ $json.first_name.trim().toUpperCase() }}`
*   `{{ Math.floor(Math.random() * 100) }}`

---

## Day 4: Core Logic Nodes

Visual programming requires mapping procedural code statements to functional nodes.

### 1. Replacing variable assignments: The Set Node (Edit Fields)
In JS, you might write `const fullName = data.firstName + " " + data.lastName;`.
In n8n, you use the **Edit Fields (Set)** node. You define a new field name (`fullName`), change the type to String, and use an expression `{{ $json.firstName }} {{$json.lastName }}` to assign the value.

### 2. Replacing `if/else`: The IF Node
The IF node evaluates a condition. It has two output paths: `true` and `false`. 
*   *Developer Note:* The IF node evaluates *per item*. If you send 5 items in, 3 might go down the true path, and 2 down the false path simultaneously.

### 3. Replacing `switch`: The Switch Node
For multiple distinct conditions (e.g., routing by `status === 'pending'`, `'active'`, `'deleted'`), use the Switch node to create up to 4 output branches.

### 4. Replacing `.filter()`: The Filter Node
If you want to drop records from the pipeline entirely (e.g., only continue if `age > 18`), the Filter node will evaluate the condition. Items that pass continue; items that fail are discarded from the flow.

---

## Day 5: Week 1 Lab (Hands-On Pipeline)

**Scenario:** We are building a User Onboarding & Security Routing Pipeline.
A webhook receives new user signups. We need to normalize their data, check their email domain, and route corporate users to a CRM and public users to an analytics endpoint.

### Execution Diagram

```mermaid
flowchart LR
    A[Webhook Node\nPOST /signup] --> B[Edit Fields Node\nNormalize Data]
    B --> C{Switch Node\nCheck Domain}
    C -->|@corporate.com| D[HTTP Request\nMock Enterprise CRM]
    C -->|@gmail.com| E[Filter Node\nCheck valid role]
    E --> F[HTTP Request\nMock B2C Analytics]
```

### Lab Instructions
1. **Setup Trigger:** Create a Webhook node (`POST`, path `signup`).
2. **Send Test Data:** Use cURL or Postman to send this JSON to the webhook test URL:
   ```json
   {
     "users": [
       {"id": 101, "email": " ALICE@corporate.com ", "role": "admin"},
       {"id": 102, "email": "bob@gmail.com", "role": "guest"}
     ]
   }
   ```
3. **Item Split (Optional but recommended):** Since the payload has an array inside a single JSON object (`users`), use the **Item Lists** node (operation: Split Out Items) targeting the field `users` to convert the 1 object into 2 separate n8n items.
4. **Normalize:** Add an **Edit Fields** node. Create a new string field called `clean_email`. Use the expression `{{ $json.email.trim().toLowerCase() }}`.
5. **Logic Routing:** Add a **Switch** node. Check if `clean_email` ends with `@corporate.com` (Route 0) or `@gmail.com` (Route 1).
6. **Action 1 (Corporate):** Connect Route 0 to an **HTTP Request** node targeting `https://jsonplaceholder.typicode.com/posts` (POST method) to simulate the CRM. Send the `$json.clean_email` as the body.
7. **Action 2 (Public):** Connect Route 1 to an **HTTP Request** node targeting `https://jsonplaceholder.typicode.com/users` (POST method).
8. **Verify:** Execute the workflow. Go to the Execution History panel. You should visually see Alice's data go down Route 0, and Bob's data go down Route 1.

---

### Resources & Further Reading
*   [n8n Core Concepts: Nodes & Connections](https://docs.n8n.io/getting-started/core-concepts/)
*   [Understanding Data Structure / Items](https://docs.n8n.io/data/data-structure/)
*   [Expressions in n8n](https://docs.n8n.io/code/expressions/)


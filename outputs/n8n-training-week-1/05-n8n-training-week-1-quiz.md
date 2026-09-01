#review: DRAFT

# N8n for Developers — Foundation & Week 1
### 05 — N8n Training Week 1 — Quiz Generator

---

## 10-Item Multiple Choice Quiz

---

1. When a node receives two items from a previous node, how many times does n8n execute the node's logic?

   A. Once — only the first item is processed
   B. Twice — once per item received
   C. Only when the items share the same values
   D. A single combined pass over all items

---

2. Which requirement keeps a self-hosted n8n instance's data intact across container restarts in Docker Compose?

   A. A named volume mapped to /home/node/.n8n
   B. A static IP address assigned to the container
   C. A separate database container on port 5432
   D. An environment variable enabling the execution engine

---

3. Which n8n node most directly replaces an if/else statement from procedural JavaScript?

   A. Edit Fields (Set)
   B. Filter
   C. Switch
   D. IF

---

4. In an n8n expression, what does the $node variable reference?

   A. The item currently being processed by the node
   B. The saved output of a specific named node
   C. The credentials of the connected workflow
   D. The full list of nodes in the node library

---

5. What is an AI Agent node in n8n?

   A. A workspace block given a goal, memory, and access to apps that works out how to solve a task itself
   B. A node that stores credentials for connecting to large language models
   C. A trigger that listens for messages sent to the agent
   D. A debugger that records AI-generated error logs

---

6. How do triggers and actions differ in an n8n workflow?

   A. Triggers actively perform work; actions passively listen for events
   B. Triggers passively listen for events; actions actively perform the work
   C. Triggers run on a schedule; actions run on demand
   D. Triggers send HTTP requests; actions receive them

---

7. Which n8n UI surface records every payload entering and exiting every node, acting as a debugger?

   A. The canvas
   B. The node library
   C. The execution history
   D. The expression editor

---

8. In the Week 1 lab, a webhook receives a single JSON body containing a users array. Which step is required before the Switch node can route each user independently?

   A. Splitting the payload into separate items
   B. Assigning each user a unique identifier
   C. Replacing the webhook with a Schedule trigger
   D. Combining all users into a single item

---

9. How does the Filter node treat items that fail its condition?

   A. It routes them down a false output
   B. It discards them entirely
   C. It sends them back to the previous node
   D. It stores them in the execution history

---

10. What is prompt-to-workflow in n8n?

    A. A workflow that sends prompts to an external LLM API
    B. A pattern where a plain-English description is mapped to connected nodes on the canvas
    C. A node that generates error logs for failed workflows
    D. A scheduling option that runs workflows at set intervals

---

## ANSWER KEY

1. **B** — Per-item execution means a node runs its logic once for every item it receives, so two items produce two executions without writing a loop.

2. **A** — A named volume mapped to /home/node/.n8n preserves the n8n instance's data across container restarts.

3. **D** — The IF node evaluates a condition per item and routes matching items to the true output and the rest to the false output, mirroring an if/else statement.

4. **B** — The $node scope reaches back to the saved output of a specific named node, while $json reads the item currently being processed.

5. **A** — An AI Agent node is a workspace block given a goal, memory, and access to apps that figures out how to solve a task itself.

6. **B** — Triggers such as Webhook or Schedule passively listen for events, while actions such as HTTP Request actively perform the work.

7. **C** — The execution history acts as the debugger, recording every payload entering and exiting every node.

8. **A** — An Item Lists node with the Split Out Items operation converts the single object into separate n8n items so the Switch node can route each user independently.

9. **B** — The Filter node drops items that fail the condition; only items that pass continue downstream.

10. **B** — Prompt-to-workflow is the pattern where a plain-English description is automatically mapped to connected nodes on the canvas.

---

Quiz complete. Review all questions against the learning path content and validate that each correct answer is accurately reflected in the material. Proceed to Checkpoint 3 for final review of the full learning path.

---

*Version: v1.0 | Created: 2026-08-13 | Author: Jem Villanueva*

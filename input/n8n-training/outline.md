
------------- end of week 1 --------------
Week 2: Developer Power Tools (Data Manipulation)
Goal: Empower them to use JavaScript inside n8n to handle complex data structures and transformations that are too tedious for basic nodes.

Day 1: The Code Node (The Dev's Best Friend)

Running Custom JavaScript in n8n.

Understanding the difference between "Run Once for All Items" and "Run Once for Each Item."

Day 2: Advanced Data Transformation

Working with the Item Lists node (flattening arrays, splitting into batches).

Merging branches (Wait, Merge, and Compare datasets).

Day 3: State Management & Pagination

Handling API pagination (looping in n8n).

Static Data: Storing state between executions (e.g., saving the last sync timestamp).

Day 4: Authentication & Credentials

Managing API keys and OAuth2 securely within n8n.

Sharing credentials across workflows.

Day 5: Week 2 Lab

Build: An ETL (Extract, Transform, Load) pipeline. Paginate through a public API, use the Code node to sanitize and reformat the JSON, and batch insert the records into a mock database or Google Sheet.

Week 3: Enterprise Architecture & Resilience
Goal: Move from "it works on my machine" to building robust, modular, and production-ready workflows.

Day 1: Modular Workflows

The "Execute Workflow" node.

Passing data between parent and sub-workflows.

Why and when to decouple workflows.

Day 2: Error Handling & Debugging

Node-level error routing (Continue On Fail).

The Error Trigger node: Building a global error-catching workflow.

Debugging executions and reading stack traces in n8n.

Day 3: Version Control & CI/CD

Exporting workflows as JSON.

Using Git with n8n workflows.

Promoting workflows from Dev → Staging → Prod.

Day 4: Performance & Resource Management

Understanding memory limits and execution pruning.

When to use sub-workflows to free up memory during large data runs.

Day 5: Week 3 Lab

Build: Refactor the Week 2 ETL pipeline into a parent/child architecture. Intentionally introduce an API failure and build an Error Workflow that sends a Slack/Teams alert with the execution URL and failure reason.

Week 4: Capstone & Custom Extensions
Goal: Prove competency through a real-world scenario and understand how to extend n8n when native nodes fall short.

Day 1 & 2: Extending n8n

Using the HTTP Request node for APIs without native integrations.

Brief overview of creating Custom Nodes (NPM packages).

Using Community Nodes.

Day 3 & 4: Capstone Project

Scenario: A realistic, multi-step process utilizing your company's actual tech stack (e.g., Syncing Jira tickets to Salesforce, or automating a developer onboarding flow in Google Workspace and Slack).

Requirements: Must include pagination, a custom Code node snippet, sub-workflows, and proper error handling.

Day 5: Demo Day & Retrospective

Developers demo their Capstone workflows.

Code (Node) review with senior peers.

Feedback session on the training itself.


This is the Definitive Guide to Self-Hosted n8n for Data Engineers.
This guide is structured to take you from "running a script" to building a production-grade Data Orchestration Platform using the free, source-available version of n8n.

Phase 1: The Infrastructure (The Hardware)
Data engineering requires stability. The default n8n setup is optimized for ease of use, not stability. We must change that.
1. The Architecture
You will implement the Queue Mode architecture. Even on a single server, this decouples the Web UI (Webhook listener) from the Workers (Job processors).
Database: PostgreSQL (Replaces SQLite to prevent locking).
Queue: Redis (Handles job distribution).
Engine: n8n Worker nodes.
2. The docker-compose.yml (Production Boilerplate)
Save this. This is your "Platform." It sets up n8n, Postgres, and Redis together.
code Yaml
downloadcontent_copy
expand_less
   version: '3.8'

volumes:
  db_storage:
  n8n_storage:
  redis_storage:

services:
  # 1. The Database (Backend)
  postgres:
    image: postgres:16
    restart: always
    environment:
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=change_me_securely
      - POSTGRES_DB=n8n_system
    volumes:
      - db_storage:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -h localhost -U n8n -d n8n_system"]
      interval: 5s
      timeout: 5s
      retries: 5

  # 2. The Message Queue
  redis:
    image: redis:6-alpine
    restart: always
    volumes:
      - redis_storage:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5

  # 3. The Main Node (Editor + Webhook Listener)
  n8n-editor:
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n_system
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=change_me_securely
      - N8N_ENCRYPTION_KEY=replace_this_with_random_string
      - EXECUTIONS_MODE=queue
      - QUEUE_BULL_REDIS_HOST=redis
      - N8N_HOST=your-domain.com
      - WEBHOOK_URL=https://your-domain.com/
    links:
      - postgres
      - redis
    volumes:
      - n8n_storage:/home/node/.n8n
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy

  # 4. The Worker (The Data Processor)
  n8n-worker:
    image: n8nio/n8n:latest
    restart: always
    command: worker
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n_system
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=change_me_securely
      - N8N_ENCRYPTION_KEY=replace_this_with_random_string
      - EXECUTIONS_MODE=queue
      - QUEUE_BULL_REDIS_HOST=redis
    links:
      - postgres
      - redis
    volumes:
      - n8n_storage:/home/node/.n8n
    depends_on:
      n8n-editor:
        condition: service_started
 

Phase 2: Configuration for Data Engineers (The Tuning)
Data pipelines are different from marketing automations. You process heavy arrays of objects. You need to tune the environment variables in the Docker file above.
Variable
Recommended Value
Why?
EXECUTIONS_DATA_PRUNE
true
Critical. Without this, your DB will grow to 100GB+ and crash.
EXECUTIONS_DATA_MAX_AGE
168 (hours)
Keeps 7 days of logs. Balance debugging vs. storage.
DB_TABLE_PREFIX
n8n_
Keeps the n8n system tables separate from your own data tables if you share the DB (though sharing is not recommended).
N8N_DEFAULT_BINARY_DATA_MODE
filesystem
Performance. By default, n8n keeps binary files (PDFs, CSVs) in memory. This moves them to disk, preventing RAM crashes.


Phase 3: The Data Engineering Workflow (SDLC)
Do not just "drag and drop." Treat n8n workflows like software code.
1. Source Control (Git)
Since n8n v1.0+, Git Integration is available in the free version.
Setup: Go to Settings > Source Control.
Usage: Connect a GitHub/GitLab repository.
Benefit: Every time you save a workflow, you can push it to Git. This provides version history and rollback capabilities—essential for MVP and production data pipelines.
2. Separation of Concerns (Credentials)
Never hardcode API keys in the nodes.
Credentials Store: Use the native "Credentials" section.
Environment Variables: For global settings (like your Warehouse URL), use global variables.
How: Define them in n8n-editor environment vars, access them in workflows via {{ $env["MY_VAR_NAME"] }}.


3. Error Handling Framework
Create a standardized "Error Workflow" that acts as a catch-all.
Create a workflow named "Global Error Handler".
Trigger: Error Trigger node.
Action: Send alert to Slack/Microsoft Teams/Email.
Payload: Include {{ $execution.url }} so you can click immediately to debug.


Integration: In every Data Workflow, go to Settings -> Error Workflow and select this handler.

Phase 4: Core Data Engineering Patterns
This is the training curriculum. Master these three patterns to handle 90% of use cases.
Pattern A: The "Orchestrator" (ELT)
Best for: Big Data, Warehousing.
Philosophy: n8n is the manager, not the worker.
Trigger: Schedule (e.g., Daily).
Extract: HTTP Request to API
        →\to→
     
 Returns a URL to a CSV/JSON file (don't download the file itself if possible).
Load: Trigger a "COPY INTO" command in Snowflake/Postgres/BigQuery via SQL.
Transform: Trigger a dbt job or a stored procedure.
Why: n8n memory stays low; the database does the heavy lifting.
Pattern B: The "Batch Processor" (ETL)
Best for: API-to-API syncs, CRMs, Transactional Data.
Philosophy: Loop responsibly.
Extract: HTTP Request (Limit: 10,000 items).
Control Flow: Split In Batches node (Batch size: 50).
Transform: Code Node (Javascript).
Load: HTTP Request (POST to destination).
Loop: Connect back to "Split In Batches".
Why: Without batching, n8n will crash Node.js heap memory on large arrays.
Pattern C: The "State Manager" (CDC)
Best for: Syncing only new data.
Read State: Postgres Node -> SELECT last_updated FROM state_store WHERE job_name = 'orders_sync'.
Fetch Data: HTTP Request -> URL params: ?updated_after={{ $json["last_updated"] }}.
Process Data: (Normal ETL flow).
Update State: Postgres Node -> UPDATE state_store SET last_updated = NOW().

Phase 5: MVP to Production Checklist
When moving your project from "Idea" to "Live":
Lock the Version: Pin your Docker image (e.g., n8nio/n8n:1.24.0) instead of :latest to prevent an auto-update breaking your pipelines on restart.
Health Checks: Ensure you have an uptime monitor (like UptimeRobot) pinging your n8n webhook URL.
Backup: Script a nightly pg_dump of your n8n_system database. If the server dies, you restore this dump, and your workflows, credentials, and history are back.
Concurrency Limits: In your HTTP Request nodes, if you are hitting a rate-limited API, use the "Wait" node or the "Batch" node. Don't DDoS your own vendors.


You have built a robust execution engine, but to make this a complete Data Engineering Platform, you are missing four critical "Day 2" operations layers:
Data Quality & Schema Validation ("Garbage In, Garbage Out" protection).
Observability & Logging (Dashboards for your pipelines).
CI/CD & Environments (Moving from Dev to Prod safely).
Custom Python Libraries (Using Pandas/NumPy inside n8n).
Here is the final piece of the puzzle to complete your guide.

Phase 6: Data Quality (The "Circuit Breaker")
Data Engineers don't just move data; they ensure it's correct. If an API changes its format, your pipeline should fail gracefully rather than filling your database with corrupt rows.
The Schema Validator Pattern
You need to validate incoming JSON against a strict schema before processing.
The Tool: Use a Code Node with a validation library (like ajv for JS or manual checks in Python).
The Workflow:
Input: 100 rows of JSON.
Node: Code Node (Schema Check).
Logic:
 code JavaScript
downloadcontent_copy
expand_less
    // JavaScript Code Node
const requiredKeys = ['user_id', 'email', 'timestamp'];
const cleanData = [];
const badData = [];

for (const item of items) {
   const keys = Object.keys(item.json);
   const isValid = requiredKeys.every(k => keys.includes(k));
   
   if (isValid && item.json.email.includes('@')) {
       cleanData.push({json: item.json});
   } else {
       badData.push({json: item.json});
   }
}

return [cleanData, badData]; // Returns two outputs
 


The Routing: Connect Output 1 to your Database Loader. Connect Output 2 to a "Slack Alert" node to notify you of bad data.

Phase 7: Observability (Grafana & Prometheus)
The n8n "Executions" tab is fine for debugging one run, but it doesn't tell you: "What is the failure rate of the Marketing Pipeline over the last 30 days?"
The Monitoring Stack
Since you are using Docker Compose, add Prometheus and Grafana to monitor n8n health.
Expose Metrics:
n8n has a hidden endpoint /metrics if you enable it.
Add this environment variable to your n8n-editor and n8n-worker services:
N8N_METRICS_ENABLED=true


Scrape with Prometheus:
Configure Prometheus to scrape http://n8n-editor:5678/metrics.


Visualize in Grafana:
Import a dashboard to see:
Active Workflows count.
Failed Execution count.
Average Execution Time (to spot slow-down trends).
RAM/CPU usage of your Worker nodes.





Phase 8: CI/CD (Dev vs. Prod)
You cannot edit workflows on the production server. One wrong click breaks the daily reporting. You need a Deployment Pipeline.
The CLI Deployment Strategy
n8n has a CLI tool built into the Docker image. Use it to move work.
Environment Setup:
Dev Server: Localhost or a cheap VPS. You build and test here.
Prod Server: The protected server. Read-only access for humans.
The Workflow:
On Dev: You finish building a pipeline.
Commit: You save the workflow JSON to your Git repo (using the Git feature mentioned earlier).
Deploy (The Script):
On your Production server, run a script that pulls from Git and imports:
 code Bash
downloadcontent_copy
expand_less
    # deploy.sh
git pull origin main

# Import workflows from the file system into n8n database
docker exec -it n8n-editor n8n import:workflow --input=/data/workflows/

# Import credentials (if they changed)
docker exec -it n8n-editor n8n import:credentials --input=/data/credentials/
 

Phase 9: Advanced Python (Pandas & NumPy)
This is the biggest blocker for Data Engineers. The default n8n Docker image is based on Alpine Linux and does not have pandas, numpy, or scipy installed. The default "Code Node" runs in a sandbox that prevents importing external libraries.
You must build a custom Docker image.
The Dockerfile
Create a custom Dockerfile to extend n8n.
code Dockerfile
downloadcontent_copy
expand_less
   FROM n8nio/n8n:latest

USER root

# Install Python and Pip
RUN apk add --update --no-cache python3 py3-pip build-base python3-dev

# Create a virtual environment to avoid breaking system packages
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Install Data Engineering Heavyweights
RUN pip install pandas numpy requests sqlalchemy

# Switch back to the n8n user
USER node
 
Enable External Modules in n8n
Update your docker-compose.yml environment variables to allow n8n to "see" these libraries:
code Yaml
downloadcontent_copy
expand_less
   environment:
  - NODE_FUNCTION_ALLOW_EXTERNAL=moment,lodash # For JS
  - N8N_PYTHON_BINARY=/opt/venv/bin/python # Point to your venv
 
Usage in Workflow
Now, inside a Code Node (switch language to Python), you can actually do data science:
code Python
downloadcontent_copy
expand_less
   import pandas as pd

# 'items' is the global variable n8n passes in
df = pd.DataFrame([x['json'] for x in items])

# Do heavy transformations
df['total'] = df['price'] * df['quantity']
summary = df.groupby('category')['total'].sum().to_dict()

# Return to n8n format
return [{'json': summary}]
 


Summary
For a Data Engineer, n8n is not just an automation tool; it is a visual middleware. By using the Postgres backend and Queue Mode, you turn it into a robust infrastructure piece capable of handling mission-critical data flows for free.







This is the Advanced Integration Module for your Self-Hosted n8n Data Engineering Guide.
When you introduce tools like Airflow, Spark, and Flink, n8n shifts its role. It stops being the processor and becomes the Event Listener and API Controller.
In a Big Data stack, n8n is the nervous system, while Spark/Flink are the muscles.
Here is how to integrate them technically using the free self-hosted stack.

Part 1: n8n + Apache Airflow (The Event Bridge)
The Problem: Airflow is excellent at scheduled dependency management (DAGs), but terrible at real-time event handling. It usually polls for data, which is inefficient.
The Solution: Use n8n to catch a webhook/event immediately, then trigger the Airflow DAG via API.
Integration Method: Airflow REST API
Airflow has a robust Stable REST API. You will use the n8n HTTP Request Node.
1. Setup (Airflow Side)
Ensure your airflow.cfg allows API access:
code Ini
downloadcontent_copy
expand_less
   [api]
auth_backends = airflow.api.auth.backend.basic_auth
 
2. The n8n Workflow (Trigger DAG)
Node: HTTP Request
Method: POST
URL: http://<your-airflow-host>:8080/api/v1/dags/<dag_id>/dagRuns
Authentication: Basic Auth (Airflow Username/Password)
Body (JSON): Pass parameters from n8n to Airflow.
 code JSON
downloadcontent_copy
expand_less
    {
  "conf": {
    "source_file": "{{ $json.body.filename }}",
    "arrived_at": "{{ $now }}"
  }
}
 
MVP Project Idea: "Event-Driven ETL"
Event: A user uploads a CSV to an S3 bucket (or MinIO for self-hosted).
Trigger: n8n S3 Trigger node detects the new file.
Action: n8n calls the Airflow API to trigger the process_finance_data DAG, passing the specific S3 key as a variable.
Result: Airflow runs immediately only when data exists. No wasted polling.

Part 2: n8n + Apache Spark (The Compute Controller)
The Problem: Spark submissions are usually done via CLI (spark-submit). You want to automate this or trigger it from a business event (e.g., "Month End Close" button in a dashboard).
The Solution: Use n8n to SSH into the Spark Driver or use a Job Server (Livy).
Method A: The SSH Approach (Easiest for Self-Hosted)
This assumes you are running Spark on a VPS or on-prem server.
Node: SSH
Credentials: Private Key to your Spark Master/Gateway node.
Command:
 code Bash
downloadcontent_copy
expand_less
    /opt/spark/bin/spark-submit \
  --master spark://spark-master:7077 \
  --class com.company.MyBigDataJob \
  /path/to/jars/my-job.jar \
  "{{ $json.date_to_process }}"
 
Method B: Apache Livy (The REST Way)
If you want a cleaner integration, install Apache Livy in front of Spark. It turns Spark into a REST API.
Node: HTTP Request
Method: POST
URL: http://<livy-host>:8998/batches
Body:
 code JSON
downloadcontent_copy
expand_less
    {
  "file": "/path/to/jars/my-job.jar",
  "className": "com.company.MyBigDataJob",
  "args": ["{{ $json.date }}"]
}
 
MVP Project Idea: "On-Demand Data Marts"
Input: Marketing team fills a Typeform requesting "Last Year's Churn Analysis."
n8n: Receives the webhook.
Action: n8n uses SSH to trigger a PySpark job that crunches 50GB of logs.
Completion: The Spark job writes the result to a small Postgres table.
Notification: n8n watches that table (or receives a callback) and Slacks the Marketing Manager: "Your data is ready."

Part 3: n8n + Apache Flink (The Stream Manager)
The Problem: Flink jobs run forever (streaming). But sometimes you need to update reference data (side inputs) or stop/restart jobs for updates.
The Solution: Use n8n to manage the Lifecycle of Flink jobs via the Flink Dashboard API.
Integration Method: Flink Dashboard API
Flink exposes a monitoring and control API on port 8081 by default.
1. Uploading a Jar (Deployment)
Node: HTTP Request
Method: POST
URL: http://<flink-host>:8081/jars/upload
Body: Multipart-form-data (The .jar file).
2. Stopping a Job (Maintenance)
Node: HTTP Request
Method: PATCH
URL: http://<flink-host>:8081/jobs/<job-id>
Body: {"cancel-job": true}
MVP Project Idea: "Dynamic Fraud Rules"
You have a Flink job detecting credit card fraud. You want to add a new "blocked country" without redeploying the code.
Input: An admin adds "Country X" to a row in a Google Sheet/Airtable.
n8n: Cron trigger checks the sheet every 5 minutes.
Action: n8n formats this rule into JSON and POSTs it to a Kafka Topic.
Flink: The Flink job is listening to that Kafka Topic as a "Broadcast State" and updates its internal logic in real-time.
Role of n8n: It acts as the UI/Admin panel for the headless Flink stream.

Part 4: The "Big Data" Architecture Diagram
For your training materials or project documentation, here is how you visualize this stack.




Since we covered the "Big Data" giants (Airflow, Spark, Flink) in the previous section, let's look at Part 2 of the Advanced Integration Module.
This section focuses on the Modern Data Stack (MDS). These are the tools you are most likely to use in a startup or agile data team: dbt (Transformations), Kafka (Streaming), and Cloud Warehouses (Snowflake/BigQuery) optimization.

Integration 4: n8n + dbt (The Transformation Layer)
The Problem: n8n is great at moving data (EL), but terrible at complex SQL logic (T). You want dbt (data build tool) to handle the SQL transformations inside your data warehouse after n8n loads the raw data.
The Constraint: dbt Cloud is expensive. You want to run dbt Core (Free/CLI) triggered by n8n.
Method: The "Sidecar" Container Strategy
Do not install dbt inside the n8n container. It bloats the image. Instead, run dbt as a separate service in your Docker Compose.
1. The Architecture
n8n: Orchestrator.
dbt-runner: A tiny API server that has dbt installed.
2. The Setup
Add this to your docker-compose.yml. We use a lightweight image that wraps dbt in a simple HTTP server.
code Yaml
downloadcontent_copy
expand_less
   dbt-service:
    image: ghcr.io/dbt-labs/dbt-postgres:1.7.0 # Or dbt-snowflake, etc.
    command: >
      /bin/sh -c "
      pip install flask &&
      python3 -c '
      from flask import Flask, request
      import subprocess
      app = Flask(__name__)
      
      @app.route(\"/run\", methods=[\"POST\"])
      def run_dbt():
          # Runs dbt run in the project directory
          cmd = \"dbt run --profiles-dir .\"
          result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
          return {\"output\": result.stdout, \"error\": result.stderr}, 200
      
      app.run(host=\"0.0.0.0\", port=8080)
      '"
    volumes:
      - ./my_dbt_project:/usr/app # Mount your local dbt project here
    working_dir: /usr/app
    ports:
      - "8080:8080"
 
3. The n8n Workflow
Load Data: n8n loads raw JSON into Postgres/Snowflake.
Trigger Transform: HTTP Request Node
Method: POST
URL: http://dbt-service:8080/run


Wait: The node waits for dbt to finish.
Result: If output contains "Error", trigger your Error Workflow.

Integration 5: n8n + Kafka / Redpanda (The Streaming Layer)
The Problem: You have high-velocity data (clicks, IoT sensors). Sending them to n8n via Webhooks one by one will kill the server.
The Solution: Buffer data in Kafka (or Redpanda, which is Kafka-compatible, faster, and easier to self-host). n8n consumes batches.
Method: Batch Consumption
n8n has a native Kafka Trigger, but for high volume, you should use the Kafka Consumer approach to control the flow.
1. Configuration (Docker)
Ensure your n8n container can reach your Kafka broker.
Credentials: In n8n, set up "Kafka" credentials (SASL/SCRAM if using Cloud, or Plaintext if local).
2. The Workflow Pattern
Trigger: Kafka Trigger (This is the easiest way).
Topic: user_clicks
Group ID: n8n-consumer-group-1
Parallelism: If you have 3 n8n workers, all 3 will consume from the topic if the topic has partitions.


Optimization:
Enable "Simplify Output" (returns one array instead of deeply nested objects).
Batching: If the trigger fires too often, insert the data into a Redis List immediately, and have a separate Scheduled Workflow process that Redis List every minute. This prevents the "thundering herd" problem.



Integration 6: n8n + Snowflake / BigQuery (The "Big Load")
The Problem: Inserting 100,000 rows into Snowflake using the standard Insert node is incredibly slow because it runs INSERT INTO ... VALUES (...) for every single row.
The Solution: The Stage & Copy Pattern. This is how pros do it.
The Architecture
n8n: Converts JSON
        →\to→
     
 CSV.
Object Storage: (S3 / GCS / MinIO) holds the CSV.
Warehouse: Executes COPY INTO.
The n8n Workflow
Aggregate: Use Code Node to combine 10,000 items into one big binary object.
 code JavaScript
downloadcontent_copy
expand_less
    // Convert JSON to CSV String
const { json2csv } = require('json-2-csv');
const csv = await json2csv(items.map(i => i.json));

// Return as binary data for the next node
return [{
    binary: {
        data: Buffer.from(csv).toString('base64'),
        mimeType: 'text/csv',
        fileName: 'batch_upload.csv'
    }
}];
 
Upload: Use S3 Node (or Google Cloud Storage node).
Action: Upload.
Bucket: staging-bucket
Key: incoming/batch_{{ $now.format('x') }}.csv


Load: Use Snowflake Node (Execute Query).
SQL:
 code SQL
downloadcontent_copy
expand_less
    COPY INTO raw.events
FROM @my_s3_stage/incoming/
FILE_FORMAT = (TYPE = CSV, SKIP_HEADER = 1);
 


Cleanup: Use S3 Node to delete the file (or move to an archive/ folder).

Integration 7: n8n + Local LLMs (Ollama) for Data Enrichment
The Trend: Using AI to clean dirty data (e.g., "Standardize these job titles").
The Tool: Ollama (Free, open-source, local).
Setup
Run Ollama in Docker (or on the host machine).
Pull a fast model: ollama pull mistral.
The Workflow
Input: Rows of dirty data (e.g., "Soft. Eng.", "Senior Dev", "React Ninja").
Split In Batches: Batch size 10 (LLMs are slow).
HTTP Request:
URL: http://host.docker.internal:11434/api/generate
Body:
 code JSON
downloadcontent_copy
expand_less
    {
  "model": "mistral",
  "prompt": "Map these titles to standard HR roles (Software Engineer, Product Manager, Designer). Return ONLY JSON. Input: {{ JSON.stringify($json) }}",
  "stream": false
}
 


Code Node: Parse the JSON response from the LLM.
Merge: Combine the cleaned data with original IDs.

Summary of Integrations
Goal
Tool
Integration Method
Key Tip
Transform
dbt
Sidecar Container (HTTP)
Do not install dbt inside n8n.
Stream
Kafka
Native Trigger Node
Use Redis as a buffer if volume is >100 msg/sec.
Load (Batch)
Snowflake/BQ
Stage (S3) -> COPY INTO
Never use INSERT for >1k rows.
AI Clean
Ollama
HTTP Request (Local)
Use small batches and strict prompts.

This completes your toolkit. You now have the Infrastructure (Docker/Postgres/Queue), the Process (CI/CD, Quality), and the Integrations (Big Data & Modern Stack).
 




Summary for Data Engineer
1. The Core Philosophy
For Data Engineering, n8n is not a "no-code toy"; it is a Visual Orchestrator.
Role: n8n acts as the "Nervous System" (Logic, Triggering, API Management).
Anti-Pattern: Do not use n8n as the "Muscle" (Heavy Compute). Do not process 1GB files in memory.
Golden Rule: Move metadata through n8n. Keep data in the DB/Warehouse.

2. The Architecture (Infrastructure Stack)
To use n8n for data, you must abandon the default "hobby" setup.
Component
Choice
Why?
Deployment
Docker Compose
Required for custom networking and persistence.
Backend DB
PostgreSQL
Replaces SQLite to prevent database locking during parallel jobs.
Execution Mode
Queue Mode
Uses Redis to decouple the Web UI from Workers. Allows scaling.
Custom Image
Alpine + Python
You must build a custom Docker image to install pandas, numpy, and scipy.


3. The Three Data Patterns (Training Curriculum)
Module A: The "Orchestrator" (ELT)
Best for: Data Warehousing (Snowflake/BigQuery).
Flow: Extract API
        →\to→
     
 Save to S3/GCS
        →\to→
     
 Trigger SQL COPY INTO command
        →\to→
     
 Trigger dbt model.
Key Node: HTTP Request, S3, Postgres (Execute Query).
Module B: The "Batch Processor" (ETL)
Best for: API Syncs, CRM updates, transactional data.
Flow: Fetch Data
        →\to→
     
 Split in Batches (Loop)
        →\to→
     
 Transform (Code Node)
        →\to→
     
 Load
        →\to→
     
 Repeat.
Critical: Always use "Split in Batches" to manage RAM usage.
Module C: The "State Manager" (CDC)
Best for: Incremental Syncs.
Flow: Query DB for last_run_time
        →\to→
     
 Fetch API data > last_run_time
        →\to→
     
 Update DB with new time.
Key Node: Postgres (for state storage).

4. Advanced Integrations (The Big Data Stack)
Tool
Integration Strategy
Technical Implementation
Airflow
Event Bridge
n8n listens for Webhooks, then triggers Airflow DAGs via REST API.
Spark
Compute Controller
n8n uses SSH to run spark-submit or calls Livy API.
Flink
Lifecycle Manager
n8n manages Flink job deployments and updates reference data via Kafka.
dbt
Transformation
Run dbt in a "Sidecar" Docker container; n8n triggers it via HTTP.
Kafka
Buffer
Use n8n Kafka Trigger for events; output to Redis for high-volume buffering.
Ollama
AI Enrichment
Run local LLM; n8n sends dirty data in batches for cleaning/standardization.


5. Production Operations (Day 2)
Data Quality: Use a Code Node with a schema validator (JSON Schema/AJV) at the start of every pipeline. Route bad data to Slack, good data to DB.
Monitoring: Enable /metrics in n8n and scrape with Prometheus. Visualize uptime and failure rates in Grafana.
CI/CD:
Develop on n8n-dev.
Push workflow JSON to Git (native integration).
Deploy to n8n-prod using the CLI: n8n import:workflow.


Environment Variables: Never hardcode credentials. Use {{ $env["DB_PASSWORD"] }}.

6. The "Danger Zones" (What to Avoid)
Memory Leaks: If you don't use Split In Batches on datasets >5,000 rows, the Worker will crash.
Disk Bloat: You must set EXECUTIONS_DATA_PRUNE=true and EXECUTIONS_DATA_MAX_AGE=168 (1 week), or your server logs will fill the hard drive.
The "Loop of Death": Be careful with auto-retries on Webhook nodes; it can cause infinite loops.
Do not try to pull the result of a Spark job into n8n JSON memory unless it is a summary (e.g., "Total Revenue: $500"). If you try to pull the raw processed data into n8n, your instance will crash. Always leave the data in the DB/S3 and use n8n to signal completion.
Final Verdict for your Project
You have all the components to build a platform that rivals paid tools like Fivetran or Prefect, costing only the price of the VPS hosting it. Start with the Docker Compose setup, master the Postgres Backend, and then layer in Python and Airflow as you grow.








This is the Data Visualization & Reporting Module for your guide.
In a Data Engineering context, n8n is not a visualization tool (it has no charts). Instead, n8n acts as the "Delivery Service" for insights. It prepares data for BI tools or generates static reports for people who refuse to log into dashboards.
Here is how to integrate the best Free & Self-Hosted visualization tools.

Phase 1: The Open Source BI Stack (Metabase / Superset)
If you are self-hosting n8n, you should likely self-host your BI tool. Metabase (easiest) and Apache Superset (most powerful) are the standards.
Integration A: The "Report Bursting" Pattern
The Problem: Executives don't log into Metabase. They want the chart in Slack/Email every Monday.
The Solution: Use n8n to call the BI tool's API, render the chart to an image, and send it.
1. Setup (Metabase Example)
Tool: Metabase (Docker).
n8n Node: HTTP Request.
2. The Workflow
Trigger: Cron (Monday 9 AM).
Auth: HTTP Request
        →\to→
     
 Metabase /api/session (Get Token).
Export: HTTP Request
        →\to→
     
 /api/card/<card_id>/query/png.
Auth: Bearer Token from previous step.
Response Format: Binary.
Delivery: Slack Node or Email Node.
Attachment: The binary data from Step 3.


Integration B: Cache Warming
The Problem: Heavy SQL dashboards take 20 seconds to load. Users complain.
The Solution: Use n8n to force-refresh the dashboard cache before the users wake up.
Trigger: Cron (6 AM).
Action: HTTP Request
        →\to→
     
 Metabase/Superset API to trigger a refresh of specific datasets.

Phase 2: "Headless" Charting (QuickChart)
The Problem: You need to embed a trend line inside an HTML email, but you don't have a BI server, or you want it lightweight.
The Solution: QuickChart (Open Source). It turns JSON data into a PNG image via API.
1. The Infrastructure
Run the open-source version in your Docker Compose to keep data private (don't send data to the public API).
code Yaml
downloadcontent_copy
expand_less
   quickchart:
    image: ianw/quickchart
    restart: always
    ports:
      - "3400:3400"
 
2. The n8n Workflow
Get Data: Postgres Node (SELECT month, revenue FROM sales).
Format: Code Node (Transform data into Chart.js config).
 code JavaScript
downloadcontent_copy
expand_less
    const labels = items.map(i => i.json.month);
const data = items.map(i => i.json.revenue);

return {
  json: {
    chartConfig: {
      type: 'bar',
      data: {
        labels: labels,
        datasets: [{ label: 'Revenue', data: data }]
      }
    }
  }
}
 
Render: HTTP Request.
Method: POST http://quickchart:3400/chart.
Body: {{ $json.chartConfig }}.
Response: Binary (Image).


Deliver: Email Node (Embed the image).

Phase 3: Operational Dashboards (Grafana)
The Problem: Grafana is usually for server metrics, but Data Engineers use it to monitor Pipeline Health.
The Solution: Use n8n to push Annotations to Grafana.
The Use Case: "Pipeline Failure Markers"
When your ETL pipeline fails, you want a red vertical line on your CPU usage graph so you can see why the server spiked.
Error Handler: Inside your Global Error Workflow.
Node: HTTP Request.
URL: http://grafana:3000/api/annotations.
Body:
 code JSON
downloadcontent_copy
expand_less
    {
  "dashboardUID": "n8n_health",
  "time": {{ $now.minus('1 minute').toMillis() }},
  "text": "Pipeline 'Sales_Sync' Failed",
  "tags": ["error", "etl"]
}
 
Result: Your ops dashboard now correlates server load with specific n8n job failures.

Phase 4: The "Shadow BI" (Google Sheets / Excel)
The Reality: 80% of business "visualization" happens in spreadsheets.
The Role of n8n: Get the data there cleanly so users don't copy-paste.
Integration: The "Append & Formatting" Pattern
Clear Old Data:
Node: Google Sheets -> Operation: Clear.


Insert New Data:
Node: Google Sheets -> Operation: Append.


Crucial Step for Data Engineers:
Do not send "Raw JSON" dates (ISO 8601 strings) to Excel. Business users hate 2023-10-05T14:48:00.000Z.
Fix: Use an n8n Expression or Code Node to format dates before sending: {{ $now.format('yyyy-MM-dd') }}.



Phase 5: Automated HTML Reporting (The Daily Email)
The Problem: "Just send me a table in an email."
The Solution: Generate an HTML Table inside n8n.
The "JSON to HTML" Snippet
Save this in your training library. This Code Node converts any JSON array into a styled HTML table for emails.
code JavaScript
downloadcontent_copy
expand_less
   // Code Node (JavaScript)
const data = items.map(item => item.json);
if (data.length === 0) return [{json: {html: "<p>No data found</p>"}}];

const headers = Object.keys(data[0]);

// Create Table Header
let html = '<table style="border-collapse: collapse; width: 100%;">';
html += '<tr style="background-color: #f2f2f2;">';
headers.forEach(h => {
    html += `<th style="border: 1px solid #ddd; padding: 8px;">${h}</th>`;
});
html += '</tr>';

// Create Table Rows
data.forEach(row => {
    html += '<tr>';
    headers.forEach(h => {
        html += `<td style="border: 1px solid #ddd; padding: 8px;">${row[h]}</td>`;
    });
    html += '</tr>';
});
html += '</table>';

return [{json: {html_table: html}}];
 
Next Node: Email Node
       →\to→
     
Body: Here is the daily report: <br> {{ $json.html_table }}.

Summary: Which Tool When?
Scenario
Recommended Tool (Free)
n8n Integration Method
Deep Analysis
Metabase / Superset
n8n triggers cache refresh or exports PNGs via API.
Server/Pipeline Stats
Grafana
n8n sends "Annotations" (Errors/Deployments).
Email/Slack Charts
QuickChart (Self-Hosted)
n8n sends data, gets image, attaches to msg.
Tables in Email
HTML (Code Node)
n8n generates HTML string directly.
Business Users
Google Sheets
n8n clears and appends clean rows.

MVP Project Idea: "The Monday Morning Briefing"
Trigger: Monday 8:00 AM.
Data: SQL query for "Weekly Sales".
Visual 1: Send data to QuickChart to get a Bar Chart image.
Visual 2: Use Code Node to generate an HTML summary table of top customers.
Delivery: Send one email containing the Chart Image (top) and the HTML Table (bottom).











Here is a progressive list of MVP (Minimum Viable Product) Projects designed to take you from a "n8n Novice" to a "Principal Data Engineer."
These projects use the Self-Hosted Stack we built: n8n + Postgres + Redis + Docker.

Level 1: The "Personal Data Logger" (Beginner)
Goal: Understand JSON parsing, Cron triggers, and simple API interactions.
Scenario: You want to track the price of Bitcoin and the local weather every hour to see if there is a correlation (for fun).
The Stack: n8n, Google Sheets (or generic CSV), CoinGecko API (Free), OpenMeteo API (Free).
Key Skills: HTTP Request, Cron Trigger, Set Node, Google Sheets Node.
The Workflow:
Trigger: Schedule (Every 1 hour).
Step 1: HTTP Request
        →\to→
     
 CoinGecko (Get BTC Price).
Step 2: HTTP Request
        →\to→
     
 OpenMeteo (Get Temp/Rain).
Step 3: Set Node
        →\to→
     
 Combine inputs into one clean JSON object: { "time": "...", "btc": 45000, "temp": 24 }.
Step 4: Append to Google Sheet.


The "Aha!" Moment: Seeing data accumulate automatically without writing a script.

Level 2: The "Idempotent Syncer" (Intermediate)
Goal: Master State Management (CDC) and Database Upserts.
Scenario: You have a "Production" database (simulated) and an "Analytics" database. You need to sync new users every 10 minutes without creating duplicates.
The Stack: n8n, Postgres (Source Table), Postgres (Dest Table).
Key Skills: Postgres Node, SQL Logic, State Management pattern.
The Workflow:
State Check: Read last_sync_id from a metadata_table in the destination DB.
Extract: Query Source DB: SELECT * FROM users WHERE id > $last_sync_id.
Decision: If node. If 0 items returned, stop execution (save resources).
Load: Postgres Node
        →\to→
     
 Operation: Upsert (Conflict on ID).
Update State: Update metadata_table with the max ID from the batch.


The "Aha!" Moment: Running the workflow 5 times in a row and seeing the destination table remain perfect (no duplicates).

Level 3: The "Visual Reporter" (Intermediate)
Goal: Handle Binary Data, Charting, and HTML generation.
Scenario: Your boss wants a "Monday Morning Email" with a bar chart of last week's sales and a formatted HTML table of top customers.
The Stack: n8n, QuickChart (Self-Hosted), Email (SMTP).
Key Skills: Code Node (HTML generation), HTTP Request (Image retrieval), Merge Node.
The Workflow:
Fetch Data: SQL Query for sales stats.
Branch A (Chart): Transform data to Chart.js format
        →\to→
     
 Send to QuickChart
        →\to→
     
 Receive Binary Image.
Branch B (Table): Use Javascript Code Node to loop through data and build a string <table... >...</table>.
Merge: Combine Branch A and B.
Deliver: Email Node.
Body: Here is the chart: <img src="cid:chart"> <br> {{ $json.html_table }}.
Attachment: The Binary Image.




The "Aha!" Moment: Sending a professional-looking automated report using $0 tools.

Level 4: The "Poor Man's Data Lake" (Advanced)
Goal: Master the ELT pattern, Batching, and S3 interactions.
Scenario: You have a massive JSON API (100k rows). Processing it row-by-row crashes the server. You need to bulk load it into Postgres.
The Stack: n8n, MinIO (Self-hosted S3), Postgres.
Key Skills: Split In Batches, Looping, S3 Node, Postgres (Execute Query COPY).
The Workflow:
Init: Create a temporary CSV file on disk.
Fetch & Loop: Fetch API (page by page).
Accumulate: Append JSON data to the local CSV file using Code Node (Node.js fs module) or keep in memory if <100MB.
Upload: Stream the completed CSV to MinIO (S3).
Load: Trigger SQL in Postgres: COPY raw_data FROM 's3://bucket/file.csv'.
Transform: Trigger a SQL script to clean the raw data.


The "Aha!" Moment: Moving 100,000 rows in seconds using SQL COPY instead of hours using INSERT.

Level 5: The "AI Data Steward" (Expert)
Goal: Integrate Python (Pandas), Local LLMs, and Data Quality checks.
Scenario: You receive a messy CSV of job applicants. Titles are non-standard ("React Ninja", "Java Guru"). You need to standardize them to "Software Engineer" using AI and clean the data using Pandas.
The Stack: n8n (Custom Image with Python), Ollama (Mistral/Llama3), Postgres.
Key Skills: Custom Docker Image, Python Code Node, HTTP Request (Ollama).
The Workflow:
Read File: Read CSV.
Pandas Clean: Use Python Code Node to drop rows with missing emails and normalize capitalization.
Batch: Split into groups of 5.
AI Standardize: Send Titles to Ollama.
Prompt: "Map these titles to standard HR roles. Return JSON."


Parse & Merge: Merge AI results back to original rows.
Quality Gate: If AI confidence is low, route to Slack. If high, write to DB.


The "Aha!" Moment: Using a Local LLM inside a data pipeline to perform "fuzzy" transformations that SQL cannot do.

Level 6: The "Ops Platform" (Principal)
Goal: CI/CD, Error Monitoring, and Dynamic Workers.
Scenario: You are now the platform engineer. You need to ensure if a workflow fails, you know why, and you need to deploy changes safely.
The Stack: n8n, Git, Prometheus, Grafana, Slack.
Key Skills: Error Trigger, Prometheus Scraper, n8n CLI, Git.
The Workflow:
Observability: Set up Grafana to visualize n8n metrics (execution counts, CPU load).
Global Error Handler: Create a workflow that catches any error, formats the JSON error data, and pings a Slack Webhook with a "Retry" button URL.
Deployment Pipeline: Write a Bash script that pulls workflows from GitHub and imports them into n8n using the CLI (n8n import:workflow).


The "Aha!" Moment: Treating your drag-and-drop workflows as professional software code with version control and monitoring.


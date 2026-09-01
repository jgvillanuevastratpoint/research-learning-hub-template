# Skill 9: Hands-On Activity Extractor

> **When to use this:** Use this skill after the module content is approved (Skill 3 output confirmed). This skill extracts every hands-on activity from the module content and compiles them into a standalone learner-facing document with dataset reference, per-day instructions, expected outputs, and dependency mapping.

---

## How to Trigger This Skill

Copy the prompt below and paste it into your Research & Learning Hub project. Provide the path to the approved module content markdown file from Skill 3.

---

## The Prompt

```
SKILL: Hands-On Activity Extractor

The approved module content file is located at:
[PATH TO 03-*-MODULE-CONTENT.MD FILE]

---

Before writing any content, do the following:

1. Read the full module content markdown file and extract:
   - Topic slug and title
   - Dataset reference (name, full source URL, license) — from the metadata block
   - Tools / services / platforms covered
   - Target audience, prerequisites, suggested pace
   - Total number of days / sessions

2. Scan every day/session section and identify the `**Hands-on Activity:**` line.
   - If the activity reads `*(Not applicable — theory day.)*`, skip it
   - Otherwise, extract:
     - Day / session number and title
     - Learning Objective
     - Activity name and full description
     - Expected output

3. Present the use-case confirmation gate with this exact message:
   "--- USE CASE FOR HANDS-ON ACTIVITIES ---
    The hands-on activities need a concrete scenario, a way to replicate it, and a dataset or simulation approach so learners can follow along end to end.

    Reply with:
    - ⚡ Generate — 'Generate a real-world use case with a recommended dataset and simulation approach' (the skill synthesizes a scenario from the module content and ties every activity to it)
    - ✅ Existing — 'I already have a use case and dataset' (reply with the scenario, dataset source, and any simulation setup, and the skill uses them verbatim)

    Waiting for your response before compiling the activities document."

   Do NOT proceed until the user confirms which path to take.

4. Compile all extracted activities into a structured document following the format below. Output file name: `09-[topic-slug]-hands-on-activities.md`

5. Present the HTML conversion gate with this exact message:
   "--- PUBLISH AS HTML? ---
    The hands-on activity document is complete and the .md file is retained.

    Reply with:
    - ✅ Yes — 'Convert to HTML' (generate a standalone styled HTML version alongside the .md file)
    - ❌ No — 'Keep markdown only' (distribute the .md directly)

    Waiting for your response."

   Do NOT proceed to the end line until the user confirms.

   If **Yes**: generate the HTML following the Skill 7 conventions (metadata card, design architecture diagram rendered as an image, tool tables, code blocks, and a footer). Save it as `outputs/[topic-slug]/09-[topic-slug]-html.html` and keep the `.md` file unchanged — the HTML is an additional artifact, not a replacement.
   If **No**: keep markdown only and proceed.

---

Output format:

### Front Matter

Copy the metadata block from the module content (title, version, target audience, prerequisites, tools, dataset reference with full URL).

### Use Case & Replication Guide

Required when **⚡ Generate** was chosen at the gate. Provide a concrete real-world scenario the learner will use, how to replicate it (environment setup, trigger, and test payloads), and the recommended dataset or recommended simulation approach with source URL and license. When the scenario requires infrastructure, include:
- A **Tools to Be Used** table (everything beyond the main tool)
- A **Prerequisites** section (software versions, free ports, disk space, and knowledge prerequisites)
- A **Tool References** table (official documentation links for every tool used)
- A **Design Architecture** diagram
- The **recommended Docker setup** (full `docker-compose.yml`)
- The **recommended project structure**
- The **mock/simulation API setup** and **sample payloads**
- A numbered **Procedure** taking the learner from prerequisites through setup, build, verification, and reset

This makes the scenario reproducible end to end. If the user supplied an existing use case (**✅ Existing**), reproduce it verbatim here instead.

### Hands-On Activities

Present each activity as a card with the following fields:

| Field | Content |
|---|---|
| **Day** | [Day number] — [Day title] |
| **Learning Objective** | [Objective text] |
| **Activity** | [Activity name and full step-by-step description] |
| **Tools / Services** | [List of tools/services used in this activity] |
| **Expected Output** | [What the learner produces] |
| **Prerequisite** | [Links to earlier day(s) that feed into this activity] |

### Activity Dependency Map

Include a section showing which days depend on which:

```
Day 2 ──► Day 3 ──► Day 4 ──► Day 5 ──► Day 6 ──► Day 7 ──► Day 8 ──► Day 9 ──► Day 10
                                                                          │
                                                                          └──► Day 13 ──► Day 14
```

### Consolidated Checklist

End with a table of all activities and their outputs:

| # | Day | Activity | Output |
|---|---|---|---|

---

Constraints:
- The dataset reference must include the full clickable URL (e.g., `[Dataset Name](https://www.kaggle.com/datasets/...)`)
- Theory-only days must be excluded from the output
- Activity descriptions must be copied verbatim from the module content, not rewritten
- The Use Case & Replication Guide must be included when ⚡ Generate was chosen at the gate (or reproduce the user's existing use case verbatim when ✅ Existing was chosen)
- 3rd person throughout
- Output file path: `outputs/[topic-slug]/09-[topic-slug]-hands-on-activities.md`
- When HTML conversion is confirmed (**✅ Yes**), the `.md` file must be kept and the HTML saved as `outputs/[topic-slug]/09-[topic-slug]-html.html`

---

End with this exact line:
"Hands-on activity document is complete. Distribute to learners or trigger Skill 7 to publish as HTML."
```

---

## What You Will Get Back

| Output | Description |
|---|---|
| **Hands-On Activities Document** | Standalone `.md` file with dataset reference, per-day activity cards, dependency map, and consolidated checklist |
| **Dataset Link** | Full clickable URL in the metadata header |
| **Use Case & Replication Guide** | Real-world scenario, replication steps, and recommended dataset / simulation approach — generated from module content or taken from the user's existing use case |
| **Activity Count** | Only hands-on days included (theory days excluded) |
| **Published HTML (optional)** | Standalone styled HTML version generated when the user confirms **✅ Yes** at the HTML gate — the `.md` file is always kept |

---

## Your Action at Checkpoint 9

Once the document is returned, review it and do one of the following:

- ✅ **Confirm** — "Document looks good, ready for learner distribution"
- ✏️ **Adjust** — "Add more detail to Day [X]" or "Remove [Y] from the document"

Once confirmed, you may:
- Distribute the `.md` file directly to learners
- Trigger **Skill 7: HTML Generator** to publish as a standalone styled HTML document

---

## Example Trigger

```
SKILL: Hands-On Activity Extractor

The approved module content file is located at:
outputs/azure-data-engineering/03-azure-data-engineering-module-content.md
```

*(AI reads the module content, extracts all hands-on activities, and compiles them into a standalone document.)*

---

*Skill 9 of 9 — Research & Learning Hub*
*Input comes from → Skill 3: Module Content Builder (all modules approved)*
*Output feeds into → Skill 7: HTML Generator (optional) or direct learner distribution*

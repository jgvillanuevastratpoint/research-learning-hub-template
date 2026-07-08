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

3. Compile all extracted activities into a structured document following the format below. Output file name: `09-[topic-slug]-hands-on-activities.md`

---

Output format:

### Front Matter

Copy the metadata block from the module content (title, version, target audience, prerequisites, tools, dataset reference with full URL).

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
- 3rd person throughout
- Output file path: `outputs/[topic-slug]/09-[topic-slug]-hands-on-activities.md`

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
| **Activity Count** | Only hands-on days included (theory days excluded) |

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

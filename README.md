# Research & Learning Hub
### An AI-Powered System for Research Synthesis and Learning & Development

---

## What Is This?

The Research & Learning Hub is a structured AI workflow system designed to help researchers and Learning & Development practitioners produce consistent, high-quality learning materials efficiently.

Instead of starting from scratch every time, this system gives anyone on the team a repeatable, step-by-step process — from raw research sources all the way to a complete, assessment-ready learning path.

This system was built to be Agent agnostic.

---

## Who Is This For?

Anyone responsible for:
- Synthesizing research sources into structured knowledge
- Designing day-by-day learning paths for technical topics
- Building module content, quizzes, rubrics, and hands-on activities
- Producing learning materials for junior engineers or technical teams

---

## What's Inside This Folder

| File | What It Is |
|---|---|
| `README.md` | This file — start here |
| `project-instructions.md` | The AI instructions — paste this into your Claude Project settings |
| `skill/01-research-synthesizer.md` | Skill 1 — synthesize your sources into a concept map and topic list |
| `skill/02-learning-path-architect.md` | Skill 2 — build the day-by-day learning path skeleton with metadata block, Gantt chart, and table format |
| `skill/03-module-content-builder.md` | Skill 3 — write module content with tags front matter, metadata table, and Gantt chart |
| `skill/04-visual-generator.md` | Skill 4 — generate Mermaid diagrams for flagged modules |
| `skill/05-quiz-generator.md` | Skill 5 — generate the end-of-path quiz covering all days |
| `skill/06-rubric-generator.md` | Skill 6 — generate quiz rubric and/or hands-on activity rubric |
| `skill/07-html-generator.md` | Skill 7 — build a standalone HTML page from markdown output with metadata card, Gantt image, and table TOC |
| `skill/xx-end-workflow.md` | Skill xx — finalize project: update status tags, index, decision log, completion summary |

---

## How to Set This Up (First Time)

**Step 1 - Clone the project**
- Run inside IDE
- Activate agent of choice


**Step 2 — Add the Project Instructions**
- Open `project-instructions.md`
- Copy the entire contents
- Paste it into the **Project Instructions** field inside your Claude Project settings
- Save — this is now always active for every conversation in this project

**Step 3 — Familiarize Yourself with the Skills**
- Read through each skill file before using it for the first time
- Each file contains: when to use it, the prompt to copy, what you'll get back, and a sample output
- You do not need to memorize anything — just know which skill to reach for

**Step 4 — Run Your First Learning Path**
- Pick a topic you know well for your first run
- Follow the workflow below step by step
- This lets you spot anything to adjust before using it on real deliverables

---

## The Workflow (Every Time)

Follow this order for every new learning path:

```
STEP 1 → Skill 1: Research Synthesizer
         Bring your sources. Get a concept map + topic list.

              ↓ CHECKPOINT 1 — Review and confirm the topic list

STEP 2 → Skill 2: Learning Path Architect
         Confirm the structure, day breakdown, and visual flags.

              ↓ CHECKPOINT 2 — Review and confirm the skeleton

STEP 3 → Skill 3: Module Content Builder
         All module content is written at once.
         Hands-on activities are clarified before writing begins.

STEP 4 → Skill 4: Visual Generator (if needed)
         Run once per flagged module.
         AI recommends diagram type — you confirm before it generates.

STEP 5 → Skill 5: Quiz Generator
         Paste full learning path. Get 10-item quiz covering all days.

STEP 6 → Skill 6: Rubric Generator
         Generate quiz rubric, hands-on rubric, or both.

              ↓ CHECKPOINT 3 — Final review + validate all links

STEP 7 → Skill 7: HTML Generator
         Build a standalone HTML page with metadata card, Gantt chart image, and table of contents.

STEP 8 → Skill xx: End Workflow
         Automates status tag updates, index, decision log, completion summary.

DONE — Ready to publish or deliver
```

> ⚠️ **Important:** Always validate supplemental reading links manually before publishing. AI suggests links but cannot guarantee they are live or accurate.

---

## Output Structure & File Naming

Every skill produces its own standalone markdown file. Skills are never appended to a previous skill's file — each skill creates a new, independent file.

### Naming Convention
```
[sequence]-[topic-slug]-[descriptor].md
```

| Part | Example |
|---|---|---|
| sequence | `01`, `02`, `03`, `07`, `xx` |
| topic-slug | `airflow`, `data-pipelines`, `sql-fundamentals` |
| descriptor | `synthesis`, `learning-path`, `module-content`, `visuals`, `quiz`, `rubric`, `html`, `end-workflow` |

### Example Folder
```
/outputs
    └── glue-athena-airflow
        ├── 01-glue-athena-airflow-synthesis.md
        ├── 02-glue-athena-airflow-learning-path.md
        ├── 03-glue-athena-airflow-module-content.md
        ├── 04-glue-athena-airflow-visuals.md
        ├── 05-glue-athena-airflow-quiz.md
        ├── 06-glue-athena-airflow-rubric.md
        ├── 07-glue-athena-airflow-html.html
        └── xx-glue-athena-airflow-end-workflow.md
```

> The file name is confirmed at the start of Skill 2 once the topic and title are known.

---

## How to Use a Skill

1. Open the skill file for the current step
2. Copy the prompt inside **The Prompt** section
3. Paste it into a new conversation inside your Claude Project
4. Fill in the placeholder fields (marked with `[BRACKETS]`)
5. Send — follow the instructions in the skill file for what to do next

Each skill file tells you:
- When to use it
- What to provide as input
- What you will get back
- What to do at your checkpoint
- A full sample output so you know what to expect

---

## Key Rules to Always Follow

These apply to everything produced by this system:

- ✅ All content is written in **3rd person** — no you / I / me / we / your / our
- ✅ What / How / Why sections are **2-3 sentences maximum**
- ✅ Supplemental reading: **official documentation first**, then blogs or Medium articles
- ✅ **Validate all links** before publishing — this is always a manual step
- ✅ **Never edit the whole document** when only one module needs changing — edit that module only
- ✅ Each learning path includes **version number, date created, and creator name** in the metadata

---

## Rubric Scoring Reference

### Quiz Rubric

| Score | Label | Action |
|---|---|---|
| 90-100% | Excellent | Proceed to next topic |
| 75-89% | Satisfactory | Review flagged items and proceed |
| 60-74% | Developing | Revisit specific days before proceeding |
| Below 60% | Needs Review | Retake full learning path |

### Hands-on Rubric

| Score | Label | Action |
|---|---|---|
| 4 | Advanced | Exceeds expectations — proceed |
| 3 | Proficient | Meets expectations — proceed |
| 2 | Developing | Mostly correct — review and resubmit |
| 1 | Beginning | Significant gaps — revisit and redo |

Passing threshold for hands-on activities: **75% of total possible score**

---

## What's Not Included Yet (Future Skills)

| Skill | Status |
|---|---|
| Capstone Generator | Planned — will be a separate system with its own workflow |
| Career Coaching Skills | Planned — future phase |
| Manager Skills | Planned — future phase |

---

## Questions or Issues

If something in the system is not working as expected:
- Re-read the relevant skill file — the answer is usually in the constraints or example sections
- Check that the Project Instructions are correctly pasted into your Claude Project settings
- Make sure you are working inside the correct Claude Project when triggering skills

---

*Research & Learning Hub — Phase 1*
*Covers: Research Synthesis + Learning & Development*
*Built for use with Claude (claude.ai) Projects*
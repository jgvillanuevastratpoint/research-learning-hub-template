# Skill 8: Capstone Project Generator

> **When to use this:** Use this skill after all modules are approved (Checkpoint 3 confirmed) and the hands-on activities, tools, and dataset are finalized. This skill generates a standalone capstone project brief that learners complete as a summative assessment — integrating every service and concept covered in the learning path into a single end-to-end scenario.

---

## How to Trigger This Skill

Copy the prompt below and paste it into your Research & Learning Hub project. Provide the path to the approved module content markdown file from Skill 3.

---

## The Prompt

```
SKILL: Capstone Project Generator

The approved module content file is located at:
[PATH TO 03-*-MODULE-CONTENT.MD FILE]

---

Before writing any content, do the following:

1. Read the full module content markdown file and extract the following:
   - Topic slug and title
   - Dataset reference (name, source URL, license)
   - All tools, platforms, and services covered in the learning path
   - All hands-on activities — what each session's exercise builds
   - The existing final-day capstone section (if any) — its objective, core idea, and deliverables
   - The metadata block: target audience, prerequisites, duration, pace

2. From the extracted data, draft a **Capstone Scope Summary** with:
   - **Scenario:** A 2-3 sentence business problem the learner must solve
   - **Dataset:** Which dataset the capstone uses, where to obtain it, and any preprocessing needed
   - **Architecture / Tech Stack Requirements:** Which tools, platforms, and services must be used and what each must do
   - **Pipeline Stages:** How the data flows from raw source to final output (e.g., Bronze / Silver / Gold for data pipelines)
   - **Deliverables Overview:** A bullet list of what the learner must submit

3. Present the scope summary to the user with this exact message:
   "--- REVIEW CAPSTONE SCOPE ---
    Below is the proposed capstone scope based on the module content.
    Please review and confirm, or request changes before I write the full document.

    [SCOPE SUMMARY HERE]

    Reply with:
    - ✅ Confirm — "Looks good, proceed to write the full capstone brief"
    - ✏️ Adjust — "Change the scenario to [X]" or "Add [Y] to the requirements"
    - 🔁 Redo — "Regenerate the scope focusing on [different aspect]"

    Waiting for your response before writing the full capstone project brief."

   Do NOT proceed until the user confirms the scope.

---

Once the scope is confirmed, build the full capstone project brief using the following structure. Every section must be populated with content extracted from the module content file — never use hardcoded examples.

### 1. Project Title

`[Topic] — Capstone Project`

### 2. Scenario & Business Problem

[3-5 paragraphs. Describe a realistic business context. Include who the stakeholders are, what problem the project solves, why it matters to the organization, and what success looks like. Written in 3rd person. Align the scenario to the tools and platforms covered in the module.]

### 3. Dataset Reference

| Field | Value |
|---|---|
| **Name** | [Dataset name] |
| **Source** | Full Kaggle/other URL with license — e.g., `[Dataset Name](https://www.kaggle.com/datasets/...)` |
| **Description** | [What the data contains — rows, columns, time span] |
| **Preprocessing Required** | [Any cleaning, joining, or preparation steps before the pipeline] |
| **Storage Location** | [Where learners stage the raw data] |

### 4. Architecture / Tech Stack Requirements

The project must include the tools, platforms, and services covered in the learning path, each performing the specified role:

| Component | Role in the Project | Required Configuration |
|---|---|---|
| [Tool/Service 1] | [What it does] | [Key config detail] |
| [Tool/Service 2] | [What it does] | [Key config detail] |
| ... | ... | ... |

### 5. Pipeline / Workflow Stage Mapping

| Stage | Location | Contents | Format |
|---|---|---|---|
| **Raw** | [path/folder] | [description] | [format] |
| **Processed** | [path/folder] | [description] | [format] |
| **Output** | [path/folder] | [description] | [format] |

### 6. Expected Workflow

```
[Step 1 description] → [Step 2 description] → [Step 3 description] → ...
```

### 7. Deliverables Checklist

The learner must submit the following:

| # | Deliverable | Format | Description |
|---|---|---|---|
| 1 | [Deliverable name] | [format] | [description derived from module activities] |
| 2 | [Deliverable name] | [format] | [description] |
| ... | ... | ... | ... |

### 8. Success Criteria

Each deliverable is evaluated against these criteria:

| Deliverable | Pass | Distinction |
|---|---|---|
| [Deliverable 1] | [Pass criteria] | [Distinction criteria] |
| [Deliverable 2] | [Pass criteria] | [Distinction criteria] |
| ... | ... | ... |

### 9. Stretch Goals (Optional)

- [Enhancement derived from the module's advanced topics]
- [Additional integration or optimisation task]

---

Constraints:
- All deliverables must reference the same dataset and tools used in the learning path modules
- Security best practices must be followed (e.g., no hardcoded credentials)
- The scenario must be distinct from the individual session exercises — it is a synthesis, not a repeat
- 3rd person throughout — no you / I / we / your / our

---

End with this exact line:
"Capstone project brief is complete. The brief is ready for learner distribution. Optionally trigger Skill 7 to publish as HTML."
```

---

## What You Will Get Back

| Output | Description |
|---|---|
| **Capstone Scope Summary** | Draft business scenario, dataset, architecture requirements, and deliverables overview — presented for review before writing |
| **Project Brief** | Full capstone document with scenario, dataset reference, architecture / tech stack requirements, pipeline stage mapping, workflow diagram |
| **Deliverables Checklist** | Deliverables with format requirements and descriptions, derived from the module's hands-on activities |
| **Success Criteria** | Pass / Distinction criteria for each deliverable |
| **Stretch Goals** | Optional enhancements for advanced learners |

---

## Your Action at Checkpoint 8

Once the full capstone brief is returned, review it and do one of the following:

- ✅ **Confirm** — "Capstone brief looks good, ready for learner distribution"
- ✏️ **Adjust scope** — "Change the business scenario to [industry]" or "Add [component] to the requirements"
- ➕ **Add stretch goals** — "Add [specific enhancement]"
- 🔁 **Redo** — "Regenerate with a different dataset" or "Simplify the architecture requirements"

Once confirmed, you may:
- Distribute the `.md` file directly to learners
- Trigger **Skill 7: HTML Generator** to publish the capstone brief as a standalone styled HTML document

---

## Example Trigger (What It Looks Like in Practice)

```
SKILL: Capstone Project Generator

The approved module content file is located at:
outputs/[topic]/[module-content-filename].md
```

*(AI reads the module content, drafts a scope summary, and pauses for review before writing the full brief.)*

---

*Skill 8 of 8 — Research & Learning Hub*
*Input comes from → Skill 3: Module Content Builder (all modules approved)*
*Output feeds into → Skill 7: HTML Generator (optional) or direct learner distribution*

# Skill xx: End Workflow

> **When to use this:** Use this skill at the very end of a project, after all 6 skills are complete and the owner has confirmed Checkpoint 3 final review is done. This skill automates the final bookkeeping — updating status tags, recording wrap-up decisions, and generating a completion summary.

---

## How to Trigger This Skill

Copy the prompt below and paste it into your Research & Learning Hub project. Provide the project topic slug and any final notes.

---

## The Prompt

```
SKILL: End Workflow

Project topic: [TOPIC SLUG — e.g., glue-athena-airflow]
Owner final notes (optional): [Any wrap-up notes, post-mortem observations, or next steps]

---

Instructions:

1. READ the current `/index.md`, `/decision-log.md`, '/README.md', and `/project-instructions.md` for the project.

2. UPDATE all output files in `/outputs/[topic]/` from `#review: DRAFT` to `#review: APPROVED`.
   - Read each file, change the first line, and write it back.
   - If any file already says `#review: APPROVED`, leave it unchanged.
   - If any file says `#review: NEEDS FIX`, ask the owner to confirm it is resolved before changing to `#review: APPROVED`.

3. UPDATE `/README.md` .
   - Read /skill.
   - Update as necessary. Make sure to capture the updates in the instructions.

4. UPDATE `/index.md`:
   - Ensure the **Inputs** section lists all files found in the `input/` directory with file paths and topic descriptions. If the section is empty, populate it.
   - Change the Status column for every output file in this project from `#review: DRAFT` to `#review: APPROVED`.
   - Ensure the **Skills** table includes all 7 skills (1–6 + xx End Workflow) with correct file links.
   - If the project has a decision log section, make sure the link is present.

5. UPDATE `/project-instructions.md` (if needed):
   - Ensure the **Skills Available** table includes Skill xx (End Workflow) with its purpose, input, and output.
   - Ensure the **Descriptor Mapping** table includes Skill xx (`xx` → `end-workflow`).
   - Ensure the **Section Headers** table includes Skill xx (`## Completion Summary`).
   - Ensure the **Workflow** diagram includes Skill xx after Checkpoint 3.
   

6. APPEND a final entry to `/decision-log.md`:
   - Date: today's date
   - Decision: "Project completed — all skills finalized"
   - Rationale: "All 6 skills complete. Owner confirmed at Checkpoint 3."
   - Made By: "Owner"

7. GENERATE a completion summary with this format and append it below the existing content:

   ---

   ## Completion Summary — [Project Title]

   | Item | Status |
   |------|--------|
   | Skill 1 — Research Synthesizer | ✅ Complete |
   | Skill 2 — Learning Path Architect | ✅ Complete |
   | Skill 3 — Module Content Builder | ✅ Complete |
   | Skill 4 — Visual Generator | ✅ Complete |
   | Skill 5 — Quiz Generator | ✅ Complete |
   | Skill 6 — Rubric Generator | ✅ Complete |
   | Status tags updated to #review: APPROVED | ✅ Done |
   | Decision log finalized | ✅ Done |

   **Total skills executed:** 6
   **Total output files:** [count of files in outputs/[topic]/]
   **Completion date:** [today's date]

   ---

8. DISPLAY the completion summary to the owner in the chat.

---

Constraints:
- Do NOT change any `#review:` tag that already says `#review: APPROVED`
- Do NOT modify any content in the output files beyond the first-line status tag
- If an output file does not exist for any skill 1-6, note it in the summary as "❌ Missing" instead of "✅ Complete"
- All content written in 3rd person
```

---

## What You Will Get Back

| Output | Description |
|--------|-------------|
| **Updated output files** | All `#review: DRAFT` tags changed to `#review: APPROVED` |
| **Updated index.md** | Inputs, Skills, Outputs, and Decision Logs sections all populated and correct |
| **Updated project-instructions.md** | Skills table, descriptor mapping, section headers, and workflow diagram all reference Skill xx |
| **Updated decision-log.md** | Final completion entry appended |
| **Completion summary** | Table of all skills with status, total file count, and date |

---

## When to Run This

Run End Workflow only after:

1. ✅ Skill 1 through Skill 6 are all complete
2. ✅ The owner has reviewed all outputs at Checkpoint 3
3. ✅ The owner has validated all supplemental reading links
4. ✅ The owner explicitly says "finalize" or "run end workflow" — never run it automatically

---

## Example Output

```
Completion Summary — AWS Glue, Athena & Airflow Serverless Analytics Pipeline

| Item | Status |
|------|--------|
| Skill 1 — Research Synthesizer | ✅ Complete |
| Skill 2 — Learning Path Architect | ✅ Complete |
| Skill 3 — Module Content Builder | ✅ Complete |
| Skill 4 — Visual Generator | ✅ Complete |
| Skill 5 — Quiz Generator | ✅ Complete |
| Skill 6 — Rubric Generator | ✅ Complete |
| Status tags updated to #review: APPROVED | ✅ Done |
| Decision log finalized | ✅ Done |

Total skills executed: 6
Total output files: 6
Completion date: 2026-06-01
```

---

*Skill xx of 6+ — Research & Learning Hub*
*Input comes from → All 6 prior skills + index.md + decision-log.md*
*This is the final step — output is a fully finalized project ready for delivery*

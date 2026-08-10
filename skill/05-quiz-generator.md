# Skill 5: Quiz Generator

> **When to use this:** Use this skill after all module content is complete and visuals are embedded. This is always the last skill in the workflow before final review. The quiz covers the entire learning path — all days, all topics.

---

## How to Trigger This Skill

Copy the prompt below and paste it into your Research & Learning Hub project. Provide the full completed learning path content — all days — so the quiz draws from the entire scope.

---

## The Prompt

```
SKILL: Quiz Generator

The completed learning path content is as follows:
[PASTE FULL LEARNING PATH CONTENT — ALL DAYS — FROM SKILL 3 OUTPUT]

---

Instructions:

1. READ the full learning path content across all days.

2. GENERATE a 10-item multiple choice quiz that covers the entire learning path.

   Rules for question generation:
   - Draw questions from ALL days — no single day should dominate
   - Questions must be in RANDOM order — do not group by day or topic
   - Each question tests one clear concept, term, or principle from the content
   - Questions progress naturally in difficulty — mix of straightforward recall,
     applied understanding, and analytical thinking across the 10 items
   - No trick questions — questions must be fair and answerable from the content
   - Wrong answer choices (distractors) must be plausible but clearly incorrect
     to someone who studied the material

3. FORMAT each question as follows:

   [Question Number]. [Question text]

   A. [Option]
   B. [Option]
   C. [Option]
   D. [Option]

4. After all 10 questions, include an ANSWER KEY in a clearly marked section:

   ---
   ANSWER KEY
   ---
   1. [Correct letter] — [1-sentence explanation of why this is correct]
   2. [Correct letter] — [1-sentence explanation of why this is correct]
   ... (continue for all 10)

---

Constraints:
- All question text written in 3rd person — no you / I / me / we / your / our
- Options labeled A, B, C, D — no numbers or lowercase letters
- Questions drawn from ALL days — minimum 1 question per day for paths
  of 10 days or fewer
- No question may repeat the same concept as another question
- Distractors must be contextually relevant — no obviously absurd wrong answers
- Answer key must include a brief explanation for each correct answer,
  not just the letter
- Do not include hints, difficulty ratings, or day references in the
  question text — questions stand alone

---

End with this exact line:
"Quiz complete. Review all questions against the learning path content
and validate that each correct answer is accurately reflected in the
material. Proceed to Checkpoint 3 for final review of the full learning path."
```

---

## What You Will Get Back

| Output | Description |
|---|---|
| **10-item Multiple Choice Quiz** | Questions in random order, labeled A/B/C/D, drawn from all days |
| **Answer Key** | Correct letter + 1-sentence explanation for each of the 10 questions |

---

## Question Coverage Guide

Use this to quickly check that the quiz has good coverage after it is generated:

| Learning Path Length | Expected Coverage |
|---|---|
| 1-5 days | At least 2 questions per day, randomly ordered |
| 6-10 days | At least 1 question per day, randomly ordered |
| 10+ days | Best coverage across all major topics, some days may combine |

If a day is clearly missing from the quiz, request a targeted replacement:

```
Question [X] feels too similar to Question [Y]. Replace Question [X] with
a new question covering Day [Z] — [Topic Name]. Keep all other questions
exactly as they are.
```

---

## Your Action at Checkpoint 3 (Final Review)

This is the last checkpoint before the learning path is ready to publish or deliver.

Review the full learning path — modules, visuals, and quiz — and check:

- ✅ All module content reads correctly and follows the schema
- ✅ All supplemental reading links have been validated by the owner
- ✅ All visuals are embedded and captions are accurate
- ✅ Quiz questions are fair, clear, and answerable from the content
- ✅ Answer key is accurate for all 10 questions
- ✅ No second-person language anywhere in the document
- ✅ Metadata block is complete — title, version, date, created by

Once all checks pass — the learning path is done.

---

## Example Trigger (What It Looks Like in Practice)

```
SKILL: Quiz Generator

The completed learning path content is as follows:

METADATA
Title: Introduction to Apache Airflow
Version: v1.0
Date Created: 2026-05-30
Created By: [Name]
...

DAY 1: What is Apache Airflow and Why It Exists
[Full Day 1 content]

DAY 2: Core Concepts — DAGs, Operators, Tasks, and Connections
[Full Day 2 content]

DAY 3: Setting Up a Local Airflow Environment
[Full Day 3 content]

DAY 4: Building a First DAG
[Full Day 4 content]

DAY 5: Scheduling, Dependencies, and Monitoring
[Full Day 5 content]
```

---

## Example: Sample Output (What AI Returns)

```
1. Which of the following best describes a DAG in Apache Airflow?

   A. A database table that stores task execution logs
   B. A collection of tasks organized with directional dependencies and no loops
   C. A scheduler that triggers pipelines on a fixed interval
   D. A connection string used to authenticate external services

2. What is the primary role of an operator in an Airflow pipeline?

   A. To store credentials for external system connections
   B. To monitor the health of a running DAG
   C. To define what a task does when it is executed
   D. To pass data between tasks within the same DAG

3. Which Airflow component is responsible for securely storing credentials
   and endpoint information for external systems?

   A. XCom
   B. Operator
   C. Task Instance
   D. Connection

... (continues to question 10)

---
ANSWER KEY
---
1. B — A DAG is defined as a Directed Acyclic Graph, a collection of tasks
   with directional dependencies that contains no circular loops.
2. C — Operators define the action a task performs, such as running a Python
   function or executing a bash command.
3. D — Connections store host, port, login, and credential information used
   by tasks to interact with external systems securely.

... (continues to question 10)
```

---

Quiz complete. Review all questions against the learning path content
and validate that each correct answer is accurately reflected in the
material. Proceed to Checkpoint 3 for final review of the full learning path.

---

*Skill 5 of 5 — Research & Learning Hub*
*Input comes from → Skill 3: Module Content Builder (full completed learning path)*
*This is the final skill in the core workflow — output feeds into → Checkpoint 3 (Final Review)*
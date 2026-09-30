---
name: rimakes-tracker
description: >-
  Capture a task or plan as a detailed, self-contained Markdown tracker file —
  the plan AND its progress record in one document, with workstreams,
  checkboxed tasks, Done-means clauses, a decisions table, and a progress log.
  Use when the user asks to "create a tracker", "make a tracker file", "create
  a detailed md with a tracker", "track this in a file", "turn this plan into
  a tracker", or — after the agent has diagnosed a problem or proposed tasks —
  the user wants that work captured as a trackable plan document. Also use to
  UPDATE an existing tracker as tasks land (checking boxes, logging progress,
  recording decisions).
---

# Task Tracker

Turn the task at hand into ONE Markdown file that is both the plan and
the progress record. The file must be self-contained: a reader (or an
agent in a fresh session) with no other context can execute it, judge
what "done" means, and see what already happened.

## 1. Have a real plan first

The tracker captures a plan — it does not substitute for one.

- If the conversation already produced the plan (a diagnosis, a list of
  recommendations, an agreed approach), use THAT. Do not re-derive or
  water it down; the tracker should preserve every finding, file
  reference, and trade-off already established.
- If the task has NOT been investigated yet, plan it first using the
  agent's planning capability — whatever planning mode or workflow the
  current environment provides — and only then write the file. Never
  write a tracker of guesses.

## 2. Where the file goes

Match the project's existing convention before inventing one:

- Look for prior tracker/plan documents (`docs/plans/`, `docs/roadmap/`,
  `PLAN.md`, dated `*.md` plans). If they exist, mirror their location,
  naming, and voice — read one before writing.
- Default when the project has none: `docs/plans/YYYY-MM-DD-<slug>.md`
  (today's date, short kebab-case slug naming the task).

## 3. The document

Canonical skeleton — adapt sections to the task, keep the bones:

```markdown
# <Task name>: the tracker

Date: YYYY-MM-DD · Status: <NOT STARTED | IN PROGRESS | DONE> — one or
two sentences: what is decided, what has landed, what is still owed.

<The context: a detailed explanation of the problem or goal. State the
findings as facts with evidence — file paths and line numbers, measured
numbers, exact symptoms. Separate what is VERIFIED from what is
hypothesis. If some things are already right and must not be redone,
list them explicitly.>

This file tracks the work of closing the gap. It is the plan AND the
progress record: update it as tasks land.

---

## How to use this file

- Each task has an ID (`T1.2`), a checkbox, and a **Done means**
  clause. A task is checked only when its Done-means holds.
- Statuses at the workstream level: `NOT STARTED` → `IN PROGRESS` →
  `DONE` (or `PARKED` with a reason).
- Log every session of work in the **Progress log** at the bottom:
  date, tasks touched, anything learned that changes later tasks.
- New decisions (or reversals) go in the **Decisions** table, dated.
- Recommended order: <W0 → W1 → …, with the REASON for the order —
  dependencies, impact per effort, which result could invalidate
  later work>.

---

## Standing decisions the work follows

<Numbered decisions (D1, D2, …) that resolve tensions or set
constraints every task obeys. Each with its rationale, so it is not
relitigated silently.>

## W0 — <First workstream>

Status: NOT STARTED · <one line: what it delivers, what depends on it>

- [ ] **T0.1 — <Imperative task title>.** <Enough detail to execute
      cold: exact files, commands, the approach, known pitfalls.>
      _Done means:_ <an observable, checkable condition — never
      "improved" or "better".>
- [ ] **T0.2 — …**

## W1 — <Next workstream> …

## W<n> — Candidates, not commitments

<Ideas that surfaced but are not committed work. Promote to a
workstream only with a stated reason.>

---

## Decisions

| Date       | Decision                        |
| ---------- | ------------------------------- |
| YYYY-MM-DD | D1: <decision and one-line why> |

## Progress log

- **YYYY-MM-DD** — <What happened this session: tasks touched,
  findings, measurements, anything that changes later tasks.>
```

## 4. Quality bar

- **Very detailed beats brief.** Every claim traceable (file:line,
  dashboard, measurement). Every task executable without asking a
  question. Every Done-means observable.
- **Tasks are verbs with evidence**, not themes: "Change X in file Y so
  Z" — not "Improve performance".
- **Order with reasons.** Say which workstream comes first and why —
  especially when one task's result could invalidate others.
- **Record the negative space**: what is already right (do-not-redo),
  what was considered and parked, what stays a candidate.
- **Measurement tasks bracket change tasks** when the goal is
  quantitative: a baseline task before, a delta task after.
- Provide clickable URLs for every external resource a task touches
  (dashboards, docs, tools).

## 5. Afterwards

- Tell the user the file path.
- In LATER sessions, when work from a tracker lands: check the boxes,
  update workstream statuses, append to the progress log, and date new
  decisions — the file only earns its keep if it stays current.

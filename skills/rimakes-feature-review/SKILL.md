---
name: rimakes-feature-review
description: Review one whole feature folder with several reviewer subagents in parallel.
argument-hint: "<feature-folder> [--only a,b] [--skip a,b] [--model name]"
disable-model-invocation: true
---

# Feature Review

Review one feature as a whole, not a diff: map it → run the reviewers in
parallel → verify what they found → one ranked report.

This skill works in any repo. It holds no rule about any one codebase: every
rule a reviewer applies comes from the repo under review (its instruction
files, its docs, its sibling features) or from the docs of the libraries the
feature uses.

## The reviewers

Each reviewer has a brief in `reviewers/<name>.md`, next to this file.

| Reviewer              | Looks for                                                       | Skip when                                            | Model     |
| --------------------- | --------------------------------------------------------------- | ---------------------------------------------------- | --------- |
| `simplifier`          | Patches, workarounds, libraries not used the way they are meant | never                                                | `inherit` |
| `bug-hunter`          | Bugs in our logic, found by trying to break it                  | never                                                | `inherit` |
| `ux-checker`          | Flows that do not work when a person uses them in the browser   | the feature has no screen                            | `sonnet`  |
| `test-checker`        | Missing scenarios, tests of library code, weak test structure   | never (no tests at all is a finding)                 | `inherit` |
| `access-checker`      | Someone reading or changing what they should not                | no entry point takes input from outside              | `inherit` |
| `conventions-checker` | The feature breaking the repo's own written rules and patterns  | the repo has no written rules and no sibling feature | `sonnet`  |

`--only a,b` runs just those reviewers. `--skip a,b` leaves those out.

## Models

The **Model** column is what to pass as the `model` of each subagent.

- `inherit` means pass no model: the subagent runs on the model of the
  session. These are the jobs where a missed finding costs the most, so the
  user picks their strength by picking the session's model.
- `sonnet` is for jobs that are many steps of plain work: matching code to
  written rules, clicking through a browser.
- The mapper of step 2 runs on `sonnet`. The verifiers of step 4 run on
  `inherit`: they are the last check before the user reads a finding.
- A subagent type that sets its own model (such as `library-researcher`)
  keeps it.
- `--model <name>` runs every subagent on that model and overrides all of
  the above.

## Steps

### 1. Find the folder and check the model

The argument is the feature folder. If it is missing or does not exist, ask
the user for it. Do not guess.

Then look at the model this session runs on. If it is **Fable** and the
user gave no `--model`, stop before launching anything and ask (with
AskUserQuestion when available). Say that the `inherit` reviewers and the
verifiers would run on Fable, name them, and offer:

- Keep Fable for those.
- Run those on `opus`.
- Run every subagent on `sonnet`.
- Cancel.

Launch nothing until the user answers. Treat the answer like a `--model`
value for the subagents it covers.

### 2. Map the feature

A feature is rarely only its folder: its routes, screens, handlers, schema
and tests often live elsewhere. Launch one read-only subagent (`Explore` if
available) to write the **feature brief**, 60 lines at most:

- What the feature does, in two or three sentences.
- The files in the folder, grouped by role.
- Files outside the folder that belong to it: routes, screens, handlers,
  jobs, schema or migrations, translations, tests.
- Entry points: every page URL, API route, RPC, action, job, webhook or tool
  through which the feature is reached.
- Third-party libraries the feature calls, with the installed version of
  each (from the lockfile or the installed package).
- Where the repo's rules are written (instruction files, contributing guide,
  architecture docs, lint config) and the one or two sibling features most
  like this one.
- How to run the app, the tests, the linter and the type checker for this
  feature, and how to sign in locally (seed users, dev login) if the docs
  say.

Obey the repo's own instruction files while mapping, including what they say
not to read.

### 3. Run the reviewers in parallel

Decide which reviewers run (the table's "Skip when" column, then `--only` /
`--skip`). Launch all of them in **one message** so they run at the same
time, each on its model from the table. Use this prompt for each, filling
the blanks:

```
You are the <name> in a review of one feature.

Read these two files first and follow them:
- <skill dir>/reviewers/<name>.md   (your brief)
- <skill dir>/report-format.md      (how to report)

Feature folder: <path>

Feature brief:
<the brief from step 2>

Reviewers running at the same time as you: <name: topic, ...>.
Leave their topics to them.

Model for any subagent you launch: <sonnet, or the --model value>.

You do not change the repo: no edits, no commits, no deletes. Scratch files
go outside the repo.
```

Do not review the code yourself while they run, and do not paste your own
suspicions into their prompts: a reviewer handed a guess tends to confirm it.

If a report comes back with a **Needs follow-up** section (the reviewer could
not launch its own subagents), launch those subagents yourself, then send the
results back to that reviewer or fold them into its findings.

### 4. Verify the findings

Reviewers that read code report things that are not real. Before anything
reaches the user:

- **Observed findings pass as they are**: a flow the ux-checker saw fail in
  the browser with steps to repeat it, a test or lint run that failed.
- **Every other finding gets a verifier.** Launch one verifier subagent per
  reviewer, in parallel. Give it only each finding's title, location, "what
  is wrong" and "how it fails" — not the reviewer's reasoning and not the
  suggested fix. Its job is to prove each finding wrong: read the code, its
  callers and what it calls; run a test or a scratch script when that
  settles it. It answers per finding: `confirmed`, `not real` or
  `cannot tell`, with the evidence.

Drop `not real`. Keep `cannot tell` apart from the confirmed ones.

### 5. Report

Merge findings that share a root cause (keep every reviewer's name on the
merged one). Sort by severity. Write the report to a file, in this shape:

```
# Feature review: <folder>

Ran: <reviewer (model), ...>. Verifiers: <model>. Mapper: <model>.
Skipped: <reviewer — why>.

## Confirmed
### High
1. **<title>** — `path:line` (<reviewer>)
   What is wrong: ...
   How it fails: ...
   Suggested fix: ...
### Medium
### Low

## Could not confirm
<same shape, plus what would settle it>

## Not checked
<what no reviewer could check, and what each would need>
```

The models in the first line are the ones each subagent really ran on, by
name (`fable`, `opus`, `sonnet`, `haiku`), never the word `inherit`. If one
ran on a model the table did not ask for, say so right under that line.

Show the code that matters as short snippets inside the report, so the user
does not have to open other files.

**Where the file goes.** Put it where this repo keeps its written documents
(plans, reviews, decisions). Do not assume a folder name: find it from the
repo's instruction files and from where documents of this kind already
live. Name the file the way the documents next to it are named; if there is
no pattern, use `feature-review-<feature>-<YYYY-MM-DD>.md`. If the repo has
no such place, ask the user where to put it. Do not commit the file.

**Open it for review.** If the session has a tool or a skill that lets the
user review or annotate a file, open the report with it and wait for what
the user sends back. If there is none, give a clickable link to the file.

**In the chat**, keep it short: the link to the file, the number of
findings per severity, and the titles of the High ones.

Then ask which findings to fix. Comments the user makes on the report in
the review tool are that answer. Fix nothing before it.

## Guardrails

- Report only. The one file a review writes in the repo is the report.
  Nothing else changes.
- The browser and any script run against a local or development
  environment, never production.
- No finding without a location and a concrete way it fails.
- A skipped or blocked reviewer is named in the report with the reason;
  never pass silence off as "no findings".
- Keep this skill free of rules about any one repo. A rule that belongs to a
  codebase goes in that codebase's instruction files, where the
  conventions-checker will find it.

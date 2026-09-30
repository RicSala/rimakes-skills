---
name: rimakes-adversarial
description: Adversarial review of a change. Several subagents each try to break it from a different angle (logic, hostile input, access, timing, failure, rollout), and each writes its findings to its own file in one folder in the repo.
argument-hint: "[branch | pr <n> | <commit> | <a>..<b> | <path>...] [--only a,b] [--skip a,b] [--focus \"<text>\"] [--no-verify] [--model name]"
disable-model-invocation: true
---

# Adversarial Review

Try to break a change before it ships. Each reviewer is a subagent that
assumes the change is wrong and attacks it from one angle. Each one writes
what it found to its own file. Then verifiers try to prove each finding
wrong, so only real ones stay.

This skill works in any repo. It holds no rule about any one codebase: every
rule a reviewer applies comes from the repo under review (its instruction
files, its docs, its code) or from the docs of the libraries it uses.

## What to review

| Argument                 | Reviews                                                                           |
| ------------------------ | --------------------------------------------------------------------------------- |
| (none)                   | The current diff: staged, unstaged and untracked files                            |
| `branch`                 | Everything on this branch that is not on the default branch, plus the current diff |
| `pr <n>`                 | GitHub pull request `<n>` as it is on GitHub. Nothing is checked out.             |
| `<commit>` or `<a>..<b>` | One commit, or a range of commits                                                 |
| `<path>...`              | Those files or folders as they are now, whole, not a diff                         |

If there is nothing to review (an empty diff, a path that does not exist),
stop and say so. With no argument and an empty diff, suggest `branch`.

## Options

- `--only a,b` runs just those angles. `--skip a,b` leaves those out.
- `--focus "<text>"`: what the user worries about most. Every reviewer goes
  deepest there and still covers the rest of its angle.
- `--no-verify` skips step 5. Faster, but more false alarms reach the user.
- `--model <name>` runs every subagent on that model.

## The angles

Each angle has a brief in `angles/<name>.md`, next to this file. Every
reviewer also follows `reviewer-rules.md`.

| Angle           | Tries to break the change with                              | Skip when                                                                        |
| --------------- | ----------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `logic`         | Valid input that gives a wrong result                       | never                                                                            |
| `hostile-input` | Bad data sent on purpose                                    | nothing in the change takes data from outside                                    |
| `access`        | A caller reading or changing what is not theirs             | the change touches no entry point and no data access                             |
| `timing`        | The same thing twice, at once, late or out of order         | the change writes nothing and has no side effects                                |
| `failure`       | A database, service or network call that fails or is slow   | the change calls nothing outside its own process                                 |
| `rollout`       | Existing data, old code and old clients meeting the new code | the change touches no schema, stored data, public API, event, config or env var |

## Models

- Reviewers and verifiers run on the session's model: pass no `model`. A
  missed or wrong finding costs the most here, so the user picks their
  strength by picking the session's model.
- The mapper of step 2 runs on `sonnet`.
- A subagent type that sets its own model keeps it.
- `--model <name>` runs every subagent on that model and overrides all of
  the above.

## Steps

### 1. Read the arguments and check the model

Work out the target from the table above. If an argument could mean two
things (a folder with the same name as a branch), ask. Do not guess.

Then look at the model this session runs on. If it is **Fable** and the user
gave no `--model`, stop before launching anything and ask (with
AskUserQuestion when available). Say that the reviewers and the verifiers
would run on Fable, and offer:

- Keep Fable for those.
- Run those on `opus`.
- Run every subagent on `sonnet`.
- Cancel.

Launch nothing until the user answers. Treat the answer like a `--model`
value for the subagents it covers.

### 2. Map the change

Launch one read-only subagent (`Explore` if available) to write the
**change brief**, 60 lines at most:

- How to see the change: the exact commands a reviewer runs. For a diff,
  the `git diff` command and the list of untracked files. For `branch`, the
  default branch and the merge base. For a PR, `gh pr diff <n>`, and how to
  read a whole file at the PR's head without checking it out
  (`git fetch origin pull/<n>/head`, then `git show <head-sha>:<path>`).
- What the change does and why, in two or three sentences, from the commit
  messages, the branch name or the PR description.
- The changed files, grouped by role.
- Entry points the change adds or touches: pages, API routes, RPCs,
  actions, jobs, webhooks, tools.
- Side effects: database writes, calls to other services, files, emails,
  events or messages sent.
- Schema, migration, config and env var changes.
- Third-party libraries the change calls, with the installed version of
  each (from the lockfile or the installed package).
- Where the repo's rules are written (instruction files, architecture docs,
  lint config), and how the repo guards access (middleware, checks in each
  handler, tenant scoping).
- How to run the tests, the linter and the type checker for the changed
  code, and which tests need a shared database or service.

Obey the repo's own instruction files while mapping, including what they
say not to read.

### 3. Make the output folder

Put it where this repo keeps its written documents (plans, reviews,
decisions). Do not assume a folder name: find it from the repo's
instruction files and from where documents of this kind already live. If
the repo has no such place, ask the user where to put it.

Inside that place, make one folder for this run. Name it the way the things
next to it are named. If there is no pattern, use
`adversarial-<YYYY-MM-DD>-<target>`, where `<target>` is `diff`, the branch
name, `pr-<n>`, the short commit hash or the last part of the path. If that
folder already exists, add `-2`, `-3`, and so on.

### 4. Run the reviewers in parallel

Decide which angles run (the table's "Skip when" column, then `--only` /
`--skip`). Launch all of them in **one message** so they run at the same
time. Use this prompt for each, filling the blanks:

```
You are the <angle> reviewer in an adversarial review of a change.

Read these two files first and follow them:
- <skill dir>/angles/<angle>.md      (your angle)
- <skill dir>/reviewer-rules.md      (how to work, how to write your file)

Target: <what is reviewed>

Change brief:
<the brief from step 2>

User's focus: <the --focus text, or "none">

Angles running at the same time as you: <angle: topic, ...>.
Leave their topics to them.

Write your findings to: <run folder>/<angle>.md
That file is the only thing you may create or change in the repo. No other
edits, no commits, no checkout, no stash. Scratch files go outside the repo.

Model for any subagent you launch: <sonnet, or the --model value>.

When you are done, reply with only the short list described under "Your
reply" in reviewer-rules.md.
```

Do not review the code yourself while they run, and do not add your own
suspicions to their prompts: a reviewer handed a guess tends to confirm it.

If a reply has a **Needs follow-up** part (the reviewer could not launch its
own subagents), launch those subagents yourself, then send the results back
to that reviewer.

If a reviewer fails or leaves no file, write its file yourself with a
`## Not run` heading and the reason. A missing file must never look like
"no findings".

### 5. Verify the findings

Skip this step with `--no-verify`.

Findings marked `Proof: ran it` pass as they are: the reviewer ran a test or
a script that showed the failure.

For every other finding, launch one verifier per angle file, all in one
message. Give each verifier only what the reviewer's reply lists for each
finding (id, title, where, what, how it fails), not its reasoning and not
its suggested fix. Use this prompt:

```
You are a verifier in an adversarial review. Each finding below claims the
code fails. Your job is to prove each claim wrong.

For each one, read the code at "where", its callers and the code it calls.
Look for the check, the transaction, the constraint or the caller that makes
the failure impossible. When a test or a scratch script settles it, run it.
Scratch files go outside the repo.

How to see the change:
<the commands from the brief>

Findings:
<id, title, where, what, how it fails, for each>

Decide every verdict before you open the findings file. Then open
<run folder>/<angle>.md and add one line at the end of each finding:
- Verdict: confirmed | not real | cannot tell — <evidence: file:line or command output>
Move each "not real" finding, whole, under a "## Rejected by the verifier"
heading at the end of the file. Change nothing else in that file and
nothing else in the repo.

Reply with one line per finding: id, verdict.
```

### 6. Write the summary

Write `<run folder>/README.md` in this shape:

```
# Adversarial review: <target>

How to see the change: `<command>`
Ran: <angle (model), ...>. Verifiers: <model, or "skipped (--no-verify)">. Mapper: <model>.
Skipped: <angle — why>.
Focus: <text, or "none">

| Angle             | High | Medium | Low | Rejected |
| ----------------- | ---- | ------ | --- | -------- |
| [logic](logic.md) | 1    | 2      | 0   | 1        |

## High
- **LOG-1: <title>** — `path:line` → [logic.md](logic.md)

## Medium
- ...

## Could not confirm
- **ACC-2: <title>** — what would settle it → [access.md](access.md)

## Not checked
<one line for each gap the reviewers named>
```

Count only findings that were not rejected. Low findings stay in their angle
files. When two angles found the same root cause, list it once and name both
ids. The models are the ones each subagent really ran on, by name (`fable`,
`opus`, `sonnet`, `haiku`), never the word `inherit`.

Do not commit anything.

### 7. Tell the user

If the session has a tool or a skill that lets the user review or annotate
a file, open `README.md` with it and wait for what the user sends back. If
there is none, give a clickable link to `README.md`.

**In the chat**, keep it short: the link, the number of findings per
severity, and the titles of the High ones. Then ask which findings to fix.
Fix nothing before the user answers.

## Guardrails

- Report only. The run folder is the one thing a review writes in the repo.
- Scripts, servers and browsers run against a local or development
  environment, never production.
- No finding without a location and a concrete way it fails.
- A skipped or failed angle is named in `README.md` with the reason.
- Keep this skill free of rules about any one repo. A rule that belongs to
  a codebase goes in that codebase's instruction files.

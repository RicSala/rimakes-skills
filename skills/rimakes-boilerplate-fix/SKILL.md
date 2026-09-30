---
name: rimakes-boilerplate-fix
description: Pick and fix a GitHub issue from the current repo. Reads open issues, ranks the 3 most urgent/relevant, lets the user choose, verifies the issue's claims against the current code (never trusting its file references), plans the fix in plan mode, implements after approval, then verifies and closes the issue. Use when the user wants to work through issues — e.g. "let's fix an issue", "what should we tackle from the roadmap", "pick something from the backlog", "work on the issues".
---

# Fix a GitHub Issue

Work one issue from the current repo's GitHub tracker end to end: triage →
user picks → verify claims → plan → implement → verify → close.

## Steps

### 1. Locate the repo and list issues

```bash
gh repo view --json nameWithOwner -q .nameWithOwner
gh issue list --state open --limit 100 --json number,title,labels,createdAt,updatedAt
```

If the current directory has no GitHub repo, or there are no open issues, say
so and stop.

### 2. Rank the top 3

Read the bodies of the plausible candidates (`gh issue view <n>`) — never
rank on titles alone. Rank by, in rough order:

1. `priority: high` label, then medium, then low
2. `bug` before `enhancement`; `idea` last (usually not directly actionable)
3. Issues touching code that changed recently (higher risk of going stale)
4. Older issues before newer ones, as a tiebreak

### 3. Ask the user which one to fix

Present the 3 candidates as a structured question (AskUserQuestion in Claude
Code): each option labeled `#<n> <short title>`, with a one-line description
of what it is and why it ranked. The user can always answer with a different
issue number instead.

### 4. Verify the issue — trust nothing in it

The issue was written against an older tree, possibly by someone else, and
may be wrong or stale. Before planning, do a deep exploration:

- Read the full issue **including comments**: `gh issue view <n> --comments`.
- Treat every claim as unverified: file paths, line numbers, snippets,
  described behavior. Find the current code yourself by searching for the
  symbols and behavior involved — do not navigate by the paths the issue
  cites, they may have moved or been rewritten.
- Check what changed since it was filed:
  `git log --oneline --since="<issue createdAt>" -- <relevant paths>`, and
  read any commits that touched the area.
- Reproduce the problem if feasible (failing test, quick script, running the
  app) so the "after" state can be compared.

Classify the result:

- **Still valid** → continue to step 5.
- **Partially valid** → continue, but the plan must state what the issue got
  wrong and what the real scope is.
- **Already fixed / obsolete** → show the user the evidence (the commit that
  fixed it, or why it no longer applies). With their confirmation, close it
  with a comment explaining why
  (`gh issue close <n> --comment "<evidence>"`), then offer the remaining
  ranked candidates.

### 5. Plan in plan mode

Enter plan mode (EnterPlanMode) and write the implementation plan — the
exploration is already done, so go straight to the plan. Ground every step in
the verified current code, not the issue's text; where they diverge, the plan
says so. Present it for approval (ExitPlanMode). If plan mode isn't available
(e.g. running outside Claude Code), write the plan in the chat and get an
explicit go-ahead before touching any file.

### 6. Implement

Only after the plan is approved. Stay within the plan's scope; if
implementation reveals the plan was wrong, stop and re-plan rather than
improvising.

### 7. Verify the fix

- Run the project's own checks for the touched area (tests, typecheck, lint —
  whatever this repo defines).
- Re-run the reproduction from step 4 and confirm the behavior is now
  correct. A green test suite alone is not verification if the issue's
  scenario was never exercised.
- If verification fails, keep working or report honestly — never continue to
  step 8.

### 8. Close the issue

Once the fix is verified and the work is committed (follow the user's normal
commit flow; reference the issue in the message, e.g. `Fixes #<n>`), close
it with a comment that says what was changed, how it was verified, and the
commit hash:

```bash
gh issue close <n> --comment "<what changed, verification, commit>"
```

If the commit was pushed to the default branch with `Fixes #<n>`, GitHub may
have closed it already — check state first, and just add the comment if so.

### 9. Report

The issue link, what was done, how it was verified, and the commit. Mention
anything discovered during verification that contradicted the issue.

## Guardrails

- One issue per invocation.
- Never put an unverified claim from the issue into the plan or the code.
- Never close an issue without either a verified fix in the tree or the
  user's explicit confirmation that it is obsolete.
- Touch only the selected issue — never edit or close others. If a duplicate
  or related issue turns up, tell the user instead of acting on it.

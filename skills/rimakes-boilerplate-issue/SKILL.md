---
name: rimakes-boilerplate-issue
description: File a bug, footgun, improvement or idea for the boilerplate the current project was started from, as a GitHub issue on the boilerplate's repo (named in `.boilerplate.json`). Use when work in a product runs into the boilerplate, such as code in `packages/` or `config/` that is wrong, a mistake the boilerplate let happen, something most products would need, or when the user says "report this to the boilerplate", "file it upstream", "this should be in the boilerplate".
---

# Boilerplate Issue

The user's projects start from a boilerplate repo, and its roadmap lives in
that repo's GitHub issues. Capture what the user described, or what you ran
into, as an issue there: never as a local file, and never in the current
project's issues. Show the draft first; posting to GitHub is the user's call.

## Steps

1. **Find the boilerplate.** Read `.boilerplate.json` at the root of the
   current repo:

   ```json
   { "repo": "RicSala/boilerplate-turbo-betterauth", "commit": "0c9b585" }
   ```

   - `repo` is where the issue goes. If the file or the field is missing,
     ask the user for the repo, then offer to create the file.
   - `commit` is the boilerplate commit the project started from. If the
     current repo *is* the boilerplate (its `origin` is `repo`), use
     `git rev-parse --short HEAD` instead. If it is missing, write
     `unknown` in the issue.

   Below, `$REPO` and `$COMMIT` are those values.

2. **Gather the content.** From the conversation and the current repo, work
   out: what should change in the boilerplate, why (what friction or bug in
   the current project motivated it), and concrete pointers (file paths,
   snippets, the workaround applied). If the project already has a fix,
   include its diff (`git diff` or `git show <sha>`, trimmed to the relevant
   hunks) so porting it back is mechanical. If the request is too vague to
   write an issue a future session could act on, ask one or two short
   questions first. For a footgun, also answer: what would have stopped it?
   That answer is the change to ask for.

3. **Check for an existing issue**, so the roadmap does not collect
   duplicates:

   ```bash
   gh issue list -R $REPO --state all --search "<2-3 keywords>" --limit 10
   ```

   If a matching open issue exists, add the new context as a comment
   (`gh issue comment <number> -R $REPO --body-file <file>`) instead of
   opening a new one. If a matching issue is closed, open a fresh one that
   says `Relates to #<number>`.

4. **Pick labels**, exactly one of each:

   - Type: `bug` (boilerplate code does the wrong thing) | `footgun` (it
     worked as designed but cost time: an easy mistake, a misleading error,
     a missing guard, a rule AGENTS.md should state) | `enhancement` (a
     concrete change most products would want) | `idea` (a direction to
     explore, not yet actionable)
   - Priority: `priority: high` | `priority: medium` | `priority: low`,
     inferred from how much friction it caused; medium when unsure.

5. **Create the issue.** Show the title, the labels and the body in chat,
   and file only after the user says yes: this skill may have started on
   its own, and posting is theirs to decide. Write the body to a temp file
   first (avoids shell quoting problems), then:

   ```bash
   gh issue create -R $REPO \
     --title "<Short imperative title>" \
     --label "<type>" --label "priority: <level>" \
     --body-file <file>
   ```

   Body:

   ```markdown
   ## What
   <The concrete change to make in the boilerplate.>

   ## Why
   <The friction or bug hit in the source project.>

   ## Context
   <File paths, snippets, the workaround or the diff. Enough that a session
   in the boilerplate can act without access to the source project. Paths
   are the project's; the boilerplate may have drifted.>

   ---
   Source: <current project name> · Boilerplate commit: $COMMIT · Filed via rimakes-boilerplate-issue
   ```

   One issue per improvement. Several unrelated improvements are several
   issues.

6. **Report back** with the clickable issue URL, the title and the labels.
   If you commented on an existing issue instead, link it and say so.

7. **Tag the local fix.** If the project patched the boilerplate's code,
   the patch is a workaround on a dependency. Mark it in every file it
   touches, so the next `/rimakes-boilerplate update` can drop it:

   ```ts
   // WORKAROUND $REPO#<n>: <what the boilerplate gets wrong>. Remove when
   // the fix ships in the boilerplate and an update brings it in.
   ```

## If GitHub is unreachable

If `gh` fails (offline, auth expired), do not lose the note: save the body
to `<current-repo>/boilerplate-issue-draft-<slug>.md`, say where it is and
why, and give the command to file it later:

```bash
gh issue create -R $REPO --title "<title>" --label "<type>" --label "priority: <level>" --body-file boilerplate-issue-draft-<slug>.md
```

## Guardrails

- File only on `$REPO`, never on the current project's repo.
- Never post without the user's yes on the draft.
- Do not create labels. If one is missing on the repo, file without it and
  say so.
- Never edit or close existing issues; only comment.

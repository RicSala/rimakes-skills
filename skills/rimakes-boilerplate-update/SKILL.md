---
name: rimakes-boilerplate-update
description: Pull the latest boilerplate changes into a project started from it. Merges the boilerplate's `upstream` remote, resolves conflicts by layer (the boilerplate owns `packages/` and `config/`, the product owns `composition/`, `features/` and `apps/`), runs the checks and bumps `commit` in `.boilerplate.json`. With `new <owner>/<name>`, starts a new project from the boilerplate instead, wired so later updates can land.
argument-hint: "[new <owner>/<name>]"
disable-model-invocation: true
---

# Boilerplate Update

A project is a clone of the boilerplate that kept its history. The project's
`origin` is its own repo; the boilerplate is a second remote, `upstream`.
Updates land with one merge from `upstream/main`.

The merge stays cheap because of the layers. The boilerplate owns `packages/`,
`config/` and the root files. The product owns `composition/`, `features/` and
`apps/`. A file only conflicts when both sides changed it.

Mode: with `new <owner>/<name>`, start a project (second half of this file).
With no argument, pull updates into the current project.

## Pull updates

### 1. Find the boilerplate and check the tree

Read `.boilerplate.json` at the project root:

```json
{ "repo": "RicSala/boilerplate-turbo-betterauth", "commit": "28f558d" }
```

- `repo` is the boilerplate. If the file or the field is missing, ask the
  user, then create the file.
- `commit` is the boilerplate commit the project started from, or the one
  the last update brought in. If it is missing, use the merge base from
  step 3 and write it.
- If the current repo's `origin` *is* `repo`, this is the boilerplate
  itself. Say so and stop.

Below, `$REPO` and `$COMMIT` are those values.

The working tree must be clean (`git status --porcelain` prints nothing).
If it is not, ask the user to commit or stash, and stop.

### 2. Wire the upstream remote

```bash
git remote get-url upstream 2>/dev/null || git remote add upstream git@github.com:$REPO.git
git fetch upstream
git config merge.ours.driver true
```

The last line turns on the `ours` merge driver used in step 4. It is a
per-clone setting, so run it every time; it is harmless when already set.

### 3. Show what is new

```bash
git merge-base HEAD upstream/main
```

If this fails, the project has no history in common with the boilerplate
(it was made with GitHub's "Use this template", or from a squashed copy).
Graft the project's first commit onto `$COMMIT`, once:

```bash
git replace --graft "$(git rev-list --max-parents=0 HEAD)" $COMMIT
```

Git now treats `$COMMIT` as the parent of the project's root commit and
merges normally. The graft lives in `refs/replace/`, which is local: tell the
user that other clones need the same command, or
`git push origin 'refs/replace/*'`.

Then list the change:

```bash
git log --oneline --no-merges $COMMIT..upstream/main
git diff --stat $COMMIT upstream/main
```

If nothing is new, say so and stop. Otherwise tell the user in plain words
what came in, grouped by area (`packages`, `apps`, `config`, root, docs).
Call out these, since they need action after the merge:

- `.env.example` changed: new or renamed env keys.
  `git diff $COMMIT upstream/main -- .env.example`
- New migrations:
  `git diff --name-only $COMMIT upstream/main -- packages/db/prisma/migrations`
- `pnpm-workspace.yaml` changed: a catalog bump (React, Expo).
- `AGENTS.md` changed: rules the product may want to adopt. Its text does
  not merge in when the product also changed it (step 4), so quote the
  changed lines.

### 4. Product-owned files keep the product's version

The merge driver `ours` keeps the current branch's version of a file **when
both sides changed it**. When only upstream changed it, upstream's version
lands as usual. Make sure the root `.gitattributes` has these lines, and
commit it before merging if you had to add them:

```
composition/src/config/** merge=ours
README.md merge=ours
AGENTS.md merge=ours
```

Add a line for any other file the product rewrote and never wants back from
the boilerplate.

### 5. Merge

```bash
git merge --no-commit --no-ff upstream/main
```

`--no-commit` stops before committing even when there are no conflicts, so
steps 6 and 7 go into the same merge commit.

Resolve conflicts by what the path is:

| Path | Resolution |
| --- | --- |
| `pnpm-lock.yaml` | Never merge by hand. `git checkout --theirs pnpm-lock.yaml`; `pnpm install` in step 6 rewrites it. |
| A path the product deleted (`features/todo`, `apps/mobile`, …): modify/delete conflicts, or new files from upstream | Keep it deleted: `git rm -rf --quiet <path>`. `-f` is needed because upstream's new files are staged. |
| `packages/**`, `config/**` | Take upstream: `git checkout --theirs <path>`. If the product changed the file, that change belongs in the boilerplate. Tell the user, keep upstream, and offer `/rimakes-boilerplate-issue` to send the change upstream. Carry it over by hand only if the user asks, and say it will conflict again. |
| `composition/**` | The product's wiring wins. Bring in upstream's new wiring only when it is infrastructure (a handler for a new auth or billing event, a new transport mount), not a `todo` mount. |
| `apps/web/components/auth/**`, `apps/web/lib/auth/**` | Vendored UI: take upstream, then re-apply the local edits listed in the project's `AGENTS.md`. |
| Other `apps/**` | The product's screens win. Take upstream for framework files (`proxy.ts`, `next.config.ts`, `instrumentation.ts`, `env.ts`) unless the product changed them on purpose. |
| `packages/i18n/src/dictionaries/**` | Both sides add keys. Keep both sets. The parity test catches a miss. |

Read every conflicted hunk before choosing a side. `git checkout --theirs`
on a path that holds a product change loses that change.

After resolving, `git add` each path. Do not commit yet.

### 6. Install and check

```bash
pnpm install
pnpm typecheck && pnpm lint && pnpm test
```

If new migrations came in, apply them locally with `pnpm db:migrate`
(`prisma migrate dev`) and tell the user production needs
`prisma migrate deploy` on its next release.

Fix what fails when the merge caused it: a wrong side taken in a conflict,
an export upstream renamed that the product imports. If the failure is in
upstream code the product did not touch, the boilerplate itself is broken:
say so, and offer `/rimakes-boilerplate-issue`.

### 7. Record and commit

Set `commit` in `.boilerplate.json` to `git rev-parse --short upstream/main`.
Then:

```bash
git add -A
git commit -m "chore(boilerplate): merge upstream <short sha>"
```

Do not push; the user does.

### 8. Report

- What landed, by area, in a few lines.
- Each conflict and the side kept. Name every `packages/` or `config/` file
  where the product's change was dropped or carried over.
- What the user must do by hand: new env keys, migrations on production,
  rule changes from the boilerplate's `AGENTS.md`.

## Start a new project (`new <owner>/<name>`)

Run from the folder that will contain the project. The boilerplate is
`RicSala/boilerplate-turbo-betterauth` unless the user names another.

### 1. Clone with history

```bash
git clone git@github.com:RicSala/boilerplate-turbo-betterauth.git <name>
cd <name>
git remote rename origin upstream
```

Keep the history. Do not use GitHub's "Use this template" or `--depth 1`:
without a common ancestor, every later update is one big conflict.

### 2. Create the project's own repo

```bash
gh repo create <owner>/<name> --private --source=. --remote=origin --push
```

If `gh` is missing or offline, give the user this command to run later and
go on.

### 3. Record the starting point

Write `.boilerplate.json`:

```json
{
  "repo": "RicSala/boilerplate-turbo-betterauth",
  "commit": "<git rev-parse --short upstream/main>"
}
```

Write the `.gitattributes` lines from step 4 above, and run
`git config merge.ours.driver true`.

### 4. Make it the product's

- Rename what names the boilerplate: `name` in the root `package.json`, the
  title of `README.md`, the first line of `AGENTS.md`. Keep the rules in
  `AGENTS.md`: they are the product's rules too.
- Ask the user whether to delete the reference feature `features/todo` now
  or keep it as an example for a while. Deleting it also means removing its
  mounts in `composition/`, its screens in `apps/web` and its keys in
  `packages/i18n`, then running `pnpm typecheck`, `pnpm lint` and
  `pnpm test` until clean.
- Point the user at the boilerplate's `README.md` for local setup (`.env`,
  `pnpm install`, `pnpm db:start`, `pnpm db:reset`, `pnpm dev`). Do not
  repeat it here.
- Commit: `chore: start <name> from boilerplate <short sha>`. Push.

### 5. Explain the update loop

Tell the user, in three lines:

- `/rimakes-boilerplate-update` brings later boilerplate changes in.
- Changing `packages/` or `config/` in the product is allowed, but each
  change there conflicts at every update that touches the same file. A
  change that is not specific to this product goes to the boilerplate
  first (`/rimakes-boilerplate-issue`) and comes down with the next update.
- Never rebase the project onto upstream. The history is shared with the
  team; a merge is the only safe way in.

## Guardrails

- Never merge with a dirty working tree.
- Never `git push` in update mode. In `new` mode, pushing is the point.
- Never drop a product change in `packages/` or `config/` without saying so
  in the report.
- Never rebase the project's branch onto `upstream/main`.
- `git merge --abort` only when the user asks for it.

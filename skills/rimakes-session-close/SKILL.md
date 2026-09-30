---
name: rimakes-session-close
description: Wrap up and safely close out a coding session on a Next.js project. Run the end-of-session checklist — sync project docs, verify nothing is left half-finished or inconsistent, and give a plain-language verdict on whether it's safe to stop. Invoke only when the user explicitly runs /rimakes-session-close.
disable-model-invocation: true
---

# Session Close

A pre-flight checklist for the **end** of a working session on a Next.js
project. The person running this may not be technical — your job is to do the
checking for them and then tell them, in plain language, whether they can walk
away or whether something still needs attention.

Think of yourself as the responsible engineer doing a final walk-through before
handing the keys back: lights off, doors locked, nothing on fire.

## Why this exists

It's easy to end a session with loose ends that aren't obvious to a
non-technical user: uncommitted work, a build that no longer compiles, a
database migration created but never applied, a doc that now lies about how the
project works, or a dev server left running. Each of these turns into a
confusing problem later. This skill catches them while the context is fresh.

## How to work through it

Adapt to whatever this particular project actually uses — detect the package
manager (look for `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, or
`bun.lockb`), and only run the checks that apply. Run real commands and report
real results; never assume something passed without checking. If a check isn't
relevant to this project, skip it and say so briefly.

Do the checks first, fix the cheap/safe things if appropriate, and save the
verdict for the end.

### 1. Sync the project docs (only what's stale)

Look at what changed this session and check whether the project's guidance docs
still tell the truth — typically `CLAUDE.md`, `AGENTS.md`, and `README.md`.
Things that commonly go stale: scripts in `package.json`, required environment
variables, setup/run commands, the tech stack, routes, or folder structure.

Rules that matter here:

- **Only update what is now inaccurate.** Do not add new sections, restructure,
  or "improve" the docs. The goal is correctness, not expansion. A non-technical
  owner is relying on these files staying lean and trustworthy.
- **Never hand-edit auto-generated blocks.** If content sits between marker
  comments (e.g. `<!-- BEGIN:... -->` / `<!-- END:... -->`), it's managed by a
  tool and will be overwritten — leave it alone.
- **If a file only imports another** (e.g. `CLAUDE.md` containing `@AGENTS.md`),
  follow the import and check the real source.
- If you genuinely find nothing stale, that's a valid outcome — say so. Don't
  invent changes to look busy.

If you do change a doc, commit it with a clear message and push (see step 2).

### 2. Git state

The most common way to lose work is to leave it uncommitted or unpushed.

- Run `git status` — is the working tree clean? Surface anything uncommitted and
  decide with the user whether it should be committed or is intentionally
  scratch.
- Confirm the current branch is **synced with its remote** (nothing un-pushed).
- If you made doc fixes in step 1, commit and push them now.
- Confirm secrets and local-only files (`.env*`, generated clients, build
  output) are git-ignored and were **not** committed.

### 3. Does it still build?

A session can end with code that no longer compiles. Verify the project is in a
runnable state:

- Type-check (`tsc --noEmit`) and/or the production build (`next build`).
- Run the linter if the project has one wired up.

Report pass/fail with the actual output. A failing build is a hard blocker for
"safe to close."

### 4. Database / ORM (if present)

If the project uses a database or ORM (e.g. Prisma, Drizzle), a frequent loose
end is a schema change that was never applied:

- Is the schema in sync with the migrations, and are migrations applied to the
  target database?
- Was any test/scratch data left behind that the owner should know about?

### 5. Deployment & environment (if applicable)

If the project deploys somewhere (e.g. Vercel) and a change was pushed this
session:

- Did the latest deploy actually succeed?
- Do the key routes respond (a quick check of the homepage and any route you
  touched)?
- Are any **new** environment variables needed in the deployment target, not
  just locally? Flag them explicitly — this is a classic "works on my machine"
  trap.

### 6. Loose ends & cleanup

- Stop any **dev servers or background processes** this session started, so
  nothing keeps running unattended. (Be careful to only stop processes you
  started — don't kill unrelated apps.)
- Scan for anything half-finished introduced this session: stubbed functions,
  `TODO`/`FIXME` you added, dead code, or a feature wired up only partway. Name
  them; don't silently leave them.

## The verdict (always end with this)

Close with a short, jargon-free summary the owner can act on immediately. Lead
with one of two clear states:

- **✅ Safe to close** — everything is committed, pushed, building, and
  consistent. Briefly list what you confirmed.
- **⚠️ Not yet** — there's an open item. List each one as a concrete,
  plain-language action ("Your latest changes aren't saved to GitHub yet" rather
  than "working tree dirty"), and say what you recommend doing about it.

Keep it honest. If you're unsure about something, say so rather than implying
everything is fine. The whole point is that the person can trust this verdict
without having to understand the underlying details.

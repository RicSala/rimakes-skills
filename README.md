# rimakes-skills

A small catalog of [Agent Skills](https://skills.sh) for Claude Code (and other
compatible coding agents), installable with the `skills` CLI.

## Skills in this repo

"Slash-command only" means the agent never starts the skill by itself: you type
`/<skill-name>`.

### Plan and document

#### `rimakes-product-spec`

Drives an in-depth conversation to define a product, then captures everything in a single,
lossless `SPEC.md` — a living product spec with per-feature build-status tracking (🔴 `TODO` /
🟡 `WIP` / 🟢 `DONE` / ⏸️ `BLOCKED`), a phased roadmap, and a decisions log. Use it when starting a
new project or defining what an app should do, and to keep the spec in sync as the product is built.

#### `rimakes-tracker`

Turns a task or plan into one Markdown tracker file: the plan and its progress record in one
place, with workstreams, checkbox tasks, "done means" lines, a decisions table and a progress
log. Also updates the tracker as tasks land.

#### `rimakes-adr`

Writes an Architecture Decision Record in `docs/ADRs/`. It first checks the decision against
the criteria, and says so when the decision does not need an ADR. Slash-command only.

### Review

#### `rimakes-adversarial`

Tries to break a change before it ships. Several subagents each attack it from one angle
(logic, hostile input, access, timing, failure, rollout), verifiers drop the false alarms, and
each angle writes its findings to its own file in one folder. Reviews the current diff by
default, or a branch, a PR, a commit range or a path. Slash-command only.

#### `rimakes-feature-review`

Reviews one whole feature folder with six reviewer subagents in parallel (simplifier,
bug-hunter, ux-checker, test-checker, access-checker, conventions-checker), verifies what they
find, and writes one ranked report. Report only, no edits. Slash-command only.

### Explain and talk

#### `rimakes-teach-me`

Builds an interactive Artifact page that teaches a topic: pictures, step-throughs, sliders,
code tracers and quizzes. Ships copy-ready widgets in `widgets/`. Saves the page's link in the
repo's `learning/ARTIFACTS.md` so you can find it again.

#### `rimakes-human-talk`

Rewrites the last reply, or the file just written, in plain and direct language.
`--technical` explains the ideas step by step; `--context` puts the code it refers to in the
chat. Slash-command only.

#### `rimakes-wdyt`

"What do you think?" An honest opinion on a topic, with the reasons, in plain language. No
edits. Slash-command only.

### Build

#### `rimakes-design-variants`

Scaffolds three designs for a component or page with an in-browser switcher (`?v=1|2|3`), then
collapses back to the one you pick.

#### `rimakes-empty-states`

Designs and builds empty states for React and Next.js screens. It finds why the screen is empty
(first use, no results, all done, no permission), writes the title, the sentence and the one
action, adds a preview, and makes sure the empty state never shows before the app knows the list
is empty. Ships preview recipes in `previews/`.

#### `rimakes-boilerplate-fix`

Picks an open GitHub issue in the current repo, checks its claims against the code, plans the
fix in plan mode, fixes it after approval, and closes the issue.

#### `rimakes-boilerplate-issue`

Files an idea, improvement or bug on the boilerplate the current project was started from. The
boilerplate's repo comes from `.boilerplate.json` at the project root. Slash-command only.

#### `rimakes-boilerplate-update`

Pulls the latest boilerplate changes into a project started from it: one merge from the
boilerplate's `upstream` remote, conflicts resolved by layer (the boilerplate owns `packages/`
and `config/`, the product owns `composition/`, `features/` and `apps/`), checks run, and
`commit` in `.boilerplate.json` bumped. `new <owner>/<name>` starts a project from the
boilerplate, wired so later updates can land. Slash-command only.

### End a session

#### `rimakes-session-close`

An end-of-session checklist for a coding session (Next.js-oriented): syncs project docs, verifies
nothing is left half-finished or inconsistent, and gives a plain-language verdict on whether it's
safe to stop. Slash-command only (`/rimakes-session-close`).

## Install

```bash
# Install everything from this repo (interactive picker)
npx skills add RicSala/rimakes-skills

# Install all, no prompts
npx skills add RicSala/rimakes-skills --all

# Install a single skill, globally (user-level)
npx skills add RicSala/rimakes-skills -s rimakes-product-spec -g

# Just list what's in the repo without installing
npx skills add RicSala/rimakes-skills -l
```

## Keep them up to date

These skills are maintained here — this repo is the source of truth. When new versions are pushed,
update your local copies with:

```bash
npx skills update            # update all installed skills (asks scope)
npx skills update -g         # only global skills
npx skills update rimakes-product-spec   # a specific skill
```

`skills update` re-fetches the latest version from this GitHub repo (the source is recorded in your
`skills-lock.json`) and refreshes the local copy.

## Repository layout

Each skill is a self-contained folder under `skills/`, holding a `SKILL.md` (the entry
point, with the skill's name and trigger description in its frontmatter) plus whatever it
bundles — `assets/` for templates it writes from, `references/` for deep-dive files it
loads on demand. The `skills` CLI discovers skills by folder, so adding one (or adding
files inside one) requires no registry edits anywhere else in the repo.

## License

Not set yet — add one (e.g. MIT) before sharing widely if you want others to reuse it freely.

# agent-skills

This repository is a **catalog of Agent Skills** authored by the user, meant to be published on
[skills.sh](https://skills.sh) and installed with the `skills` CLI (`npx skills add OWNER/agent-skills`).
It is **not** an application — there is no build, no runtime. The deliverable is the skills themselves:
Markdown instructions (plus bundled assets) that teach a coding agent how to do something well.

The user is a **beginner** (capable, but not deep in tooling jargon) — adapt communication accordingly,
prefer plain language, and suggest commits often (conventional commits).

## What's here

Two skills, each a self-contained folder under `skills/`:

- **`product-spec`** — drives an in-depth conversation to define a product and produces a single,
  lossless `SPEC.md` (living product spec + per-feature build-status tracking + phased roadmap +
  decisions log). Has a bundled template at `skills/product-spec/assets/SPEC_TEMPLATE.md` that the
  skill copies from; `SKILL.md` references it by **relative path**, so the `assets/` folder must
  always travel with the skill — don't move or rename it.
- **`session-close`** — an end-of-session checklist skill (Next.js-oriented). Slash-command only
  (`disable-model-invocation: true` in its frontmatter → it runs only when the user types
  `/session-close`, never auto-triggered).

**These skills are carefully tuned and the user likes them as they are.** Don't restructure or
"improve" them unprompted. Make surgical, requested changes and keep their shape.

## How Agent Skills & skills.sh work (context for maintaining this repo)

- A skill is a folder with a **`SKILL.md`**: YAML frontmatter (`name` + `description` required) then
  Markdown instructions. `description` is the primary trigger — it should say *what it does AND when
  to use it*, and lean slightly "pushy" so the skill isn't under-triggered.
- **Publishing = a public GitHub repo.** No registration. skills.sh is a leaderboard populated by
  anonymous install telemetry; it does not gate or review.
- **This repo uses the "catalog" layout** the CLI expects: multiple skills under `skills/<name>/`.
  Each skill directory is the unit that gets installed (including its `assets/`).
- **Updates:** when this repo changes and is pushed, users run `npx skills update` — it re-fetches the
  latest from GitHub (the source repo + path are recorded in each consumer's `skills-lock.json`) and
  refreshes their local copy. So the maintenance loop is: **edit here → `git push` → users
  `skills update`.** This repo is the source of truth.

## Repository layout

```
agent-skills/
├── CLAUDE.md          # this file
├── README.md          # public-facing: what's inside + install/update commands
└── skills/
    ├── product-spec/
    │   ├── SKILL.md
    │   └── assets/SPEC_TEMPLATE.md
    └── session-close/
        └── SKILL.md
```

To add a new skill: `npx skills init <name>` (or create `skills/<name>/SKILL.md` by hand), then
document it in `README.md`.

## Design principles baked into `product-spec` (understand before editing it)

The skill encodes a specific philosophy — respect it:

- **One document, two readers:** the same `SPEC.md` serves a non-technical user (product sections)
  and technical AI instances (whole doc). Never split into two docs.
- **Lossless:** capture decisions *and* their rationale *and* the discarded alternatives.
- **Behavior, not implementation:** plain-language behavior + *decided* tech choices; never DB
  schemas / code / folder layout.
- **Phases as vertical slices:** each phase is a thin end-to-end path defined by a *user outcome*,
  not a technical layer. Plus an anti-silo nudge (slice by cross-feature outcome, build the
  integrating surface first, make cross-feature interactions explicit, "done" includes connections).
- **Fidelity dial:** default **production**; switch to **prototype** ("lite"/"course"/"quick") to cut
  *depth* (happy-path only, no edge cases, no non-functional hardening, demo-level "done"). This is
  the lever for fast, course-friendly builds without losing the spec's shape.
- **Placement & wiring:** save `SPEC.md` under `docs/` (create `docs/` if there's no obvious docs
  home), add a one-line pointer + a "keep it up to date" standing instruction to the project's
  `CLAUDE.md`, and fill a project description there if missing.
- **Drift is the AI's job:** whenever features/phases/status change, the AI updates the spec — not the user.

## Commands (skills CLI)

- `npx skills add OWNER/agent-skills [--all | -s <skill> | -l] [-g]` — install / list
- `npx skills update [-g | -p | <skill>]` — pull latest from source
- `npx skills list` — list installed
- `npx skills init <name>` — scaffold a new skill

## Known follow-up

The two skills currently also live installed at `~/.claude/skills/` (where they were originally
authored). Once this repo is the source of truth, those copies should be reconciled (reinstall from
the repo, or symlink) to avoid divergence. The user is aware and will handle this later.

## References

- skills.sh — the leaderboard / ecosystem
- `github.com/vercel-labs/skills` — the open-source CLI that powers install/update

# rimakes-skills

This repository is a **catalog of Agent Skills** authored by the user, meant to be published on
[skills.sh](https://skills.sh) and installed with the `skills` CLI (`npx skills add RicSala/rimakes-skills`).
It is **not** an application — there is no build, no runtime. The deliverable is the skills themselves:
Markdown instructions (plus bundled assets) that teach a coding agent how to do something well.

The user is a **beginner** (capable, but not deep in tooling jargon) — adapt communication accordingly,
prefer plain language, and suggest commits often (conventional commits).

## What's here

Twelve skills, each a self-contained folder under `skills/`. Every skill name starts with
`rimakes-`, the user's namespace; the folder name and the `name:` in `SKILL.md` always match.
`README.md` has a one-paragraph description of each. Two need extra care:

- **`rimakes-product-spec`** — drives an in-depth conversation to define a product and produces a single,
  lossless `SPEC.md` (living product spec + per-feature build-status tracking + phased roadmap +
  decisions log). Has a bundled template at `skills/rimakes-product-spec/assets/SPEC_TEMPLATE.md` that the
  skill copies from; `SKILL.md` references it by **relative path**, so the `assets/` folder must
  always travel with the skill — don't move or rename it.
- **`rimakes-session-close`** — an end-of-session checklist skill (Next.js-oriented). Slash-command only
  (`disable-model-invocation: true` in its frontmatter → it runs only when the user types
  `/rimakes-session-close`, never auto-triggered).

Kept out of this public repo on purpose (they stay local for now):

- **`rimakes-unslop`**: a changed copy of `cursor/plugins`' unslop skill. That repo has no
  license, so it cannot be republished here.
- **`dev-patterns`**: its `resources/` holds source code from a private repo. The user will
  decide later how to publish it. Its untracked folder under `skills/` is not committed.

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
rimakes-skills/
├── CLAUDE.md          # this file
├── README.md          # public-facing: what's inside + install/update commands
└── skills/
    ├── rimakes-product-spec/
    │   ├── SKILL.md
    │   └── assets/SPEC_TEMPLATE.md
    ├── rimakes-adversarial/
    │   ├── SKILL.md
    │   ├── reviewer-rules.md
    │   └── angles/*.md         # one brief per review angle
    ├── rimakes-feature-review/
    │   ├── SKILL.md
    │   ├── report-format.md
    │   └── reviewers/*.md      # one brief per reviewer
    ├── rimakes-teach-me/
    │   ├── SKILL.md
    │   └── widgets/            # base.css, template.html, one .html per widget
    ├── rimakes-adr/            # SKILL.md + template.md
    └── rimakes-<name>/         # the rest: a single SKILL.md each
```

To add a new skill: `npx skills init <name>` (or create `skills/<name>/SKILL.md` by hand), then
document it in `README.md`.

## Design principles baked into `rimakes-product-spec` (understand before editing it)

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

- `npx skills add RicSala/rimakes-skills [--all | -s <skill> | -l] [-g]` — install / list
- `npx skills update [-g | -p | <skill>]` — pull latest from source
- `npx skills list` — list installed
- `npx skills init <name>` — scaffold a new skill

## Known follow-up

The skills were authored by hand in `~/.claude/skills/` and `~/.agents/skills/`, some under older
names (`fix-issue`, `boilerplate-issue`, `feature-review`, `tracker`, `design-variants`,
`product-spec`, `session-close`). The user plans to delete those copies and install from this repo
with `npx skills add RicSala/rimakes-skills`, so there is one source.

## References

- skills.sh — the leaderboard / ecosystem
- `github.com/vercel-labs/skills` — the open-source CLI that powers install/update

# rimakes-skills

A small catalog of [Agent Skills](https://skills.sh) for Claude Code (and other
compatible coding agents), installable with the `skills` CLI.

## Skills in this repo

### `product-spec`
Drives an in-depth conversation to define a product, then captures everything in a single,
lossless `SPEC.md` — a living product spec with per-feature build-status tracking (🔴 `TODO` /
🟡 `WIP` / 🟢 `DONE` / ⏸️ `BLOCKED`), a phased roadmap, and a decisions log. Use it when starting a
new project or defining what an app should do, and to keep the spec in sync as the product is built.

### `session-close`
An end-of-session checklist for a coding session (Next.js-oriented): syncs project docs, verifies
nothing is left half-finished or inconsistent, and gives a plain-language verdict on whether it's
safe to stop. Slash-command only (`/session-close`).

## Install

```bash
# Install everything from this repo (interactive picker)
npx skills add RicSala/rimakes-skills

# Install all, no prompts
npx skills add RicSala/rimakes-skills --all

# Install a single skill, globally (user-level)
npx skills add RicSala/rimakes-skills -s product-spec -g

# Just list what's in the repo without installing
npx skills add RicSala/rimakes-skills -l
```

## Keep them up to date

These skills are maintained here — this repo is the source of truth. When new versions are pushed,
update your local copies with:

```bash
npx skills update            # update all installed skills (asks scope)
npx skills update -g         # only global skills
npx skills update product-spec   # a specific skill
```

`skills update` re-fetches the latest version from this GitHub repo (the source is recorded in your
`skills-lock.json`) and refreshes the local copy.

## Repository layout

Catalog layout — each skill is a self-contained folder under `skills/`:

```
skills/
├── product-spec/
│   ├── SKILL.md
│   └── assets/SPEC_TEMPLATE.md   # bundled template the skill writes from
└── session-close/
    └── SKILL.md
```

## License

Not set yet — add one (e.g. MIT) before sharing widely if you want others to reuse it freely.

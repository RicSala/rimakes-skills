# rimakes-skills

A small catalog of [Agent Skills](https://skills.sh) for Claude Code (and other
compatible coding agents), installable with the `skills` CLI.

## Skills in this repo

### `rimakes-product-spec`

Drives an in-depth conversation to define a product, then captures everything in a single,
lossless `SPEC.md` — a living product spec with per-feature build-status tracking (🔴 `TODO` /
🟡 `WIP` / 🟢 `DONE` / ⏸️ `BLOCKED`), a phased roadmap, and a decisions log. Use it when starting a
new project or defining what an app should do, and to keep the spec in sync as the product is built.

### `rimakes-session-close`

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

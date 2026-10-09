---
name: rimakes-skill-new
description: Create a new skill in the user's rimakes-skills catalog, from a name and a one-line purpose, or from a workflow just done in the conversation. Writes skills/rimakes-<name>/SKILL.md in the repo with the house conventions, documents it in README.md and CLAUDE.md, commits, pushes and installs it with the skills CLI so it works right away. Use when the user says "make this a skill", "create a skill", "turn this into a skill", "new rimakes skill", or invokes /rimakes-skill-new. Also covers changing an existing rimakes skill.
argument-hint: "<name> [what it does]"
---

# New Rimakes Skill

The user's skills live in one catalog repo: `~/Developer/rimakes-skills`
(clone `git@github.com:RicSala/rimakes-skills.git` there if it is missing).
One folder per skill, `skills/rimakes-<name>/SKILL.md`. The machine installs
them from GitHub with the skills CLI into `~/.claude/skills/`. Those installed
copies are never edited; the repo is the only source.

The request: $ARGUMENTS. The first word is the name, the rest is what the skill
does. Take everything else from the conversation. When the user just did a
workflow and says "make this a skill", the steps, the commands, the
corrections they made and the shape of the output are all in the conversation:
use them.

## Steps

### 1. Pin down the skill

Answer these before writing. Ask the user only for what the conversation does
not give, in one short message.

- **Name.** `rimakes-<kebab-case>`: a verb or the thing it makes
  (`rimakes-adr`, `rimakes-teach-me`). Lowercase, hyphens, at most 64
  characters. The folder name and the `name:` field are the same.
- **What it does**, in one sentence.
- **When it triggers.** The phrases the user would say. Or slash-only: the
  agent must never start it by itself. Slash-only is right for anything that
  commits, pushes, posts, deletes, or whose moment is the user's call.
- **Input and output.** What `$ARGUMENTS` carries, and what comes out: a
  file, a message, a change in the repo.
- **Bundled files.** A template it copies (`assets/` or next to it), a
  reference it reads on demand (`references/`). Most skills need none.

Then check it does not exist already:

```bash
ls ~/Developer/rimakes-skills/skills/
npx -y skills@latest list -g
```

If a close skill exists, change that one (step 7) instead of adding a twin.

### 2. Write `SKILL.md`

Frontmatter:

```yaml
---
name: rimakes-<name>
description: <What it does>. <When to use it, with the user's phrases in quotes>.
argument-hint: "<what goes after the slash command>"
disable-model-invocation: true
---
```

- `argument-hint` only when the skill takes arguments.
- `disable-model-invocation: true` only when it is slash-only.
- The description is the trigger. It says what the skill does **and** when,
  in the third person, under 1024 characters. For a skill the agent may
  start on its own, be a bit pushy: name the situations and the phrases, so it
  is not under-triggered. For a slash-only skill, say what it does; the body
  holds the rest.

Body, in this order, like the sibling skills:

1. `# Title`, then one short paragraph: what the skill produces and the one
   idea behind it.
2. A `$ARGUMENTS` line when it takes input, with what to do when it is empty.
3. `## Steps`: numbered, imperative, small. Each step has the exact commands
   in fenced blocks and the rule for each decision. An agent must be able to
   run it from a cold start with no other context.
4. An output section when the skill writes something: show the template.
5. `## Guardrails`: the "never" lines.

Style:

- Plain language, grade 6 to 8. Short sentences. Imperative. No filler.
- Under 250 lines. Past that, move detail into `references/` (read on
  demand, say when) or `assets/` (files to copy), and point to them by
  relative path: "`template.md`, next to this file".
- No absolute paths. A `~` path is fine for the user's machine.
- Name the exact tool or command (`gh`, `git`, `pnpm`), not "use a tool".

Before moving on, read the whole file as the agent that will run it. Fix
every step that needs context the file does not give.

### 3. Document it

- `README.md`: a paragraph under the right heading (Plan and document,
  Review, Explain and talk, Build, End a session, Make skills):
  `#### \`rimakes-<name>\``, three to five lines in the same voice as the
  others, ending with "Slash-command only." when it is.
- `CLAUDE.md`: bump the count in "What's here". Add the folder to the layout
  tree only when it has files beyond `SKILL.md`.

### 4. Commit and push

```bash
cd ~/Developer/rimakes-skills
git add skills/rimakes-<name> README.md CLAUDE.md
git commit -m "feat: add rimakes-<name>"
git push origin main
```

Add only this skill's folder and the two docs. Never add untracked
local-only folders such as `skills/dev-patterns/`.

### 5. Install it

```bash
npx -y skills@latest add RicSala/rimakes-skills -s rimakes-<name> -a claude-code -g -y
test -f ~/.claude/skills/rimakes-<name>/SKILL.md && echo installed
```

Then tell the user to run `/reload-skills` in Claude Code so the new slash
command shows up in this session.

### 6. Report

- The path of the new `SKILL.md`, as a clickable link.
- How to call it: `/rimakes-<name> <args>`, or the phrases that trigger it.
- What it does, in one line.
- The `/reload-skills` reminder.

### 7. Changing an existing skill

Edit it in the repo, never in `~/.claude/skills/`. Keep its shape: the user
tunes these by hand and likes them as they are, so make the requested change
and nothing more. Then:

```bash
git commit -am "fix(<name>): <what changed>"
git push origin main
npx -y skills@latest update -g -y
```

## Guardrails

- One skill per request. Do not touch other skills.
- Never edit the installed copies under `~/.claude/skills/`.
- Never commit local-only folders (`skills/dev-patterns/`).
- Push only `main` of `RicSala/rimakes-skills`. Never force-push.
- Take trigger phrases from the conversation or from the user. Do not invent
  phrases they would not say.

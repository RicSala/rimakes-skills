---
name: product-spec
description: >-
  Drive an in-depth conversation with the user to define a product, then capture
  everything in a single lossless SPEC.md (product spec + per-feature build-status
  tracking). Use this whenever a new project or product is being started, or when the
  user wants to define, scope, or plan what an app/product should do — e.g. "let's plan
  this app", "define the features", "write a spec", "what should we build", "I have an
  idea for a product". Also use to UPDATE an existing SPEC.md as features get built or
  the product evolves (changing feature status, logging new decisions). Trigger this
  even when the user doesn't say the word "spec" but is clearly defining product scope
  or functionality from scratch.
---

# Product Spec

## What this skill is for

Turn a messy, open-ended product conversation into a single, durable document — `SPEC.md`
— that says **what the product does** and **how much of it is built**. The document is the
*output* of a real conversation, not a form to fill silently. Your job is twofold:

1. **Run the conversation** — interview the user in depth until the product is genuinely defined.
2. **Maintain the document** — keep `SPEC.md` complete, lossless, and current.

## The three principles (read these first — they drive every choice below)

**1. One document, two readers.** The same file serves a non-technical user (who reads the
product sections) and future technical AI instances (who read everything). Don't split into
"user doc" + "technical doc" — two documents become two sources of truth that drift apart.
Instead, layer by *type of information*: product behavior in plain language up top, technical
decisions and constraints in clearly-marked sections lower down.

**2. Lossless of information, not a transcript.** Capture every decision, rule, edge case, and
— critically — the *why* and the *alternatives that were discarded*. Rationale is the first
thing forgotten and the most expensive to lose. But this is not a chat log: structure it, don't
paste the back-and-forth.

**3. Behavior, not implementation.** Keep functional behavior in plain language — that's what
the user understands *and* what the AI actually needs to build well. Capture tech that's been
*decided* ("auth via magic link", "Postgres"), but never low-level implementation (table
schemas, folder layout, code). Premature implementation detail rots fast and ties the builder's
hands. The spec answers *what / why / how it behaves*, not *how it's coded*.

## When you trigger

- **No `SPEC.md` exists yet** → you're defining a product from scratch. Run the full conversation
  (below), then create `SPEC.md` from the bundled template.
- **`SPEC.md` already exists** → you're maintaining it. Read it first, then update: change feature
  statuses, add features, log new decisions, resolve open questions. Never silently drop content.

## The conversation

Interview the user section by section, but **conversationally, not as a robotic questionnaire**.
Ask a few focused questions, listen, follow threads, and probe edge cases. Go deep — a real spec
comes from pushing past the first answer ("what happens when there are no results?", "who is
*not* allowed to do that?", "what did we consider and reject?").

**Establish the goal first.** Before anything else, if the user hasn't already said it, ask what
the *goal* of the product is — the problem it solves or the outcome it's chasing, and for whom.
Everything downstream depends on this: you can't tell a good feature from a distraction, and you
certainly can't propose features the user never mentioned, without a clear objective to judge
against. Don't move into the details until you understand *why this product should exist*.

**Detail level (fidelity).** Establish early, alongside the goal, what this build is *for*: a
**production** product or a **prototype** (course, demo, MVP). It's the biggest lever on how much
gets built, so don't skip it. Default to **production**; switch to **prototype** when the user
signals it ("lite", "prototype", "course", "quick", "happy path"). In prototype fidelity, keep the
same spec *shape* and the same features — just dial the **depth** down: happy-path only (omit edge
cases and most rules), skip non-functional hardening (audit, consent, a11y/perf) unless it *is* the
product, and use demo-level "done" (the core path visibly works, not every connection wired and
verified). Stamp `Build fidelity: prototype` near the top of the spec so the implementing AI builds
at that level instead of gold-plating.

Cover these areas (they map 1:1 to the template sections). You don't have to go in order, and you
can batch related questions, but don't finish until each has a real answer or is explicitly parked
in **Open questions**:

1. **Vision** — what it is, who it's for, the problem it solves.
2. **Users & roles** — the actors and what each can/can't do.
3. **Glossary** — domain terms, so user and AI mean the same thing.
4. **Core entities** — the main "things" and how they relate (conceptual, not a schema).
5. **Features** — the bulk. Per feature: what it does, rules, edge cases, and acceptance criteria
   ("done when…"). Group by module.
6. **Roadmap / Phases** — the build order as ordered vertical slices, each a working end-to-end
   path defined by a user outcome. References features from section 5; doesn't redefine them.
7. **Screens** — the proposed screens, their contents, and navigation between them.
8. **Look & Feel** — mood and visual direction in broad strokes (adjectives, tone, references,
   color/typography *intent* — never final values).
9. **Non-functional requirements** — privacy/compliance, i18n, performance, accessibility. Easy
   to forget, costly to miss — raise them even if the user doesn't.
10. **Decisions** — for every meaningful choice: what was decided, why, what was discarded. This is
   how you stay lossless.
11. **Technical constraints** — tech already decided (not implementation).
12. **Out of scope** — what you're explicitly *not* building now. Cheap insurance against scope creep.
13. **Open questions** — anything unresolved. Don't fake consensus.

### How to ask well

- **Bring options, not blank stares.** When the user is unsure, propose 2–3 concrete possibilities
  with trade-offs and a recommendation. Defining a product is easier by reacting than by inventing.
- **Surface what they didn't think of.** Privacy, roles/permissions, i18n, and edge cases are the
  usual blind spots. Raise them proactively.
- **Separate decided / why / open.** As you go, sort each point into a clean statement (decided), a
  rationale-with-alternatives (the why), or a parked item (open question).
- **Don't over-ask.** If something has an obvious sensible default, state the default and move on;
  only stop the user for choices that genuinely change the product.

### Advise, don't just transcribe (default behavior)

Don't limit yourself to capturing what the user asks for. The user may not be an expert in this
domain, and is relying on you to catch what they're missing — so don't wait to be asked. Using the
product goal as your yardstick, **proactively propose features that fit *this* phase (the first
version) and deliver a lot of value for little added complexity.** Also flag anything *fundamental*
that a solid v1 would be incomplete without.

Be specific and grounded: each suggestion should name the feature, tie it back to the goal (why it
matters for the problem being solved), and note why it's cheap to add now. Keep it scoped — a
first version, not a wishlist. Resist gold-plating: if an idea is valuable but heavy, or serves a
later phase, name it and park it in **Out of scope** or **Open questions** rather than smuggling it
into v1. The aim is a lean first version that nonetheless doesn't miss anything essential.

### Think in phases (vertical slices)

A flat feature list is all-or-nothing: until everything is built, nothing feels like a product.
Avoid that. Organize the build as an ordered set of **phases**, where each phase is a *vertical
slice* — a thin but complete path that works end to end — defined by a **user outcome**, not by a
technical layer. "The whole database" is not a phase; "a staffer can run a rental end to end" is.
The test: at the end of Phase 1 the user can do one real task from start to finish, so even the
first version feels like a working product — just a smaller one.

Keep the two layers separate. The feature catalog (section 5) stays the stable description of *what
each thing does*; the **Roadmap** (section 6) is the *order of attack* and only references those
features. A feature can appear in several phases at increasing depth — note which slice lands in
which phase, but keep its full definition in the catalog so it's never fragmented. This is also
where your proactive v1 suggestions land: high-value/low-complexity ideas go in Phase 1; valuable-
but-heavy ones get parked in a later phase or Out of scope.

A light nudge that often pays off: let Phase 1 build the app's overall **layout**, with links to
sections that aren't built yet shown with a **"soon"** marker — so the shape of the whole product is
visible from the start. A suggestion to weigh, not a rule.

### Phase to connect, not to isolate

The failure mode of phasing is **feature silos**: each slice built alone, so the cross-feature
interactions — where a product's value often lives — never get wired. The fear is real; guard
against it deliberately:

- **Slice by cross-feature outcome, not by single feature.** A vertical slice is a path to a *user
  outcome*, and a good outcome spans several features on purpose. "Manage a relationship end to end"
  beats "build the contacts feature" — the former forces the connections, the latter invites a silo.
- **Build the integrating surface first.** Find where features converge (a timeline, an activity
  feed, a dashboard) and build it early, so every later feature plugs into it instead of standing alone.
- **Make cross-feature interactions explicit.** Silos form because the connections are never written
  down. Note them on each feature ("touches: timeline, alerts, dashboard") so they get built, not assumed.
- **A feature's "done" includes its connections** — not the feature in isolation. It's done when it
  shows up everywhere it should and feeds what it should, verified.

And design the whole data model (section 4) up front even if you build it incrementally — the shared
model is the backbone that makes cross-feature interaction possible in the first place.

## Writing the document

Use the bundled template at `assets/SPEC_TEMPLATE.md`. Replace `{{PRODUCT_NAME}}` and `{{DATE}}`.
Write all content and headings **in English** (the template is English by design; it's read by
technical AI instances).

**Where to put it:** if the project already has an obvious home for docs (a `docs/` directory, or
similar), place `SPEC.md` there. If there's no obvious documentation location, **create a `docs/`
directory** and put it there rather than dropping it in the project root. Either way, the user can override.

Keep the section conventions intact:

- **Status tracking:** every feature in section 5 carries exactly one status label —
  🔴 `TODO`, 🟡 `WIP`, 🟢 `DONE`, or ⏸️ `BLOCKED` (note why). This is the at-a-glance answer to
  "what's done?". Granularity is **per feature**, not per sub-item — keep it scannable.
- **Acceptance criteria** live *inside* each feature ("Done when…"), not in a separate section.
  A feature is only 🟢 `DONE` when its acceptance criteria are met and verified.
- **The Decisions section is the lossless core.** Whenever a choice is made during the
  conversation, record it there with its rationale and the discarded alternatives — don't let it
  evaporate into the chat.
- Each template section carries an HTML-comment instruction and example. Replace the
  `_To be defined._` placeholders with real content; you may leave the guiding comments or remove
  them once a section is solid.

### Wire it into CLAUDE.md (after creating the spec)

Once `SPEC.md` exists, make it discoverable from the project's `CLAUDE.md` (create that file if the
project doesn't have one) — future AI instances read `CLAUDE.md` first, so the spec should be one
hop away:

- **Add a one-line pointer** to the spec — succinct, just what's there and where, e.g.
  `See [SPEC.md](docs/SPEC.md) for the full product spec and per-feature build status.` One line, no
  more; don't duplicate the spec's content into `CLAUDE.md`.
- **If `CLAUDE.md` has no project description**, add one at the very top: a couple of sentences on
  what the product is and the problem it solves, drawn from the goal you established. If a
  description is already there, leave it as is.
- **Add a standing instruction** next to the pointer so every future AI instance treats the spec as
  a live document it owns — e.g. `Keep SPEC.md up to date: whenever you add, change, or finish a
  feature, update its definition, its phase, and its status. Preventing drift is your job, not the
  user's.` This is what makes the spec stay true over time instead of rotting after day one.

### Keeping it current (maintenance mode)

**Preventing drift is the AI's job, not the user's.** The spec is only useful if it keeps matching
reality, and you can't rely on the developer to remember to update it. So whenever you build, change,
or finish work — in the same session, before you consider the task done — bring the spec back in
sync along all three axes:

- **Features** — if behavior, rules, or edge cases changed, or a new feature appeared, update its
  definition in section 5. The catalog must describe what the product *actually* does.
- **Phases** — if scope moved between phases (something pulled into v1, something pushed later),
  update the Roadmap so the phase plan reflects the real plan.
- **Progress** — update each feature's **status** label (🔴/🟡/🟢/⏸️) the moment it changes. A
  feature is only 🟢 `DONE` once its acceptance criteria are met and verified — not when coding starts.

Also: move resolved items out of **Open questions** into the relevant section (logging the call in
**Decisions**), and bump **Last updated**. Preserve history — don't delete decisions or rationale;
a superseded decision gets a short note, not a deletion. Lossless applies over time, not just on day one.

## Anti-patterns to avoid

- Writing the doc silently without the conversation — the conversation *is* the value.
- Acting as a pure scribe — only recording what's asked and never proposing what a strong v1 is missing.
- Defining features before the product's goal is clear — there's nothing to judge their value against.
- Treating the spec as write-once — letting features, phases, or status drift from reality. Keeping
  it in sync is the AI's responsibility, not something to wait for the user to ask for.
- Two parallel documents (user vs technical) — one layered document instead.
- Dumping DB schemas / code / folder structure into the spec — that's implementation, it belongs in code.
- Recording decisions without the *why* and the discarded options — that breaks lossless.
- Per-sub-item checkboxes everywhere — status is per feature, keep it scannable.
- Faking consensus — if it's unresolved, it goes in Open questions.

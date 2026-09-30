# {{PRODUCT_NAME}} — Product Spec

> Single source of truth for **what this product does** and **how much of it is built**.
> This document is the outcome of an in-depth conversation between the user and the AI
> that defines the product. It is meant to be **lossless**: no decision, rule, or rationale
> from that conversation should be lost here.
>
> **Two readers:**
> - **The user** (non-technical) reads sections 1–8.
> - **Future AI instances** (technical) read the whole document (1–13).
>
> Keep functional behavior in plain language. Capture *decided* tech choices, but not
> low-level implementation (DB schemas, folder structure, code) — that lives in the code.

**Last updated:** {{DATE}}

---

## How to use this document

- Each **feature** in section 5 carries one **status** label.
- The document is edited by hand (or by the AI) as the product is defined and built.

**Status labels:**

| Label | Meaning |
|-------|---------|
| 🔴 `TODO` | Not started |
| 🟡 `WIP` | In progress |
| 🟢 `DONE` | Built and verified |
| ⏸️ `BLOCKED` | Blocked (note why) |

---

## 1. Vision

<!-- 2-3 sentences: what the product is, who it's for, and the problem it solves. -->

_To be defined._

---

## 2. Users & roles

<!--
The actors who use the product and what each can do (permissions).
This is product behavior, not auth implementation.

Example:
| Role     | Description                          | Can do                                       |
|----------|--------------------------------------|----------------------------------------------|
| Staff    | Runs the front desk                  | View bikes, start/end rentals, log maintenance |
| Manager  | Configures the shop                  | Everything staff can + manage users & pricing |
-->

_To be defined._

---

## 3. Glossary

<!--
Domain terms shared by user and AI, so both mean the same thing.

Example:
- **Bike:** a rentable unit in the fleet, identified by a code.
- **Customer:** a person who rents bikes.
- **Rental:** an active or past loan of one bike to one customer for a period.
-->

_To be defined._

---

## 4. Core entities

<!--
The conceptual data model: the main "things" and how they relate.
NOT a database schema — no columns or types. The backbone both readers rely on.

Example:
- **Customer** has many **Rentals**.
- **Bike** has many **Rentals** and many **Maintenance tickets**.
- **Rental** links one **Customer** and one **Bike** for a time period.
-->

_To be defined._

---

## 5. Features

<!--
The core of the document. Group features by module.
For each feature, in plain language: what it does, its rules, and edge cases.
Add a status label, and acceptance criteria ("done when…").

Example:

### Customer management

#### Search customers — 🟢 `DONE`
Search customers by name or phone number. Partial matches allowed. Results show
name, phone, and number of active rentals.
- Rules: search is case-insensitive; min 2 characters.
- Edge cases: no results → show empty state with a "create customer" hint.
- Done when: a query returns matching customers ranked by relevance.

#### Start a rental — 🟡 `WIP`
Loan an available bike to a customer (bike, start time, expected return).
- Done when: a saved rental appears in the customer's history and the bike shows as unavailable.
-->

### <Module>

#### <Feature name> — 🔴 `TODO`
_Description, rules, edge cases._
- Done when: _acceptance criteria._

---

## 6. Roadmap / Phases

<!--
The build order: an ordered list of phases that turn the feature catalog (section 5) into
something shippable step by step. Each phase is a VERTICAL SLICE — a thin but complete path
that actually works end to end — defined by a USER OUTCOME, not by a technical layer.
Phases REFERENCE features; they don't redefine them. A feature can appear across phases at
increasing depth — note which slice belongs to which phase, keep the full definition in §5.

Rule of thumb: at the end of Phase 1, someone can do one real task from start to finish.

Example:

### Phase 1 — A staffer can run a rental end to end
Outcome: front-desk staff can register a customer, start a rental, and end it.
- Search customers (name only) — see §5
- Start a rental (pick available bike, set return time) — see §5
- End a rental — see §5
- Bike list with availability — see §5

### Phase 2 — Day-to-day operations without spreadsheets
Outcome: the shop is fully run from the app.
- Maintenance tickets — see §5
- Dashboard (bikes due back, active rentals) — see §5
- Customer history — see §5

### Phase 3 — Polish & scale
- Fuzzy search + filters — see §5
- Multi-language UI — see §9
-->

_To be defined._

---

## 7. Screens

<!--
The proposed screens, what each contains, and how the user navigates between them.
Bridges features (what it does) to UI (what the user sees).

Example:
- **Dashboard** — overview: active rentals, bikes due back, key metrics. Entry point after login.
- **Customer list** — searchable/filterable table. → opens **Customer detail**.
- **Customer detail** — profile, current rental, rental history.
- Navigation: persistent left sidebar (Dashboard · Customers · Bikes · Rentals).
-->

_To be defined._

---

## 8. Look & Feel

<!--
The intended mood and visual direction — broad strokes only, not a final design.
Adjectives, tone, references/inspiration, and at most an intent for color/typography
(no exact values).

Example:
- Mood: friendly and energetic, but clean and quick to scan. Approachable, not corporate.
- References: Linear, Stripe Dashboard.
- Color intent: bright neutral base, a single lively accent for primary actions; restrained.
- Typography intent: a clear, modern sans for UI and data tables.
-->

_To be defined._

---

## 9. Non-functional requirements

<!--
Cross-cutting requirements that are easy to forget and costly to miss.

Example:
- **Privacy / compliance:** handles customer personal data → GDPR. Define retention and access.
- **Internationalization:** UI must support multiple languages. Define which.
- **Performance:** customer search feels instant (<300ms perceived).
- **Accessibility:** keyboard-navigable; meets WCAG AA for contrast.
-->

_To be defined._

---

## 10. Decisions

<!--
The lossless log. For each meaningful decision: what was decided, why, and which
alternatives were discarded (and why). This is what is normally lost first.

Example:

### Single spec document instead of separate user/technical docs
- **Decided:** one document, layered by information type.
- **Why:** two documents become two sources of truth that drift apart.
- **Discarded:** separate non-technical and technical specs (sync cost too high).
-->

_To be defined._

---

## 11. Technical constraints

<!--
Tech that is ALREADY decided — not implementation detail.

Example:
- Built on Next.js.
- Data in Postgres via Prisma.
- i18n via next-intl.
-->

_To be defined._

---

## 12. Out of scope

<!--
What we are explicitly NOT building (now). Prevents scope creep.

Example:
- No customer-facing self-service booking in v1 — staff-operated tool only.
- No online payments/invoicing.
-->

_To be defined._

---

## 13. Open questions

<!--
Unresolved items. Don't pretend something is decided when it isn't.

Example:
- Which languages must the UI support at launch?
- Can a customer have more than one active rental at the same time?
-->

_To be defined._

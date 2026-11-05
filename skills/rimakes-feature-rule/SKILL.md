---
name: rimakes-feature-rule
description: Write or update the rule file for one feature, `.claude/rules/<feature>.md`, which Claude Code loads only when it touches that feature. The rule holds what the code cannot say and what stays true after a rewrite. That is why the feature exists, what its business words mean, its business rules, and the traps agents already fell into. Never file names or code details. Use when the user says "create a feature rule", "document this feature", "add documentation for the X feature", "add a trap to the X rule", or invokes /rimakes-feature-rule.
argument-hint: "[<feature> [what to add]]"
---

# Feature Rule

Write one small rule file per feature: `.claude/rules/<feature>.md`. Claude
Code loads it only when it touches that feature's files.

The one idea: the rule holds only what the code cannot say, and what stays
true when the code changes. Code changes often. A rule that describes code
goes stale, and then it misleads.

The request: $ARGUMENTS

It names the feature, and maybe what to add. If it is empty, take the feature
from the conversation. If you cannot, ask the user which feature.

## What goes in

Four parts, in this order:

| Part      | What it holds                                                                           | Size             |
| --------- | --------------------------------------------------------------------------------------- | ---------------- |
| **Why**   | The first lines, with no heading. Who uses the feature, for what job, what it prevents. | 1 to 3 sentences |
| **Words** | What each business word means.                                                          | 1 line per word  |
| **Rules** | Business rules that hold however the code is written.                                   | 1 line each      |
| **Traps** | A mistake an agent already made here, and what is right.                                | 1 line each      |

Every line must pass two tests:

1. **Can an agent learn this by reading the code?** If yes, delete the line.
2. **Would it still be true after a full rewrite of the feature?** If no,
   delete the line.

## Steps

### 1. Find the feature and its rule

```bash
ls .claude/rules/
find . -type d -name "<feature>" -not -path "*/node_modules/*"
```

- The rule exists: go to **Update a rule**.
- The user only wants to add a trap: go to **Add a trap**.
- The project keeps its path rules in another place: write the file there.

### 2. Collect the facts

Take them from these, in this order:

1. What the user said in the conversation.
2. The project's docs.
3. The user. Ask everything in one short message.

Read the feature's code only to list its business words: the names of its
models, its states, the words on its screens. Do not guess what a word means
or why a rule exists. Ask.

### 3. Write the file

Copy the template below.

- Leave out **Words** or **Rules** when there is nothing to say.
- Always keep **Traps**, with its comment and no trap.
- Say each fact once. A line in Words is not said again in Rules.
- Add the reason to a rule when you know it. An agent that knows the reason
  can handle a case the rule does not cover.

### 4. Run the two tests

Go line by line. Delete every line that fails one.

### 5. Check the paths

Every folder that `find` showed in step 1 must match a glob in `paths`.
`**/<feature>/**` covers the feature's folder, its routes and its tests. Add
a second path when part of the feature lives under another name.

### 6. Report

Show the whole file in the chat and give its path as a clickable link. Do not
commit it unless the user asks.

## Update a rule

Read the whole file first. Then:

- Put what is there into the four parts. Move the facts. Do not add one the
  user did not give.
- Add what the user asked for, and only that.
- Run the two tests on the new lines.

## Add a trap

A trap goes in only when **both** are true:

1. **An agent already fell into it.** Not "an agent might".
2. **Code, types, lint or a test cannot prevent it.** If one of them can,
   propose that change instead, and add no trap.

Write one line: what went wrong → what is right. Describe what the agent
did, not a file. Delete a trap when the code later makes it impossible.

## Template

```md
---
paths:
  - "**/<feature>/**"
---

# <Feature>

<Why. Who uses it, for what job, what goes wrong without it.>

## Words

- **<Word>**: <what it means to the business>.

## Rules

- <A business rule. Its reason, when you know it.>

## Traps

<!--
Add a trap only after an agent fell into it, and only if code, types, lint or a
test cannot prevent it. One line each: what went wrong → what is right.
-->
```

## Example

A made-up feature, with one trap to show the form:

```md
---
paths:
  - "**/invoices/**"
---

# Invoices

Invoices exist so each customer has a legal record of what we charged, and
the accountant can close the month without asking the team.

## Words

- **Invoice**: the legal record of one charge to one customer.
- **Draft**: an invoice with no number yet. It can still change.
- **Credit note**: a new document that cancels all or part of an invoice.

## Rules

- An issued invoice never changes. The law treats it as final, so a mistake
  is fixed with a credit note.
- Invoice numbers have no gaps. The tax office reads a gap as a hidden
  invoice.

## Traps

- Fixed a wrong issued invoice by hand in the database → issue a credit note.
```

## Guardrails

- Never name files, functions, folders, tables or libraries. They change.
- Never write how-to steps, status or todos.
- Never invent a fact, a reason or a trap.
- Never add a trap that code, types, lint or a test can prevent.
- Never write a rule for a feature that has nothing to say. Tell the user
  instead.
- Keep the body under about 30 lines.

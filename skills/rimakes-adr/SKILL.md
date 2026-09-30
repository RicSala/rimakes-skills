---
name: rimakes-adr
description: Write an Architecture Decision Record (ADR) in docs/ADRs/ for a decision that qualifies. Checks the decision against the criteria first, and says so when it does not need an ADR.
argument-hint: "<the decision>"
disable-model-invocation: true
---

# ADR

Record one decision as an ADR in `docs/ADRs/`, but only if it qualifies.
Most decisions do not.

The decision: $ARGUMENTS

If no decision is given, take the last decision made in the conversation.
Say it back in one sentence. If you cannot, ask the user what the decision
is.

## The criteria

A decision gets an ADR only when **both** are true:

1. **A good developer could have chosen differently.** There was a real
   second option, and it was rejected for a reason.
2. **Undoing it later is costly.** It touches many files, the data model,
   security, or how every feature is built.

The quick test: "In six months, would a new developer or an agent undo this
because they do not know why we did it?" If yes, and undoing it would hurt,
it qualifies.

**Signs that it qualifies**

- It goes against a library's default way.
- It picks one library or provider over another.
- It sets a rule that every feature must follow.
- It took a long discussion to decide.
- It has a condition to decide again, such as "remove this once the fix
  ships".

**Signs that it does not**

- The code or a lint rule already shows the decision.
- It affects one file or one feature.
- It can be changed in an hour.
- There was no real second option.

**Where a decision that does not qualify goes**

- Why one change was made: the commit message.
- A rule to follow from now on: one line in the repo's instruction file.
- A known bug or trap: one line where the repo lists those.

The instruction file says the rule. The ADR says the reason, and what was
rejected.

## Steps

### 1. Check the criteria

Answer both criteria for this decision, with the reason for each answer.

- **Both true:** go on.
- **One or none true:** tell the user it does not qualify and why, say where
  it should go instead, and stop. Write the ADR only if the user then says
  they still want it; in that case the Meta section says so.

### 2. Collect the facts

- Read the code the decision is about. Do not describe it from memory.
- List the options that were really considered. Take them from the
  conversation, the code and its history. Do not make up options to fill
  the section. If you do not know what was rejected or why, ask the user.
- List `docs/ADRs/`. If an ADR already covers this decision, do not write a
  second one: tell the user, and see step 4 if the decision changed.

### 3. Write the file

- Path: `docs/ADRs/NNNN-short-title.md`. `NNNN` is the next free number,
  four digits, starting at `0001`. Create the folder if it is missing.
- The title states the decision, not the topic: "Use cases are the only
  guard", not "Authorization".
- Start from `template.md`, next to this file. Keep every section. Delete
  the guidance in angle brackets.
- Date: today (`date +%F`).
- Status: `Accepted` if the decision is made, `Proposed` if the user has not
  decided yet.

### 4. When a decision replaces an older one

An accepted ADR is not rewritten. Write a new ADR, and in the old one change
only the status line to `Superseded by [NNNN](NNNN-short-title.md)`. The new
ADR says in its Context which ADR it replaces and what changed.

### 5. Finish

- Show the ADR in the chat and give a link to the file.
- If the decision sets a rule to follow from now on, propose the one line
  for the repo's instruction file. Ask before adding it.
- Do not commit.

## How to write it

- Plain, short sentences. Facts, not opinions about the facts.
- The decision comes first in its section, in the present tense: "Use cases
  check the actor. There is no row-level security."
- Name files and functions with their paths so a reader can find them.
- For each rejected option, give the real reason it was rejected, even when
  the reason is "we did not have time".
- Write down the costs of the choice. An ADR with no downside is not
  finished.
- One page. If it needs more, the decision is two decisions.

# NNNN. <The decision, as one short sentence>

- Status: <Proposed | Accepted | Superseded by [NNNN](NNNN-short-title.md)>
- Date: <YYYY-MM-DD>

## Context

<The situation that forced a choice. What we needed, what limited us, and
what was true in the code at the time. Facts only. If this replaces an
older ADR, name it and say what changed.>

## Decision

<What we do, in one or two sentences, in the present tense. Then the
details a developer needs to follow it: where it lives in the code, and the
rule to keep.>

## Options considered

### <Option A> (chosen)

<What it is. Why we chose it.>

### <Option B>

<What it is. Why someone would pick it. The real reason we rejected it.>

## Consequences

**Easier**

- <What this makes simpler or safer.>

**Harder**

- <What this costs. What we now have to keep doing, or can no longer do.>

## Revisit when

<The condition that would make us decide again: a library fix ships, the
data grows past a size, a second provider is added. Write "No condition
known" if there is none.>

## Meta

Why this decision has an ADR. A decision qualifies only when both criteria
are true.

| Criterion                                         | True? | Reasoning                                                        |
| ------------------------------------------------- | ----- | ---------------------------------------------------------------- |
| A good developer could have chosen differently    | <yes> | <The real second option, and why someone would pick it.>         |
| Undoing it later is costly                        | <yes> | <What would have to change: which files, data, rules, security.> |

- **Who would undo this in six months, and why:** <The person or agent, and
  the wrong "fix" they would make without this record.>
- **Signs present:** <Any that apply: goes against a library's default;
  picks one library or provider over another; sets a rule for every
  feature; took a long discussion; has a condition to decide again.>
- **Written at the user's request without qualifying:** <Only if so. Say
  which criterion was not met. Delete this line otherwise.>

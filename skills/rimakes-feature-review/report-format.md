# How every reviewer reports

Return only this. No story of how you worked.

```
## Findings

### <short title that states the defect>
- Severity: high | medium | low
- Where: path/to/file.ext:line
- What is wrong: one or two sentences.
- How it fails: a concrete input, state or sequence, and the wrong result.
- Suggested fix: the smallest change that solves it, with a short snippet
  when it helps.
- Evidence: what you saw that proves it (code read, command output, doc
  link, steps in the browser, screenshot path).
- Confidence: sure | likely | unsure

## Not checked
What you could not check and what you would need to check it.

## Needs follow-up
Only if you wanted to launch a subagent and could not: the exact task each
one would have received.
```

## Severity

- **high**: wrong data, lost data, a person seeing or changing what they
  should not, a main flow that does not work.
- **medium**: a secondary flow that fails, a wrong result in an edge case
  people will reach, a gap that will let a high bug in unnoticed.
- **low**: works, but costs the next person time.

## Rules

- One finding per root cause. If it shows up in five places, list the five
  places under one finding.
- No finding without a location and a way it fails. "Could be a problem" is
  not a finding; settle it or leave it out.
- Say `unsure` when you are. A wrong finding stated with confidence costs
  more than a missed one.
- Stay on your topic. If you trip over something that belongs to another
  reviewer, give it one line under **Not checked** and move on.
- If you find nothing, say "No findings" and list what you did check.

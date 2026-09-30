# Rules every reviewer follows

## How to work

1. See the change with the commands in the brief. Read each changed file
   whole, not only the changed lines.
2. The diff is where you start, not where you stop. Follow the data from
   each entry point in, through what the change touches, and out. Read the
   callers of every changed function and the code it calls. Many bugs sit
   where new code meets old code.
3. For each suspect, read its callers and the code it calls before you
   report it. Most false alarms die here: the check you thought was missing
   is one level up.
4. Prove it when you cheaply can: run an existing test, or write a scratch
   script or test **outside the repo**. Other reviewers run at the same
   time, and tests that use a shared database or service can clash. A test
   that fails in a way that does not match your scenario is not proof: run
   it again alone, or mark the finding `read it`.
5. A third-party library is assumed to do what its docs say for the
   installed version. What the code assumes beyond that is fair game: check
   the docs.
6. If the user gave a focus, go deepest there, then cover the rest of your
   angle.

## Finding ids

Number your findings with your angle's prefix: `LOG-1`, `LOG-2`, and so on.

| Angle           | Prefix |
| --------------- | ------ |
| `logic`         | `LOG`  |
| `hostile-input` | `INP`  |
| `access`        | `ACC`  |
| `timing`        | `TIM`  |
| `failure`       | `FAIL` |
| `rollout`       | `ROLL` |

## Your file

Write it in this shape:

```
# <Angle>: adversarial review

Target: <what was reviewed>
Model: <the model you run on, by name>
Checked: <what you went through: entry points, files, commands you ran>

## Findings

### <ID>. <short title that states the defect>
- Severity: high | medium | low
- Where: path/to/file.ext:line
- What is wrong: one or two sentences.
- How it fails: the input, state or sequence, and the wrong result.
- Proof: ran it | read it — what you saw (the command and its output, or
  the lines that show it).
- Suggested fix: the smallest change that solves it, with a short snippet
  when it helps.
- Confidence: sure | likely | unsure

## Not checked
What you could not check and what you would need to check it.

## Needs follow-up
Only if you wanted to launch a subagent and could not: the exact task each
one would have received.
```

Show the code that matters as short snippets, so the reader does not have
to open other files.

## Your reply

After writing the file, reply with only this, one block per finding:

```
<ID> | <severity> | <path:line> | <title>
  What is wrong: ...
  How it fails: ...
  Proof: ran it | read it
```

Add a `Needs follow-up` part if your file has one. If you found nothing,
reply "No findings" and one line on what you checked.

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
  not a finding: settle it or leave it out.
- Say `unsure` when you are. A wrong finding stated with confidence costs
  more than a missed one.
- Stay on your angle. If you trip over something that belongs to another
  angle, give it one line under **Not checked** and move on.
- If you find nothing, write "No findings" and list what you checked.

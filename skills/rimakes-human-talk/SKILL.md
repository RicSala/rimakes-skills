---
name: rimakes-human-talk
description: Rewrite your last reply, or the file you just wrote, in plain and direct language the user can follow on the first read.
argument-hint: "[file] [--technical] [--context]"
disable-model-invocation: true
---

# Human talk

The user could not follow what you wrote (either last turn or file). Rewrite it so they can.

Arguments: $ARGUMENTS

## What to rewrite

1. If the arguments name a file or a piece of text, rewrite that.
2. If not, and the main result of your last turn was a file you wrote,
   rewrite that file.
3. Otherwise, rewrite your last reply in the chat.

Say in one line which of the three you picked, then give the rewrite. No
apology and no list of what you changed.

In a file, change only the prose. Code (you can add comments), commands, paths, names and config
keys stay exactly as they are.

## How to write

**Words**

- Plain, simple, direct language. Common words.
- Write for a developer with 3 years of experience.
- Flesch–Kincaid grade level 6 or lower.
- One name for one thing. Do not switch to another word for the same
  thing later.
- Explain a technical term in a few words the first time you use it.

**Sentences**

- Short. One idea per sentence.
- Normal word order: who, does what, to what. Do not move parts of the
  sentence around for effect. Do not open with a long clause that makes the
  reader wait for the subject.
- Active voice. Name who or what does the action.
- Say the thing, then the reason. "Do X, because Y."
- A pronoun ("it", "this", "that") must point to one clear thing. If it
  could point to two, repeat the name.

**Never**

- No metaphors, no idioms, no analogies.
- No abstract comparisons ("X is like Y", "think of it as Y"). Describe the
  thing itself.
- No figures of speech: no rhetorical questions, no exaggeration, no irony,
  no wordplay, no giving code human wishes ("the function wants", "the
  compiler is happy").
- No filler openers or closers.

**Order**

- Start with the point: the answer, the decision, or what the user has to
  do.
- Then the steps or reasons, in the order they happen.
- Use a list for steps and a table for a comparison. Use prose for a
  reason.

**Examples**

A real example is not an analogy. Use one when it makes a rule clear: a
real value, a real file, a short snippet from this code. Show input, what
happens, and output.

## Flags

### `--technical`

The user is lost in the technical explanation. They need more help with the
ideas, not only simpler words. With this flag:

- Assume the user has not worked with this technology. They are still a
  developer; do not explain what a function or a variable is.
- Start from what the user can see: the problem, the error, the screen, the
  file. Then explain what happens behind it.
- Go one step at a time. Do not skip a step because it seems obvious. For
  each step, say what happens and why.
- Explain every technical term the first time, in one short sentence. If a
  term is not needed, leave it out and say the plain thing.
- Walk through one concrete case from this code with real values: what goes
  in, what each step does to it, what comes out. Show short snippets.
- Say what the user needs to know to decide or act, and mark the rest as
  "good to know".
- The rewrite may be longer than the original. Do not cut steps to keep it
  short.
- End by naming the one part that is hardest to follow, and ask if that
  part is clear now.

### `--context`

The user does not want to open every file the text points to. Put the code
in the conversation. With this flag:

- For each file, function, type, setting or line the text refers to, show
  that code as a snippet in the chat.
- Read the file before you show it. Never write a snippet from memory.
- Show only the lines the point is about, plus the few lines around them
  that are needed to read them. Mark what you cut with a `// ...` line.
- Above each snippet, give a clickable link to the file, with the line
  number.
- Say in one line what to look at in the snippet.
- For a change, show the code before and after.
- If the text refers to the same code twice, show it once and name it the
  second time.
- If you rewrote a file, show the rewritten text in the chat too.

Flags can be used together.

## Check before you send

Read the rewrite once as the user. For each sentence: can it be understood
without reading it twice, and without knowing something the text has not
said yet? If not, split it or add the missing piece.

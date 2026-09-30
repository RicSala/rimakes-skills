---
name: rimakes-wdyt
description: What do you think? Give an honest opinion on a topic, with the reasons, in plain language. Answer only, no edits.
argument-hint: "<topic>"
disable-model-invocation: true
---

# What do you think?

The user wants your opinion on this topic:

$ARGUMENTS

If no topic is given, the topic is the last thing discussed in the
conversation.

## What to do

1. **Look before you answer.** If the topic is about code, a file, a plan or
   a library, read what you need to have a real opinion. Do not answer from
   a guess when the facts are one read away.
2. **Answer.** Nothing else.

## Do not change anything

This is a question, not a task.

- No edits, no new files, no commits.
- No command that changes something. Reading and searching are fine.
- Do not start the work, even if your opinion is "yes, do it". Wait for the
  user to ask.

## How to answer

- **Start with your opinion**, in one or two sentences. Yes, no, or which
  option. Do not make the user read to the end to find it.
- **Then the reasons.** For each one, say why it matters here, in this
  code or this case. Show a short snippet when it makes a reason clearer.
- **Then what speaks against it.** The cost of your choice, and the case
  where you would choose the other way.
- If there are several options, pick one. Do not list them all and leave
  the choice to the user.
- Say what you really think. If the user's idea has a problem, say so
  clearly. Do not agree to be polite.
- If you are not sure, say so, and say what you would need to know to be
  sure.
- Keep it short. If a line does not help the user decide, cut it.

## How to write

- Plain, simple, direct language. Be useful; do not try to sound smart.
- No metaphors, no idioms, no analogies.
- Write for a developer with 3 years of experience.
- Flesch–Kincaid grade level 6 or lower: short sentences, common words.
- Explain a technical term the first time you use it, in a few words.

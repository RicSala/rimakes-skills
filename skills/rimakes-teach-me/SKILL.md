---
name: rimakes-teach-me
description: Teach the user a topic with a visual, interactive Artifact page (pictures, step-throughs, things to try, questions to check yourself), then save its link in the repo's learning/ARTIFACTS.md. Uses the topic the user passes, or the one being discussed. Use when the user says "teach me", "help me understand", "explain this visually", or asks for an explainer page.
argument-hint: "[topic | file | folder]"
---

# Teach me

Build one Artifact page that teaches one topic. The page explains with
pictures, lets the user try things, and checks what they understood. Then
save its link where the user can find it again.

Arguments: $ARGUMENTS

## 1. Pick the topic

1. If the arguments name a topic, teach that.
2. If they name a file or a folder, the topic is what that code does and
   why.
3. Otherwise, take the topic being discussed: what the user asked about
   last, or the part they said they did not follow.

If two topics fit, ask which one (with AskUserQuestion when available). Say
in one line which topic you picked.

## 2. Find the gap

From the conversation, note what the user already knows and where they got
lost. Then write 3 to 6 goals, each as "After this page, you can ...". Every
section of the page serves a goal. Leave out what serves none.

When the topic comes from the user's code, teach with that code: real files,
real values, real snippets. Read a file before you quote it. Never write a
snippet from memory. When the topic is a library, check its docs for the
installed version before you explain how it works.

## 3. Plan the page

In this order:

1. **The point.** The title, then two or three sentences: what the thing is
   and why it matters to the user. Someone who reads only this knows the
   answer.
2. **One section per idea**, in the order the ideas build on each other.
   Each has a heading that states the idea, a few short paragraphs, then a
   picture, a widget, or both.
3. **Check yourself**: a quiz with 3 to 5 questions, about one goal each.
4. **Where this is in your code**: paths with line numbers, one line each.
   Only when the topic comes from the user's repo.
5. **Good to know**: things that are true but not needed to act. Short.

**Pictures.** Use one when the idea has a shape: parts that talk to each
other, a flow, layers, a timeline. Mermaid renders on its own in an
artifact. Load the `artifact-diagramming` skill before you draw an SVG.

**Widgets.** Use one when doing something teaches more than reading about
it: seeing steps in order, moving a slider and watching the result,
comparing two versions, checking yourself. Not every section needs one. A
widget that repeats the text adds nothing.

For each widget, first ask: what does the reader do, and what do they see
change? Then pick the shape. A stock widget from
[widgets/README.md](widgets/README.md) is right when the idea has exactly
its shape. Otherwise write a **freestyle** widget for the case: a drawing of
the thing itself (the cache entry, the queue, the tree, the screen and the
state behind it) that the reader can act on. A widget shaped for the case
teaches better than a generic one, so plan one or two freestyle widgets per
page, not zero. `widgets/README.md` ("Freestyle") has the contract and
`widgets/freestyle.html` an example.

| The idea is...                                   | Widget           |
| ------------------------------------------------ | ---------------- |
| Something with a shape of its own                | `freestyle`      |
| A flow between parts, one step at a time         | `stepper`        |
| What a piece of code does, line by line          | `code-tracer`    |
| A number that depends on sliders                 | `explorer`       |
| A value, key or request that depends on choices  | `chooser`        |
| Two ways to do the same thing                    | `compare`        |
| Things that happen at the same time              | `lanes`          |
| The order of steps                               | `order`          |
| What each part of a snippet is for               | `annotated-code` |
| Did the reader get it?                           | `quiz`           |

## 4. Build it

- Follow the Artifact tool's own rules for a new page first: its quickstart
  and its design guidance. This skill adds the lesson plan and the widgets;
  it does not replace those rules.
- Start from `widgets/template.html`. Paste `widgets/base.css` into its
  `<style>`. Change the token values to fit the topic if that helps, and
  keep the token names: every widget reads them.
- Keep the template's page parts: the highlight.js script tag, the
  `<nav class="toc">` and the `PAGE: JS` block. They give the page its
  "On this page" list, the theme switch and code highlighting. Every
  `section.lesson` needs an `id` and an `h2`, or it is missing from the
  list.
- Code in prose goes in `<pre><code class="language-ts">` (or another
  highlight.js language name). Code inside a widget's JSON takes a `lang`
  field; the default is `typescript`.
- For each stock widget, copy the three marked blocks (CSS, HTML, JS) from
  its sample file, as `widgets/README.md` explains. Change the JSON, not
  the JS.
- For each freestyle widget, write the three blocks yourself, following the
  contract in `widgets/README.md`. Save it in `widgets/` only if it would
  serve other topics with different JSON.
- Write the page in the session's scratchpad directory unless the user
  names a place.
- Before publishing, parse every widget's JSON and syntax-check the page
  script once (a short `node -e` over the file). Then take the one look the
  Artifact rules allow, fix what it shows, and publish. The `description` is
  one sentence on what the page teaches.

## 5. Save the link

Keep a list of these pages in the repo, so the user can find them again.

1. Find the folder where this repo keeps its docs. Do not assume a name:
   find it from the repo's instruction files and from where its documents
   already live. If there is no such folder, ask the user. If the session
   is not inside a repo, skip this step and say so.
2. Open `<docs folder>/learning/ARTIFACTS.md`. Create the folder and the
   file if they do not exist. The user asked for this file, so read and edit
   it even when the repo's instructions say not to read its docs. Read
   nothing else there.
3. Add one line at the top of the list, newest first:

   ```md
   - [<page title>](<artifact url>) · <YYYY-MM-DD> · <what the page teaches, in one sentence>
   ```

   If the page was republished to a link that is already in the list,
   update that line instead of adding another.

A new file starts with:

```md
# Learning artifacts

Pages made to teach a topic, newest first.
```

Do not commit the file.

## 6. Hand it over

In the chat:

- The link to the page.
- One line per section: what it teaches.
- A clickable link to `ARTIFACTS.md`.
- Ask which part is still unclear.

When the user asks for more, change or add a section and republish the same
file, so the link stays the same. Then update its line in `ARTIFACTS.md`.

## How to write

These rules apply to the page and to your chat reply.

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

## How to teach

- Assume the user has not worked with this technology. They are still a
  developer; do not explain what a function or a variable is.
- Start from what the user can see: the problem, the error, the screen, the
  file. Then explain what happens behind it.
- Go one step at a time. Do not skip a step because it seems obvious. For
  each step, say what happens and why.
- Explain every technical term the first time, in one short sentence. If a
  term is not needed, leave it out and say the plain thing.
- Walk through one concrete case with real values: what goes in, what each
  step does to it, what comes out.
- Put what the user needs to know to decide or act in the sections. Put the
  rest in "Good to know".

# Widgets

Interactive parts to copy into a teach-me page. Each sample file runs on its
own: open it in a browser to see the widget work.

## Pick a widget

| Widget           | File                  | Shows                                                       | Use it for                                                                |
| ---------------- | --------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------- |
| `freestyle`      | `freestyle.html`      | Whatever the idea needs; you write it for the case          | The first choice when the idea has a shape of its own. See below.         |
| `stepper`        | `stepper.html`        | Who sends what to whom, one step at a time                  | Requests, handshakes, event flows                                         |
| `code-tracer`    | `code-tracer.html`    | Code running line by line, with the value of each variable  | A function whose result is not clear from reading it                      |
| `explorer`       | `explorer.html`       | How a result changes when you move a slider                 | Numbers that depend on settings: retries, limits, prices, cache times     |
| `chooser`        | `chooser.html`        | A result that depends on a few choices, shown as a table    | Which value, key or request you get for each combination of settings      |
| `compare`        | `compare.html`        | Two or more versions of the same code, one tab each         | Before and after, wrong and right, option A and option B                  |
| `lanes`          | `lanes.html`          | Things happening at the same time in different places       | Race conditions, parallel requests, events that arrive out of order       |
| `quiz`           | `quiz.html`           | Questions with an explanation for every answer              | The "Check yourself" section, or one question after a hard idea           |
| `order`          | `order.html`          | Steps the reader puts in the right order                    | A procedure where the order matters                                       |
| `annotated-code` | `annotated-code.html` | A snippet with marked parts; tap one to read about it       | A short snippet with 3 to 6 parts worth explaining                        |

A stock widget is a fit when the idea has exactly its shape. When a stock
widget would only approximate the idea, write a freestyle one instead. A
widget shaped for the case teaches better than a generic one, so freestyle
is a normal choice, not a last resort: expect one or two per page.

## What the template gives every page

`template.html` carries three page parts. Keep them; widgets rely on none
of them, but readers do.

- **On this page.** A `<nav class="toc">` that the page script fills with
  one link per `section.lesson[id]` that has an `h2`. It is a bar at the top
  on narrow screens and a fixed sidebar to the left of the reading column
  from 78rem. The link of the section in view is marked. Every section
  needs an `id`, or it is missing from the list.
- **Theme switch.** The `Theme: system / light / dark` button in that nav
  sets `data-theme` on the root and remembers the choice in `localStorage`
  (wrapped in try/catch). `base.css` already styles both themes.
- **Code highlighting.** A pinned highlight.js script from cdnjs
  (`11.11.1`, 36 languages, `typescript` included). The page script
  highlights every `<pre><code class="language-…">` in prose. Widgets that
  show code call `hl(text, lang)`, defined in their own JS block; when the
  script did not load, `hl` falls back to escaped plain text. Colors come
  from the `--hl-*` tokens in `base.css`, so highlighted code reads in both
  themes.

## How to add one to a page

Each sample has three marked blocks:

```
/* WIDGET <name>: CSS */   ...   /* /WIDGET <name>: CSS */
<!-- WIDGET <name>: HTML -->   ...   <!-- /WIDGET <name>: HTML -->
/* WIDGET <name>: JS */    ...   /* /WIDGET <name>: JS */
```

1. Copy the CSS block into the page's `<style>`, after `base.css`. Once per
   widget type.
2. Copy the JS block into the page's `<script>` at the end, after the
   `PAGE: JS` block. Once per widget type. It finds every copy of that
   widget on the page.
3. Copy the HTML block where the widget goes, as many times as you need. In
   each copy, change the label, the title and the JSON.

To change what a widget shows, change its JSON, not its JS. The exceptions
are `explorer` and `chooser`: a new topic needs a new function in their
`MODELS`.

The lines above the CSS block (doctype, meta tags, the links and the
highlight.js script) are there only so the sample opens on its own. Do not
copy them; the template already has the script.

## The data of each widget

In every text field, text inside backticks becomes inline code. All other
text is escaped, so do not put HTML in the JSON.

Widgets that show code take an optional `lang` (a highlight.js language
name: `typescript`, `javascript`, `json`, `bash`, `sql`, `xml` for HTML,
`css`, `yaml`, `python`, ...). The default is `typescript`.

- **stepper**: `actors`: names. `steps`: `{ from, to, label, text }`, where
  `from` and `to` are actor indexes. `from` equal to `to` is a step inside
  one actor. Keep `label` to about four words and put the explanation in
  `text`.
- **code-tracer**: `code`, optional `lang`, optional `call`, and `steps`:
  `{ line, vars, returns?, note }`. `line` starts at 1. `vars` maps each
  name to its value as text. A name missing from a step shows as "not in
  scope". Values that changed since the step before are highlighted.
- **explorer**: `model` names a function in `MODELS` inside the JS block.
  `inputs`: `{ id, label, min, max, step, value, unit }`. Optional `note`.
  A model gets `{ [id]: number }` and returns
  `{ rows: [{ label, value, text }], summary }`. All bars share one scale.
- **chooser**: `model` names a function in `MODELS` inside the JS block.
  `choices`: `{ id, label, options: [{ value, label }] }`; the first option
  starts selected. Optional `note`. A model gets `{ [id]: value }` and
  returns `{ rows: [{ label, value, code? }], summary, verdict? }`, with
  `verdict` `"good"`, `"bad"` or `""`.
- **compare**: optional `lang`, and `options`:
  `{ label, verdict, verdictText, code, lang?, mark?, points }`. `verdict`
  is `"good"`, `"bad"` or `""`. `mark` lists line numbers to highlight.
- **lanes**: `lanes`: names. `events`: `{ t, lane, text, bad? }`, where `t`
  is the moment (1, 2, 3, ...) and `lane` an index. Optional `outcome`,
  shown fully at the last moment.
- **quiz**: optional `lang`, and `questions`:
  `{ question, code?, lang?, options, answer, explain }`. `answer` is the
  index of the right option. `explain` has one line per option, saying why
  it is right or wrong.
- **order**: `items` in the right order; the widget shuffles them. Optional
  `explain`, shown when the order is right.
- **annotated-code**: `code`, optional `lang`, and `notes`:
  `{ match, text }`. `match` is exact text from the code; its first
  occurrence gets marked. Notes are numbered in the order they appear in the
  code. A `match` that is not found is left out, with a warning in the
  console.
- **freestyle**: whatever the widget needs. Put every value the reader sees
  in the JSON, so the JS stays about behavior.

## Rules every widget follows

- Colors and fonts come only from the tokens in `base.css`. To change the
  look, change the tokens.
- The root is a `.widget` with `data-widget="<name>"`, and the data sits
  inside it in `<script type="application/json" class="w-data">`. The JS
  builds the rest.
- Class names start with the widget's prefix (`st-`, `ct-`, `ex-`, `cmp-`,
  `ln-`, `qz-`, `or-`, `ac-`, `ch-`, and one of its own for a freestyle
  widget), so widgets on one page do not clash.
- Complete at rest: on load, the widget shows its first step with real
  content, never an empty box.
- Real buttons, keyboard access, and `aria-live` on the text that changes.
- Works at phone width: a wide part scrolls inside the widget, never the
  page.
- No library beyond the page's highlight.js, no network calls, no storage.

## Freestyle: writing a widget for the case

Use a freestyle widget when the idea has a shape none of the stock widgets
draw: a data structure that changes as you act on it, a screen and the
state behind it side by side, a small simulation, a picture the reader can
poke. `freestyle.html` is one example, a query-cache board; copy its shape,
not its content.

How to build one:

1. **Say what the reader does and what they see change.** One sentence.
   If the answer is "they read", it is not a widget; write a paragraph or
   draw a picture instead.
2. **Put the content in the JSON.** Names, values, labels, texts. The JS
   then holds only the behavior, and the widget can be reused with other
   content.
3. **Pick a prefix** no other widget uses (`cb-` in the sample) and name
   the root `<div class="widget w-<name>" data-widget="freestyle" data-name="<name>">`.
   The JS finds it with
   `document.querySelectorAll('[data-widget="freestyle"][data-name="<name>"]')`.
4. **Follow the rules above**: tokens only, complete at rest, real buttons,
   `aria-live` on the text that changes, no page-wide scrolling, no
   network.
5. **Show the state, not just the result.** The best freestyle widgets draw
   the thing (the cache entry, the queue, the tree) and let the reader
   change it. A log line under the drawing that says what just happened and
   why is usually worth more than an extra control.
6. **Keep it small.** Three to five controls. If it needs more, the section
   is teaching two ideas; split it.

A freestyle widget lives in the page it was written for. If it would serve
other topics with different JSON, save it here as `<name>.html` with the
three marked blocks and add a row to the table.

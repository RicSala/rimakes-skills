# Preview: animated action

A small React component that plays the action the user is about to do, and
stops on the result. For a todo list: text fills the input, the button is
pressed, a row enters the list.

Use it in a first-use empty state on a page or a large card. One per screen.

## Build it

1. **Pick the story.** Three or four steps: the input, the trigger, the
   result. The last frame is the filled screen.
2. **Draw it small and simple.** Copy the shapes of the real screen with the
   theme's tokens. Use bars in place of words, and plain `div`s in place of
   real inputs and buttons.
3. **Pick the library.** Look in `package.json` for `motion` or
   `framer-motion`. Use it when it is there. When it is not, write CSS
   keyframes in the project's own way (Tailwind theme, `tw-animate-css`, a CSS
   module). Do not add a library for this without asking. A CSS-only preview
   can stay a Server Component.
4. **Animate only `transform` and `opacity`.** They do not move anything
   around the preview.
5. **Reduced motion.** With `prefers-reduced-motion: reduce`, show the last
   frame and move nothing.

The rules for every preview in `SKILL.md` apply too.

## Timing

| Step                           | Length      | Easing      | Why                                                                              |
| ------------------------------ | ----------- | ----------- | -------------------------------------------------------------------------------- |
| Wait before the first movement | 300–500 ms  |             | The user reads the title first. Then the movement draws the eye to the preview.  |
| Text fills the input           | 600–900 ms  | linear      | It stands for typing, the slow part of the real action.                          |
| The button is pressed          | 100–150 ms  | ease-in-out | A press is short in real life.                                                   |
| The result enters              | 200–300 ms  | ease-out    | Under 100 ms the eye misses it. Over 400 ms a small element feels slow.          |
| Gap between two steps          | 150–300 ms  |             | The steps read as cause, then effect.                                            |

The whole pass takes 2 to 4 seconds, and never more than 5. A user will not
wait longer to see the end. Motion that starts by itself and lasts more than
5 seconds also needs a pause control (WCAG 2.2.2).

## Do not loop

Play once, then stay on the last frame.

- The last frame is the message: this is what you get. A loop keeps erasing it.
- Motion that never stops pulls the eye away from the title and the button.
- An endless loop needs a pause control (WCAG 2.2.2).

A replay is fine when the user asks for one: play again when the pointer or
the keyboard focus enters the empty state. Do it by changing the `key` of the
preview, which needs a Client Component.

## Sketch

A CSS-only version for "add a todo". Each element has its own animation, a
delay that places it in the story, and `both` so it holds its first frame
before and its last frame after.

```tsx
export function AddTodoPreview() {
  return (
    <div aria-hidden="true" className="preview">
      <div className="preview-input">
        <span className="preview-typed" />
        <span className="preview-button" />
      </div>
      <div className="preview-row">
        <span className="preview-check" />
        <span className="preview-line" />
      </div>
    </div>
  )
}
```

```css
/* Sizes and colors of the shapes are left out. Take them from the theme. */
.preview { width: 16rem; height: 6.5rem; }

.preview-typed  { transform-origin: left; animation: preview-type 800ms linear 400ms both; }
.preview-button { animation: preview-press 150ms ease-in-out 1400ms both; }
.preview-row    { animation: preview-enter 250ms ease-out 1700ms both; }

@keyframes preview-type  { from { transform: scaleX(0); } to { transform: scaleX(1); } }
@keyframes preview-press { 50% { transform: scale(0.94); } }
@keyframes preview-enter { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: none; } }

@media (prefers-reduced-motion: reduce) {
  .preview * { animation: none; }
}
```

The last movement ends at 1950 ms. With `animation: none`, every element shows
its normal style, which is the last frame.

With `motion`: the root is a `motion.div` with `initial="start"` and
`animate="end"`. Each child has the two variants, and its `end` variant carries
`transition: { delay, duration, ease }` with the same numbers. When
`useReducedMotion()` is true, set `initial={false}` so it renders the last
frame.

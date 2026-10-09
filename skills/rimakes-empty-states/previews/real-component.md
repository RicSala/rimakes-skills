# Preview: the real component with sample data

Render the screen's real list, table or card with two or three example items,
faded and switched off. It is the real UI, so it changes when the UI changes
and never goes out of date.

Use it when the component that draws the items takes them as props. If the
component reads its own data, it cannot take sample items: use example rows
instead, or ask before you split it.

## Build it

1. **Find the component that draws the items.** It takes the items and the
   handlers as props and reads nothing by itself.
2. **Check the rows for side effects.** A row that reads by id (an avatar, a
   count, a query for each row) would send real requests with sample ids. If
   the rows do that, use example rows instead.
3. **Write the sample items as a function** that returns the real item type,
   in a file next to the component. The type checker then flags the samples
   when the type changes.
   - Texts come from the dictionary.
   - Customize them: the viewer is the author, the organization's name is in
     the text.
   - Show the range of the UI, for example one done item and two open ones.
   - Ids are clearly not real (`sample-1`).
   - No `new Date()` or random value during render: the server and the
     browser would render different HTML. Use fixed values.
4. **Pass the samples as props only.** Never write them to a cache, a store or
   the server. Another component could read them as real data.
5. **Switch it off.** The real component has real buttons, so `aria-hidden`
   is not enough. Add `inert` to the wrapper: nothing inside can be clicked or
   take focus. Pass handlers that do nothing.
6. **Fade it and cap its height.** Lower the opacity, fade the bottom edge
   out with a mask, and set a maximum height with `overflow: hidden`. Label it
   "Example".
7. **Keep tests honest.** A test that counts rows must not count the sample
   rows. Mark the wrapper (`data-preview`) and keep the row selectors out of
   it.

The rules for every preview in `SKILL.md` apply too.

## Next.js

Handlers are functions, and a Server Component cannot pass a function to a
Client Component. If the real component is a Client Component that needs
handlers, make the preview a small Client Component.

## Sketch

```tsx
"use client"

export function TodoListPreview({ viewer }: { viewer: TodoViewer }) {
  const t = useTranslations("todos")

  return (
    <div
      aria-hidden="true"
      inert
      data-preview
      className="pointer-events-none max-h-40 overflow-hidden opacity-60 select-none [mask-image:linear-gradient(to_bottom,black_40%,transparent)]"
    >
      <TodoList
        todos={sampleTodos(t, viewer)}
        onToggle={() => {}}
        onDelete={() => {}}
      />
    </div>
  )
}
```

`inert` is a boolean prop from React 19. In React 18 write `inert=""`.

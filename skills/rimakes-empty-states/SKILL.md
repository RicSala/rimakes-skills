---
name: rimakes-empty-states
description: 'Design and build empty states with very good UX for React and Next.js screens: find why the screen is empty (first use, no results, all done, no permission), write the title, the sentence and the one action, add a preview, and make sure the empty state never shows before the app knows the list is empty. Use when the user asks to "add an empty state", "improve the empty state", "zero state", "no results screen", "what shows when there is no data", when building or reviewing a list, table, inbox, dashboard or search that can have zero items, or invokes /rimakes-empty-states.'
argument-hint: "[path or screen]"
---

# Empty states

An empty state is what a screen shows when it has nothing to list. A very good
one says why the screen is empty and lets the user fix that right there, with
one action. The user should have a real item a few seconds after they arrive,
without looking for the next step.

Arguments: `$ARGUMENTS`. If they name no screen, take the one being discussed.
If that is unclear, ask.

## 1. Read the screen

Read the component and follow its data back to the read. Note:

- Who reads the list: a Server Component, or the browser (React Query, SWR,
  `useEffect`).
- What the screen shows during the read, and when the read fails.
- What the project already has: an empty-state primitive (shadcn `Empty`), a
  skeleton, a dictionary for texts, a permission check that reaches the UI, an
  animation library.
- What the app knows about the viewer on this screen: their name, their role,
  the organization, what they did before.

Use what exists. Add no dependency without asking.

## 2. Find the reason

A list shows zero rows for six reasons. Only four are empty states.

| Reason                                        | The user needs                     | Main action                |
| --------------------------------------------- | ---------------------------------- | -------------------------- |
| **First use**: nobody added anything          | To know what the screen is for     | Create the first item      |
| **No results**: a search or filter hides all  | To know the items still exist      | Clear the search or filter |
| **All done**: the user finished everything    | A confirmation, with real numbers  | None, or one quiet link    |
| **No permission**: this viewer cannot add     | To know who can                    | None                       |
| Still loading (not an empty state)            | To know the answer is on its way   | A skeleton, no text        |
| The read failed (not an empty state)          | To know it failed                  | Try again                  |

Ask in this order. The first "yes" decides what to show:

1. Did the read fail? An error with a retry.
2. Is the parent missing, or hidden from this user? Not found.
3. Is the answer still on its way? A skeleton.
4. Does the whole collection have 0 items? First use, or no permission.
5. Do the search and filters leave 0 items? No results, or all done.
6. Otherwise, the list.

To tell 4 from 5, the query must return two facts: the items after the filter,
and whether the collection has any item at all. Add the second fact if it is
missing. Never show the first-use message for no results: the user reads that
their data is gone.

## 3. Build the parts

| Part       | Answers                       | Rules                                                                                                           |
| ---------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Title      | "What is this state?"         | The user's words: "No todos yet", not "No data".                                                                |
| Sentence   | "What will be here, and why?" | What will appear and who it is for.                                                                             |
| One action | "What do I do now?"           | A verb and a thing ("Add a todo"). It calls the real use case. One main action, at most one quieter second one. |
| Preview    | "What will I get?"            | Optional. See section 4.                                                                                        |

- **Customize.** Use what the app knows about the viewer in every part: the
  organization's name in the sentence, the viewer's role in the action, what
  they did before in the suggestions, the viewer as the author of example
  content. "Todos you add here are shared with everyone in Acme" is better
  than "Add todos for your team". A generic text is the fallback, for when the
  app knows nothing.
- **Permission.** Show the action only when this viewer can use it. Use the
  same check as the use case (for example a `can` field on the DTO). If the
  viewer cannot act, the sentence says who can. No disabled button without a
  reason.
- **Container.** A page holds all four parts. A card holds a title, a sentence
  and one button. A popover holds two lines. A table keeps its header and
  filters and shows the empty state in the body.
- **Words.** No blame ("You have not created anything"). No joke that hides the
  next step. Under two lines. Every text in every locale; other languages run
  longer than English.
- **Access.** The title is a real heading. A no-results message that appears
  after a search sits in an element with `role="status"`.

Make it fast to start. These three moves are what makes it feel magical:

1. **Start inside the empty state.** Put the input or the upload area in it,
   with focus. Do not send the user to another page to begin.
2. **Offer real first items.** Suggestions, templates, an import. One click
   makes one real item. Pick them for this viewer when you can.
3. **Show the first item at once.** Write it to the screen before the server
   answers, and undo it if the server refuses. The empty state leaves without
   the page moving.

Avoid: a picture that pushes the action off the screen; hiding the search box
and filters when there are no results; motion that loops or lasts more than
5 seconds; six empty cards on one dashboard (use one empty state for the page).

## 4. Preview

A preview shows the user the result before they do any work. Use it only for
first use, and only where there is room. If the user did not pick a kind, use
example rows.

| Kind            | What it is                                                                                                                      | Recipe                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Example rows    | Two or three faded rows of example content under the empty state, labelled "Example". Hand-drawn, no motion.                    | This line is the recipe.                                   |
| Real component  | The screen's real list rendered with sample items, faded and switched off. Needs a list that takes its items as props.          | [previews/real-component.md](previews/real-component.md)   |
| Animated action | A small component that plays the action the user is about to do, once, and stops on the result.                                 | [previews/animated-action.md](previews/animated-action.md) |

Rules for every preview:

- It is decoration. `aria-hidden="true"`, and nothing inside can take focus.
  The title and the sentence carry the meaning.
- It never replaces the action. The real button or input sits outside it.
- Nobody can take it for real data: it is faded or labelled. It is never saved
  to the database.
- It uses the theme's tokens, so it reads in light and dark.
- It has a fixed size, so nothing around it moves.
- Words inside it come from the dictionary. Bars in place of words need none.
- Its example content is customized too: the viewer as the author, the
  organization's name in the text.

## 5. Next.js rules

APIs change between versions. Check the docs of the installed version (newer
versions ship them in `node_modules/next/dist/docs`) before you use one.

- **Show the empty state only when the app knows.** In a Client Component the
  data is `undefined` on the first render. `data ?? []` with no loading check
  shows the empty state, then the list. Check in this order: failed, loading,
  empty, list.
- **Test with data in the database.** With 0 items the false empty state looks
  the same as the true one, so you cannot see the bug.
- **A server read needs a fallback.** With no `loading.tsx` and no
  `<Suspense>`, the previous page stays on screen during the read. Prefer
  `<Suspense>` around the one part that reads, so the rest shows at once.
- **Same height.** Give the skeleton and the empty state about the same
  height, or the page moves when one replaces the other.
- **An empty list is a normal page**, status 200. Call `notFound()` only when
  the parent does not exist. `error.tsx` is for the failed read.
- **After the first item, the list is old.** A Server Action calls `refresh()`
  or `revalidatePath`. A browser cache marks the query as old. To show the
  item at once, use `useOptimistic`, or write to the cache in `onMutate`.
- **No browser-only data on the first render.** The server cannot read
  `localStorage`. An empty state that depends on a flag stored there causes a
  hydration error. Read it after mount, or keep it in a cookie.

## 6. Check and hand off

1. Run the project's typecheck and lint.
2. Look at every reason in the browser: a new account, a search with no match,
   a slow network, a read that fails, a viewer who cannot add. Then add the
   first item and watch the empty state leave.
3. Add one test that opens the screen with no items.
4. Tell the user, in a few lines: which reasons the screen now handles, the
   texts you wrote, and what you left out and why.

## Notes

- Deleting the last item brings back the first-use state. A shorter text is
  fine there: the user already knows the feature.
- Send one analytics event when the user clicks the action in a first-use
  state. The share of new users who click tells you if the empty state works.
- To review empty states without changing them, run sections 1 to 5 as
  questions against each one and report what is missing.

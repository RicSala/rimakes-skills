# Logic

Assume the change computes the wrong thing, and prove it. You succeed when
you can name a valid input or state that gives a wrong result. Nobody is
attacking here: these are normal people doing normal things.

## Questions to ask

**Values**

- Empty, missing, null, zero, negative, very large, duplicated?
- A wrong type at runtime where the types say otherwise: casts, non-null
  assertions, data from outside that nobody validated?
- Text with spaces, upper and lower case, other alphabets, emoji?

**Boundaries**

- None, one, many. First and last. Off by one.
- Pages: the last page, an empty page, items added while paging.

**Dates and numbers**

- Time zones, daylight saving, end of month, "now" read twice.
- Rounding, floating point, money kept in floats, overflow, mixed units.

**Conditions**

- An inverted check, `and` where `or` was meant, the wrong variable, the
  wrong default.
- A missing `else`, a `switch` with no default, an early return that skips
  work that must always run.

**State**

- A state that can be entered but never left, or one nothing can reach.
- A change allowed from a state where it makes no sense.
- Derived or cached data that goes stale when its source changes.
- Deleting something other things still point to.

**Contracts**

- The change relies on something other code does not promise: the order of
  a list, a value never null, exactly one result.
- A function whose name, types or docs say one thing while it does another.
- Callers of a changed function that still expect the old behavior.
- Logic copied from elsewhere and only partly adapted.

## Leave out

- Attackers and bad data sent on purpose: `hostile-input` and `access`.
- Twice, at once, out of order: `timing`.
- A call that fails or is slow: `failure`.
- Existing data, deploys and old clients: `rollout`.

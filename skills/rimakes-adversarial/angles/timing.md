# Timing

Assume the change breaks when things happen twice, at the same time, late or
out of order. Find the sequence that breaks it.

## Questions to ask

**Twice**

- A retry, a double click, a webhook or message delivered again, a job that
  runs again after a crash, a person who goes back and submits again.
- Does the second run do the work again: two charges, two emails, two rows?
- What stops it: a unique constraint, an idempotency key, a status check?
  Is that check inside the same transaction as the write?

**At the same time**

- Two requests on the same record.
- Check-then-act: read "does not exist", then insert.
- Read-change-write: read a count, add one, save it. One update is lost.
- A unique value picked by reading the current maximum.
- What makes it safe: a lock, a unique constraint, an atomic update, the
  isolation level? Is it really there?

**Late or out of order**

- Events that arrive in another order than they were sent: "deleted" before
  "created", an older update after a newer one. Does the code compare
  versions or timestamps?
- A callback that arrives after the person undid the action.
- A cache or client state that shows old data after a change.

**Replay**

- A valid signed message, token or link used again later, or after it
  should have expired: invites, reset links, webhooks.

**Background work**

- A job queued inside a transaction that later rolls back.
- Work that starts before the data it needs is committed.
- A timer or cron that overlaps its own previous run.

## How it fails

The sequence, step by step: who does what, in what order, and the wrong
result. When it is cheap, prove it with two parallel calls to a local
server or a scratch script.

## Leave out

- A single call that fails or is slow: `failure`.
- Logic that is wrong even when it runs once: `logic`.

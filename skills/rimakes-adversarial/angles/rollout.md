# Rollout

The new code does not start in an empty world. Find what breaks when it
meets existing data, old code that is still running, and old clients.

## Questions to ask

**Existing data**

- Rows written before this change: do they meet the new code's
  assumptions? A new non-null column, a new enum value, a new format, a new
  unique rule.
- A migration that fails on real data: duplicates for a new unique index,
  nulls for a new `NOT NULL`.
- A migration that locks or rewrites a big table.
- Data that needs a backfill, and no backfill.

**Deploy order**

- During a deploy, old and new code run at the same time. Does old code
  break on the new schema (a dropped or renamed column)?
- Does new code break on old data still moving: queued jobs, events, cached
  values, sessions, cookies with the old shape?

**Contracts others depend on**

- A public API, webhook, event payload, message, URL or export changed in a
  way that breaks existing callers or saved links.
- Mobile apps or open browser tabs still running the old version.
- A removed or renamed field that something still reads.

**Config**

- A new env var or setting: is it required? What happens when it is
  missing: a crash at boot, a silent default, a feature that turns itself
  off?
- Is it set in every environment? A secret that must exist before the
  deploy?

**Rollback**

- If the code is rolled back after the deploy, does the old code still work
  with what the new code wrote: new enum values, new rows, a migrated
  schema?

**Defaults and flags**

- A new default that changes behavior for existing users or records.
- A feature flag with the wrong default.

## How it fails

What state exists, what gets deployed, and what breaks for whom.

## Leave out

- Bugs that show up on a fresh, empty database too: the other angles own
  those.

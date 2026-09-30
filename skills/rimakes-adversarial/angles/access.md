# Access

Find every way a caller can read or change, through this change, something
that is not theirs.

## 1. Learn how this repo guards things

Repos do this in different places: middleware, route guards, policies,
checks inside each handler, database row rules, a scope or tenant object
passed around. Before judging the change, find out from the repo's docs and
from code the change did not touch:

- Where identity is established and how code gets it.
- Where "may this person do this?" is decided.
- How data is kept apart between users, teams or tenants.

Judge the change against **that** model, not against one you prefer.

## 2. Walk every entry point and query the change adds or touches

**Who can call it**

- Is there an identity check, and does it run before any work?
- Is it meant to be public? If so, does the code say so, or did the check
  just get forgotten?
- Can a lower role do what only a higher role should?
- Is there a new way in that skips the guard the others go through: a new
  route, action, job or tool that calls the same function?

**Which records it touches**

- A record loaded by an id from the request: is it checked to belong to the
  caller, or to the caller's team or tenant?
- The owner or tenant id: does it come from a trusted place (session,
  verified token, server-side lookup) or from the request itself?
- Lists and searches: filtered by owner or tenant in the query, not after?
- Writes, updates and deletes: checked as strictly as reads?
- Related records reached through the first one (children, files,
  comments): checked too?
- A changed query that lost a filter it had before?

**What comes back**

- More fields than the caller should see: secrets, tokens, internal ids,
  other people's emails? A whole row where a trimmed object was meant?
- An error that reveals whether a record the caller may not see exists?
- Ids that can be guessed and walked through one by one?

**Secrets**

- Keys or tokens in the code, in logs, in error messages, or sent to the
  browser (public env vars, props passed to client code)?

## How it fails

A request: who is signed in, what they send, and what they get or change
that they should not. If you can show it safely against the local app with
data you created, do; otherwise trace it in the code and say so.

## Leave out

- Validation and injection: `hostile-input`.
- Logic bugs with no access angle: `logic`.

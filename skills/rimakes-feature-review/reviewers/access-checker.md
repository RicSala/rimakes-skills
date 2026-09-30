# Access-checker

Find every way someone could read or change, through this feature, something
they should not.

## 1. Learn how this repo guards things

Repos do this in different places: middleware, route guards, policies,
checks inside each handler, database row rules, a scope or tenant object
passed around. Before judging the feature, find out from the repo's docs and
from a sibling feature:

- Where identity is established and how code gets it.
- Where "may this person do this?" is decided.
- How data is kept apart between users, teams or tenants.

Judge the feature against **that** model, not against one you prefer.

## 2. Walk every entry point

Take each entry point in the brief (page, API route, RPC, action, job,
webhook, tool). For each:

**Who can call it**

- Is there an identity check, and does it run before any work?
- Is it meant to be public? If so, is that stated by the code, or did the
  check just get forgotten?
- Can a lower role do what only a higher role should?

**Which records it touches**

- The record is loaded by an id from the request: is it checked to belong
  to the caller, or to the caller's team or tenant?
- The owner or tenant id: does it come from a trusted place (session,
  verified token, server-side lookup) or from the request itself?
- Lists and searches: filtered by owner or tenant in the query, not after?
- Writes, updates and deletes: checked as strictly as reads?
- Related records reached through the first one (children, files, comments):
  checked too?

**What comes back**

- More fields than the caller should see: secrets, internal ids, other
  people's data?
- An error that reveals whether a record the caller may not see exists?

**What comes in**

- Input from outside validated before use?
- Input that ends up in a query, a file path, a URL the server fetches, a
  shell command or HTML?
- Uploads: type, size and destination checked?
- Webhooks: signature verified before the body is trusted?
- Something costly a caller can trigger without limit?

**Secrets**

- Keys or tokens in the code, in logs, or sent to the browser?

## How to report

"How it fails" is a request: who is signed in, what they send, and what they
get or change that they should not. If you can show it safely against the
local app with data you created, do; otherwise trace it in the code and say
so.

## Leave out

- Logic bugs with no access angle: the bug-hunter owns those.
- General hardening advice not tied to a line of this feature.

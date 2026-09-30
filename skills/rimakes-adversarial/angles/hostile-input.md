# Hostile input

Someone sends bad data on purpose. Find what they can make the code do.

## 1. List every way data comes in

Everything the change reads that someone outside the code controls: request
body, query string, headers, cookies, URL params, form fields, uploaded
files, webhook bodies, queue messages, answers from third-party APIs, and
content another user stored earlier.

## 2. Follow each one to where it is used

**Validation**

- Validated on the server, before use? Does the schema match what the code
  assumes?
- Extra fields accepted and passed on? A body spread into an update can set
  `role: "admin"` or an owner id.

**Where it ends up**

- A database query built from strings.
- HTML or markdown shown to another person (XSS).
- A shell command, a file path (`../`), a template.
- A URL the server fetches (SSRF: internal addresses, cloud metadata).
- A redirect target (open redirect).
- A regular expression (slow patterns on long input).
- Logs, email headers, CSV exports (formula injection).

**Size and count**

- Very long strings, huge arrays, deep nesting, big files.
- Something costly a caller can trigger without limit: emails, AI calls,
  exports, uploads, heavy queries.

**Parsing**

- JSON with unexpected types, numbers sent as strings, `__proto__` keys.
- Dates in odd formats.
- Unicode tricks where a name or slug must be unique: lookalike characters,
  zero-width characters, different normalization.

**Uploads**

- Type checked by content, not only by file name? Size limited?
- Where it is stored, and with what content type it is served back?

**Webhooks and signed data**

- Signature checked on the raw body, before the body is trusted?

## How it fails

The exact payload, where it is sent, and what happens. If you can show it
safely against the local app with data you created, do; otherwise trace it
in the code and say so.

## Leave out

- Who may call what, and data leaking in responses: `access`.
- Valid input that gives a wrong result: `logic`.
- A valid message sent again later (replay): `timing`.

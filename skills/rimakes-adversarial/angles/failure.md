# Failure

Assume every call outside this process can fail, be slow or half succeed.
Find what the change leaves broken when that happens.

## 1. List every outside call

Everything the change calls or depends on that can fail on its own:
database, cache, other services and APIs, storage, email, queues, the file
system, the browser's network.

## 2. For each one, ask

**It fails**

- Is the error caught? Caught and ignored? Caught, and the code carries on
  as if it worked?
- Does the right error reach the person, or none, or a raw vendor error?
- A promise nobody waits for: its error is lost, or its work is cut off
  when the request ends (serverless)?

**It is slow**

- Is there a timeout? Is a request held open?
- A slow call inside a database transaction: it holds locks and can hit
  the transaction's timeout.
- Retries that multiply the load on something already slow.

**It stops half way**

- Which writes already happened, and is what is left valid?
- Does one transaction cover every write that must happen together?
- A database write plus a call to another service: what if the call fails
  after the write commits? What if the write rolls back after the call ran
  (an email sent for a row that does not exist)?
- Cleanup that does not run when an earlier step throws.

**It returns something odd**

- Empty, null, a partial list, a different shape, a success status with an
  error in the body, pagination cut short.

**Resources**

- Connections, file handles, listeners or timers not closed on the error
  path.

## How it fails

Which call fails, at which step, and what state is left behind or what the
person sees. When it is cheap, make it fail locally: stop the service, point
to a wrong URL, or throw from a scratch copy outside the repo.

## Leave out

- The same call succeeding twice: `timing`.
- Bad data sent on purpose: `hostile-input`.

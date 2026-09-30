# Test-checker

Check three things about the feature's tests: the important scenarios are
tested, we do not test library code, and the tests are built on a sound
structure.

First run the feature's tests the way the repo's docs say. A failing, flaky
or very slow test is a finding by itself.

## 1. Are the important scenarios tested?

1. From the code, not the tests, list what the feature does: for each public
   function, use case and entry point, the main path, every rule and branch,
   every error it can raise, every check on who may call it, and the edge
   cases that matter (empty, limits, runs twice).
2. Match each one to the test that would fail if it broke.
3. Report the ones with no such test, most costly first. The question for
   each: **"what bug could someone add here that no test would catch?"**
   Name that bug in "How it fails".

Also report a test that exists but cannot fail: no assertion, an assertion
on the wrong thing, or a scenario the setup never really reaches.

Not the goal: full coverage. Code with no decision in it (pass-through,
plain data, wiring) needs no test of its own.

## 2. Are we testing library code?

A test should prove something about **our** code. Report tests that only
prove again what a third party already promises and tests for itself:

- The assertion is about the library's own behaviour: the ORM saves a row,
  the validation library rejects a bad email by its own built-in rule, the
  framework routes a request, the date library adds a day.
- The only thing exercised is a mock: set the mock to return X, call
  through, assert X.
- A snapshot that is mostly a third-party component's output.
- A check the compiler or type checker already makes.

Not a finding: tests of **how we use** a library. Our schema, our query, our
config, our mapping of its errors, and integration tests at the boundary are
our code.

For each one, say whether to delete the test or rewrite it to assert our
logic, and what that rewritten test would assert.

## 3. Is the structure sound?

Learn the repo's own test tools first: shared helpers, factories, fixtures,
fakes, mocks, setup files, and how sibling features write their tests.

**Setup and helpers**

- The same setup copied across tests where a helper or factory should be.
- The opposite: a helper so big or clever that a test no longer shows what
  it sets up and what it checks.
- A shared helper, factory or fake exists in the repo and the test builds
  its own.
- Setup in a before-hook that most tests in the file do not need.
- Hidden defaults in a helper that a test silently depends on.

**Isolation**

- State shared between tests; tests that pass only in a given order.
- Data, mocks, timers or env changes that are not reset.
- Real network, real clock, randomness or sleeps.

**Mocks**

- Mocking our own code where the real thing is cheap to use.
- So many mocks that the test checks the mocks.
- A hand-made mock where the repo has a shared one.
- A mock whose shape no longer matches the real thing.

**Assertions and shape**

- Asserting how the code works (call counts, private state, internal
  order) instead of what it produces.
- One test that checks several unrelated things.
- A name that does not say the scenario and the expected result.
- Logic in a test (loops, conditions) that could itself be wrong.

**Level**

- A unit test with mocks for something only a test against the real
  database, API or browser can prove.
- A slow end-to-end test for a rule a unit test would pin down.

## Leave out

- Bugs in the feature's code: the bug-hunter owns those. A missing test is
  your finding; the bug it would have caught is theirs.
- Test style taste with no effect on what the tests prove or cost.

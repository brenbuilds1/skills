---
name: unclebob
description: >
  A do-not-read-the-code workflow for delegating implementation to a coding
  agent. Use when handing an agent a feature or a fix you do not intend to
  review line by line. The agent turns the request into human-approved
  gherkin scenarios, writes failing tests and locks them before writing any
  implementation, then runs the result through machine-checked constraints:
  unit tests, acceptance scenarios, changed-line coverage, mutation testing
  on both the code and the gherkin examples, quality metrics, the project's
  own checks, plus a torture round (property, performance, jitter tests)
  where the change warrants it. The human reads a one-table
  receipt instead of the diff. The test lock and the mutation constraint exist
  because the same agent writes both the code and the tests.
---

# Uncle Bob

The promise of agent coding is that you stop reading the code. The problem
with keeping that promise is that the constraints meant to replace your
reading are written by the same agent that writes the code. A test suite
authored by the thing it judges proves very little on its own.

This skill keeps the promise anyway, by making a faked pass cost more than
an earned one. Scenarios get approved by the human before any code exists.
Tests get written, shown to fail, and locked before implementation starts.
After the lock, no round of work may touch code and tests together. And
mutation testing runs at the end because it is the one constraint a vacuous
test cannot talk its way through.

## When To Use

Delegated implementation you intend to judge by behavior, not by reading:
features, bug fixes, refactors with a defined outcome. Skip it for throwaway
scripts and exploratory spikes, and say so out loud when you skip it.

Right-size it. The full gauntlet is for code nobody will read. When the
human is going to read the diff anyway, plain unit tests can be the whole
gauntlet; running every constraint on a two-line fix is ritual, not
confidence.

## The Order Is The Point

1. **Contract.** The gherkin feature file is the spec of record and the
   human owns it. The agent may draft scenarios (Given/When/Then) from the
   request, but the human edits and approves them before anything else
   happens. Add a short list of planned unit tests. This is the one
   artifact the human must read, and it is written in behavior, not code.
2. **Lock.** Write the tests. Run them. Show they fail, and fail for the
   right reason (a missing feature, never a typo in the test). Then declare
   the lock in the transcript: "tests locked at <commit or timestamp>".
3. **Implement.** Write code until the tests pass. No test edits in this
   phase.
4. **Gauntlet.** Run every constraint below. Fix and re-run until green or
   until a failure needs a human decision.
5. **Receipt.** Report the one-table receipt. Never report done without it.

## The Lock

After step 2, code rounds and test rounds never mix:

- A round may edit implementation, or edit tests, never both.
- A test round must state its reason before it starts (a scenario changed,
  a test was wrong, a new edge case was approved by the human).
- Every test edit after the lock appears in the receipt with that reason.
  A weakened assertion, a deleted test, or a skip marker added to get a
  constraint green is a gauntlet failure, and the receipt says so.

## The Constraints

Run all seven. A constraint whose tool is missing from the project gets
marked `skipped: <reason>` in the receipt. A skipped constraint is visible,
never silent.

1. **Unit.** Full suite green.
2. **Acceptance.** Every approved scenario executes and passes, driven
   from the feature file itself (behave, pytest-bdd, cucumber-js). The
   agent writes step handlers, never freestanding acceptance tests that
   can drift from the gherkin. Where no BDD runner exists: one integration
   test named after each scenario, asserting that scenario's example
   values.
3. **Changed-line coverage.** Coverage measured on the diff, not the repo.
   Default floor: 90% of changed lines. `diff-cover` works against any
   Cobertura/LCOV report.
4. **Mutation, both layers.** Code layer: run mutants on the changed files
   only (full-repo runs take hours); every surviving mutant is listed by
   name in the receipt, then killed with a new test in a declared test
   round or justified in one sentence. Spec layer: copy a scenario, flip
   an important example value, re-run acceptance, and require a failure.
   An acceptance suite that still passes on mutated examples is asserting
   nothing, and that is a gauntlet failure.
5. **Quality metrics.** Lint clean, no new warnings, no new duplication,
   and no high-CRAP functions (CRAP weighs a function's complexity against
   its coverage; where no CRAP tool exists, cap new-function cyclomatic
   complexity at 10).
6. **Project checks.** Whatever the repo itself runs: `make test`, the CI
   script, pre-commit hooks. The project's own QA procedure outranks this
   skill's defaults wherever they disagree.
7. **Torture round.** The agent is fast; spend the speed here, where it
   applies. Property tests (hypothesis, fast-check, proptest) on any changed
   function with an invariant worth stating: round-trips, ordering,
   idempotence. A performance test wherever the project states a budget to
   assert against; no budget, no test, say so. For changed concurrent code,
   jitter tests: repeat the racy paths under randomized thread timing until
   a race surfaces or the repetition count earns the all-clear, and put that
   count in the receipt.

| Language | Tests | Coverage | Mutation |
|---|---|---|---|
| Python | pytest | pytest-cov + diff-cover | mutmut |
| JS/TS | vitest or jest | c8 / built-in + diff-cover | StrykerJS |
| Rust | cargo test | cargo-llvm-cov | cargo-mutants |
| Go | go test | go test -coverprofile | gremlins |
| Java | JUnit | JaCoCo | PIT |

## The Receipt

This table replaces the diff as the thing the human reads:

```markdown
| constraint | result | detail |
|---|---|---|
| unit | pass | 42 passed, 0 failed, 0 skipped |
| acceptance | pass | 6/6 scenarios |
| changed-line coverage | pass | 94% (floor 90%) |
| code mutation | pass* | 31 killed, 2 survived, justified below |
| spec mutation | pass | 4/4 mutated examples failed as required |
| quality | pass | lint clean, max new complexity 7 (cap 10) |
| project checks | pass | make test green |
| torture | pass | 200 property cases; jitter x500 clean; perf skipped: no budget |
| test edits after lock | 1 | round 4: scenario "expired token" tightened, human approved |
```

Surviving mutants each get their own line under the table:

```text
mutant: auth.py:88 `>=` -> `>`  survived
why: boundary already pinned by scenario "expired token at exact expiry"
action: justified (equivalent mutant), no new test
```

## Hard Rules

- Never report done without the receipt.
- Never weaken, delete, skip, or retag a test to turn a constraint green. If a
  test is genuinely wrong, that is a declared test round with a stated
  reason, and it shows up in the receipt.
- Coverage is judged on changed lines. Repo-wide numbers can rise while the
  new code ships untested.
- Surviving mutants are named one by one. "A few mutants survived" is not a
  receipt.
- A skipped constraint names its reason. Silence is a failure, and so is
  quietly substituting an easier tool.
- When the human asks what the code does, answer from the scenarios and the
  tests. If the answer is not in them, that is a missing scenario: propose
  it, get approval, run a test round.

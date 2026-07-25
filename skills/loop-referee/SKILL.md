---
name: loop-referee
description: >
  Referee for writer/reviewer loops between coding agents, where one tool
  writes code and the other reviews it in rounds: Claude Code writing while
  Codex reviews, or the reverse. Use when running or setting up a cross-model
  review loop, a test-fix loop, or any "loop until clean" workflow. Sets the
  loop contract up front, keeps an append-only per-round ledger, applies stop
  rules so the loop converges, ends, or escalates to a human instead of
  burning tokens on two agents disagreeing. Does not write or fix code itself.
---

# Loop Referee

A reviewer running on a different vendor's model catches what the writer
cannot see. The two can also disagree politely forever, at your expense. The
stop rules loops ship with today are "clean" and a round cap. Real loops die
in ways neither covers. This skill is the stop switch and the receipt.

## When To Use

Any loop where one agent produces and another judges: cross-agent code review
(Claude Code writes, Codex reviews, or the reverse), test-fix loops,
doc-review loops. Run the referee alongside the loop, not instead of it.

## How The Loop Actually Runs

No extra software. The loop lives inside one agent session, and that agent is
the referee. Example with Claude Code writing and Codex reviewing:

1. You: "implement X, then run a review loop with codex."
2. The agent writes the contract (below), then implements.
3. Reviewer round: the agent runs the reviewing tool on the diff, for example
   `codex exec "review this diff for must-fix bugs: $(git diff)"`, or the
   Codex plugin, or a review bot on the PR.
4. The agent appends a ledger row, checks the stop rules, then either fixes
   and loops or stops and says which rule fired.

Works the same with the roles reversed, or with any writer/reviewer pair. The
stop rules bind on countable events (a reopened finding, two rounds of diff
growth, the round cap), so the orchestrating agent cannot easily talk itself
into one more round.

## The Contract

Write these down before round 1. Changing them mid-loop is how loops run away.

- Max rounds: 3, unless the human raises it in writing.
- Severity floor: which findings the writer must fix (for example bug or
  security). Findings below the floor get logged, not fixed, and never extend
  the loop.
- Scope: the files the loop may touch. A fix outside scope ends the round.
- Budget: a token or cost cap if the platform supports one.

## The Ledger

Append one row after every round. Never edit old rows.

```markdown
| round | opened | closed | reopened | must-fix left | diff lines | verdict |
|---|---|---|---|---|---|---|
| 1 | 4 | 0 | 0 | 3 | +120/-30 | continue |
| 2 | 1 | 3 | 0 | 1 | +18/-6 | continue |
| 3 | 0 | 1 | 0 | 0 | +2/-2 | clean: stop |
```

## Stop Rules

Stop the loop the moment a rule fires, and name the rule. In order:

1. Clean round. No must-fix findings remain. Stop. Done.
2. Reopened finding. A finding closed in an earlier round comes back. That is
   not progress, it is the writer and the reviewer disagreeing. Stop
   immediately, hand it to the human with both positions. Round n+1 does not
   get to re-litigate round n.
3. Flip-flop. The reviewer asks to undo something it previously required.
   Same as a reopen: the human decides.
4. Diff growth. The fix diff is bigger than the previous round's fix diff, two
   rounds in a row. The loop is diverging, not converging. Stop and recommend
   reverting to the round with the fewest must-fix findings; the ledger says
   which. The human approves any revert. The referee never reverts code
   itself.
5. Round cap. Max rounds hit with findings still open. Stop, escalate the
   leftovers. Never silently raise the cap.
6. Nit round. A round produced only findings below the severity floor. The
   loop is done, it just does not want to say so. Stop.

## Escalation Shape

Hand the human the ledger plus each unresolved finding in this form, not the
transcript:

```text
finding: expired tokens pass at exact boundary (`auth.ts:42`)
reviewer says: use `now >= exp`, the boundary is exploitable
writer says: spec reads exp as inclusive; changing it breaks token refresh tests
rounds contested: 2, 3
```

## Hard Rules

- The referee never writes or fixes code. It judges rounds.
- Ledger rows are append-only. A rewritten ledger is not a receipt.
- Every stop names the rule that fired.
- Findings below the severity floor never extend the loop.
- A loop that ends on rules 2 through 6 always hands the human the ledger.
  Silence is not an exit.

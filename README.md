# Skills

Agent skills that read the receipts: the code, the bill, the ledger, the
diff. Trust what you can re-check, cut what you cannot.

## Skills

### Uncle Bob

`unclebob` is a do-not-read-the-code workflow for delegated implementation:
surround the agent with extreme constraints and read the receipt instead of
the diff. The catch that plan usually skips is that the same agent writes the
code and the tests. So the skill makes the human approve gherkin scenarios up
front, locks failing tests before any implementation exists, forbids mixing
code edits and test edits in one round, and runs mutation testing last on
both layers, code mutants and mutated gherkin examples, as the test of the
tests. When the change warrants it there is a torture round: property tests,
performance tests against a stated budget, jitter tests for concurrent code.
Done means a one-table receipt, never a diff to read.

Path: [`skills/unclebob/SKILL.md`](./skills/unclebob/SKILL.md)

### STE Audit

`ste-audit` checks output against the ASD-STE100 rules a machine can
verify: sentence caps, noun cluster limits, verb forms, passive voice,
paragraph limits. Violations table with Issue 9 rule numbers and quotes,
per response, plus a drift verdict. The memory line makes the style
appear; nothing checks it holds. This does. No dictionary bundled, that
list is ASD's.

Path: [`skills/ste-audit/SKILL.md`](./skills/ste-audit/SKILL.md)

### Memory Audit

`memory-audit` fact-checks an agent's persistent memory against the repo it
describes. Every line in CLAUDE.md, AGENTS.md, rules files, and memory
directories is a claim that ages: paths move, commands get renamed, flags
disappear, and the agent stays confidently wrong. This skill splits the
memory into atomic claims, verifies each one against the current codebase
and git history, and reports a per-line verdict table (confirmed, stale,
wrong, contradiction, preference) with a receipt for every verdict. It
reports; the human prunes.

Path: [`skills/memory-audit/SKILL.md`](./skills/memory-audit/SKILL.md)

### Engineering Review

`engineering-review` is a bug-first review stance for diffs, PRs, commits,
migrations, architecture changes, implementations, test plans. It focuses on
real breakage, missing coverage, operational risk, and user impact instead of
style noise.

Path: [`skills/engineering-review/SKILL.md`](./skills/engineering-review/SKILL.md)

### Loop Referee

`loop-referee` referees writer/reviewer loops between coding agents: one tool
writes, another reviews, they iterate. It sets a loop contract up front, keeps an
append-only per-round ledger, then applies stop rules that end the loop on
convergence, disagreement, or divergence instead of burning tokens on polite
model deadlock. Built for cross-model setups like Claude Code writing while
Codex reviews.

Path: [`skills/loop-referee/SKILL.md`](./skills/loop-referee/SKILL.md)

### Skill Auditor

`skill-auditor` audits a third-party agent skill before you install it. It
clones the package read-only, greps for fetch-and-execute, credential access,
hidden code, network calls, and broad permission grants, then reports findings
with exact `file:line` receipts and an honest severity. It never installs or
runs the thing it audits.

Path: [`skills/skill-auditor/SKILL.md`](./skills/skill-auditor/SKILL.md)

## Layout

```text
skills/
  engineering-review/
    SKILL.md
  loop-referee/
    SKILL.md
  memory-audit/
    SKILL.md
  skill-auditor/
    SKILL.md
  ste-audit/
    SKILL.md
  unclebob/
    SKILL.md
```

Flat by design. No category maze.

## Install

Any agent (Claude Code, Codex, Cursor, Copilot, Gemini, Windsurf, more):

```sh
npx skills add brenbuilds1/skills
```

Claude Code, as a plugin:

```text
/plugin marketplace add brenbuilds1/skills
/plugin install bren-skills@bren-skills
```

Manual: copy or symlink any folder under `skills/` into your agent skills
directory.

```sh
cp -R skills/unclebob ~/.codex/skills/
```

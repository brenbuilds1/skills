---
name: memory-audit
description: >
  Fact-check an agent's persistent memory against the repo it describes.
  Use when an agent acts on outdated notes, when inheriting a project with
  existing CLAUDE.md / AGENTS.md / rules files, after a large refactor, or
  periodically on any long-lived project. Inventories every memory and
  instruction file the agent loads, splits them into atomic claims, verifies
  each claim against the current codebase and git history, and reports a
  per-line verdict table (confirmed, stale, wrong, contradiction,
  preference) with a receipt for every verdict. Reports only; never edits
  or deletes memory.
---

# Memory Audit

Agents now remember. Nothing checks what they remember. A memory line
written on day one is still steering the agent on day 365, and a note that
was true in March makes the agent confidently wrong in July. Every entry in
a memory file is a claim about the repo. Claims age. This skill fact-checks
them, line by line, with receipts.

## When To Use

The agent just did something baffling that traces back to an old note. You
inherited a project whose instruction files nobody has read in months. A
refactor moved directories, renamed commands, or swapped tools. Or simply:
the memory files have grown for a while and nobody has ever pruned them.

## What Counts As Memory

Audit every file the agent loads without being asked:

- `CLAUDE.md` at repo root and parents, plus anything it imports
- `AGENTS.md`, `GEMINI.md`, `.github/copilot-instructions.md`
- `.cursor/rules/`, `.cursorrules`, `.windsurfrules`
- the agent's per-project memory directory (for Claude Code, the
  `memory/` folder under the project's config dir, `MEMORY.md` first)

Inventory them with line counts before judging anything. Memory nobody can
list is memory nobody is auditing.

## Method

1. **Inventory.** List every memory file found, its size, and its last
   modified date from git.
2. **Extract claims.** Split each file into atomic claims: a path, a
   command, a flag, a version, a rule, a recorded decision. One claim, one
   row.
3. **Verify each claim, cheapest check first.** Does the path exist? Is the
   command still in the Makefile or package.json? Is the flag still in the
   config? Does the version match the lockfile? Does the named function
   still exist (grep)? For dated facts, check git log around the date, not
   memory of the date.
4. **Cross-check the files against each other.** Two instruction files that
   disagree are worse than one stale file: the agent obeys whichever it
   read last.
5. **Report the table.** Never fix anything.

## Verdicts

- **confirmed**: receipt found in the current repo.
- **stale**: was true once, the repo moved on. Cite what replaced it or
  the commit that removed it.
- **wrong**: contradicted by the repo right now.
- **contradiction**: two memory files disagree. Cite both lines.
- **preference**: a convention or style rule with nothing to check. Not a
  risk, but it still costs context every message; count these.

## The Receipt

```markdown
| where | claim | verdict | receipt |
|---|---|---|---|
| CLAUDE.md:14 | tests live in spec/ | stale | renamed tests/ in a1b2c3 (Apr) |
| CLAUDE.md:9 | use python3 not python | confirmed | Makefile:37 |
| AGENTS.md:22 | never commit to main | contradiction | .cursor/rules/git.md:3 says squash to main |
| memory/build-fix.md:6 | build needs NODE_OPTIONS hack | wrong | flag removed, package.json:41 |
| CLAUDE.md:31 | prefer early returns | preference | n/a |
```

End with the counts (x confirmed, y stale, z wrong, n contradictions) and
the single most dangerous entry: the one most likely to make the agent
confidently wrong tomorrow.

## Hard Rules

- Report only. This skill never edits, trims, or deletes a memory file.
  The human prunes with the table in hand.
- Every verdict cites a receipt someone can re-check. A claim with no
  evidence either way is `preference` or `unverifiable`, never `wrong`.
- No invented staleness. A convention is not stale just because no code
  enforces it.
- Dates come from git history, not from guessing.
- Do not write the audit report into a memory file. It would be the next
  stale entry.

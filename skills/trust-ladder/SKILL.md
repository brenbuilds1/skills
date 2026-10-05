---
name: trust-ladder
description: >
  Audits a coding-agent setup against the trust ladder: the environment that
  makes agent output trustworthy enough to stop micromanaging and scale up.
  Use when the user supervises one agent all day, ferries bug reports or
  context into agent chats by hand, reviews every agent diff, wants more
  agents running at once, or wants agents to merge their own pull requests.
  Checks eight rungs (verification, determinism, constraints, outer loop,
  coordination, queues, review, autonomy), cites evidence for each, and names
  the next rung to build. Reports only; changes nothing.
---

# Trust Ladder

The practices here come from Matt Pocock's livestream with Lauren Tan
(poteto), and Cory House's summary of it. Grouping them into rungs, the order,
the Check lines, the statuses and the hard rules are this skill's own.
`sources.md` cites each practice with a timestamp and a speaker; read it only
when the user asks where a practice comes from. The audit does not need it.

The ladder, in Cory House's words: the more you can constrain the agent's
output, the more you can trust it. Verification is the lever that generates
that trust. Low on the ladder you micromanage, and micromanaging leaves no
time to build the environment that would lift you. poteto prefers the picture
of a Michelin kitchen to a software factory: processes and tools that make
good work the default, with the human still responsible for the final
outcome. Work backwards from one question: what would it take for an agent to
merge its own code?

## Method

1. Read the repo and agent setup: tests, lint config, skills, scripts, CI,
   connectors, recent PRs.
2. For each rung below, cite evidence: a file, a command, a PR.
3. Rungs 4 to 7 are partly workflow. Ask the user what files cannot show,
   and mark answers that cannot be checked as unknown.
4. Report the table, one row per rung. Name the lowest rung marked partial
   or missing.

## The Rungs

1. **Hands and eyes.** The agent runs the code, uses it the way a user
   would, and can debug it, take traces and snapshots. Without that, you are
   the proxy between the agent and its output, and there is no loop. With a
   score to aim at, the agent can keep improving on its own. At poteto's
   company every app has a verification skill, maintained automatically.
   Check: can the agent see the result of its own change without you?

2. **Deterministic parts in code.** Put the mechanical steps of a skill in a
   CLI or script inside the skill, and leave the agent only the judgment.
   Without one, poteto's agents rebuilt the tooling each time, each a
   different way, at a cost in context and speed. Ask how much of each skill
   and rule could be deterministic. For migrations, the mechanical part is a
   codemod over the syntax tree, run by a script instead of the agent.
   Check: does a skill make the agent repeat the same mechanical steps?

3. **The easy thing is the right thing.** In poteto's experience agents,
   frontier ones included, tend to take shortcuts. Watch how they fail, and
   every time you see a mistake, ask how to make it impossible in the
   codebase: a lint rule, a type constraint, a convention. Strict lint rules,
   one way to do each thing, a directory per feature, no god files. Rules in
   the environment do not overload the agent with things to remember; it runs
   into them when it matters. A good codebase is easy to change.
   Check: when a mistake repeated, did the fix land in the environment or in
   a prompt?

4. **Outer loop.** The inner loop is agents building toward a snapshot of
   your intent. The snapshot goes stale as bug reports and requests land in
   chat, issue trackers and email. Connect those sources so new work reaches
   the agents on its own: triage, reproduce with the verification skill,
   confirm the bug still exists on main. Context, not control.
   Check: where are you the bottleneck, and why does the agent need you to
   answer this? Each piece of context you ferry in by hand is something to
   teach the agent to fetch itself, from real data, not by guessing.

5. **A coordinator for related work.** A coordinator agent delegates and
   supervises; it does not do the work itself. Send related issues to one
   coordinator. One agent per bug may duplicate work and can miss the shared
   cause that several reports together reveal.
   Check: do related issues reach one coordinator, or one agent each?

6. **A queue before fixes.** For sweeps, such as hunting a known bad
   pattern, the agent can append findings to a document. Review the queue
   every few days and group what you see. This is sometimes more effective
   than spawning a fix per finding.
   Check: do sweep findings get looked at together before anything is fixed?

7. **Review by sampling.** You cannot taste every dish. Sample the agents'
   work every day and scrutinize it. When several agents make the same
   mistake, fix the environment, not the one agent. A one-off may need
   nothing.
   Check: is there a daily sample, and does a repeated mistake end in an
   environment change?

8. **Autonomy last, only where verifiable.** Agents merging their own work
   needs verification and the environment to hold (here, rungs 1 and 3), and
   getting there takes time and effort. The interview's example: on each pull
   request, verifier agents run the app, click through it like a user, hunt
   regressions, fix and re-verify before it lands. It is token-heavy; one
   verifier, or the agent checking its own work, costs less. Landed work is
   reviewed in the commit history; problems get reverted or modified, and
   new lint rules added. Where work is verifiable, one-way doors become
   two-way doors in a sense. Where it is hard to verify by program, getting
   there is very hard, and poteto did not have an answer.
   Check: which kinds of change here are reversible and provable by a check,
   and are those the only ones that merge without a human?

## Keeping Skills Useful

Offer these as next steps when rung 3 or rung 7 does not hold.

- Look through past agent transcripts for the moments you corrected or
  stepped in, and turn each pattern into a skill or a lint rule. Past chats
  are the real process, not the one in your head.
- As models improve, cut implementation details from skills and keep the
  workflow. poteto expects skills to get smaller over time.
- State intent precisely. One word can carry a lot of intent: "tautological
  tests".

## Output

```markdown
| rung | status | evidence | next step |
|---|---|---|---|
| 1 hands and eyes | partial | e2e tests exist; agent cannot run the app | verification skill with a CLI |
| 3 easy thing right | missing | same import mistake in 4 recent PRs | lint rule |
| 5 coordinator | unknown | user unsure how issues are routed | ask who triages |
| 8 autonomy | not yet | rung 1 partial | stay on human review |
```

Status is one of: holds, partial, missing, unknown, and for rung 8 only,
not yet. One row per rung, all eight. End with the lowest rung marked partial
or missing: that is the next thing to build. List unknown rungs separately,
as questions for the user.

## Hard Rules

- Report only. Never change code, settings or memory.
- Every status cites evidence a person can re-check, or says unknown.
- Mark rung 8 holds only when rungs 1 and 3 both hold; otherwise not yet.
- A pattern across agents is an environment fix; a one-off is not.

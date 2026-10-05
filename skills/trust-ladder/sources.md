# Sources

- Interview: Matt Pocock's livestream "LIVE: Poteto (creator of pstack) on
  shipping 1,000's of PR's a month at SpaceX", announced for Friday 2 October
  2026: <https://www.youtube.com/watch?v=MN9dGgmLyso>. Matt is the host;
  poteto is Lauren Tan, the guest.
- Timestamps below follow the full 66.6 minute stream as re-uploaded on X by
  a third party, with burned-in subtitles, on 3 October 2026
  (<https://x.com/thecsguy/status/2106398397506916410>). Matt
  said at 00:06 he would cut the YouTube version down, so its times may differ.
- Cory House's summary, 5 October 2026:
  <https://x.com/housecor/status/2107080556378816979>

Excerpts come from an automatic transcript, so expect small errors. Where it
heard "lit rules" (31:31, 55:13, 63:44), the speaker almost certainly said
lint rules, and
where it heard "verify it's unworked" (54:46), "verify its own work"; the
excerpts use the corrected words. Every practice in `SKILL.md` maps to a row below. The
grouping into rungs, the order, the Check lines, the statuses and the hard
rules are the skill's own.

| practice | time | who | excerpt |
|---|---|---|---|
| The trust ladder | 01:37, 02:45 | Matt, describing poteto's talk | "climbing the trust ladder with agents"; "you talk about a trust ladder with agents" |
| Ladder in one line | n/a | Cory House's summary | "The more you can constrain the agent's output, the more you can trust it." |
| Verification generates trust | 17:12 | Matt | "that is the lever that you can start to generate trust" |
| Low trust means micromanaging | 27:43 to 28:14 | poteto | "stuck in this mode where you're very low on that trust ladder ... the only way to cope ... is just to kind of lock in and micromanage" |
| Michelin kitchen over a factory | 12:29 to 13:16 | poteto | "I've never really liked the term software factory ... I do think software factory is an apt term ... Michelin kitchen is the thing that I've sort of landed on" |
| Still responsible for the outcome | 15:54 to 16:04 | poteto | "you as a human are still responsible for the final outcome" |
| Good work by default | 52:45 to 52:52 | poteto | "setting up guardrails and constraints so that they do the right thing by default"; Cory: "Create processes and tools that make beautiful code automatically." |
| Work backwards to self-merge | 40:46 to 40:58 | poteto | "the big unlock for me ... is starting from the question and working backwards of how do I get to the point where my agent can merge its own code" |
| 1 Hands and eyes | 17:23 to 18:05 | poteto | "the single most important skill ... is verification"; "give your agent hands and eyes"; run the code "like a normal human user would", "take traces and snapshots" |
| 1 No loop without verification | 18:32 to 19:04 | poteto | "if the agent can actually see the result of its work"; "the most important part of a loop ... is the verification part" |
| 1 Hill climbing | 19:22 to 19:44 | poteto | "some kind of rubric or a way to judge or score something ... an agent continually try to make improvements" |
| 1 A verification skill per app | 20:17 to 20:23 | poteto | "every app that Cursor has or SpaceX AI has, has a verification skill that is auto maintained" |
| 2 Deterministic parts in a CLI | 22:00 to 23:16 | poteto | "take the deterministic parts of what the skill does and encode that into a script or a CLI"; the skill is "a wrapper with some light instructions around ... custom tools" |
| 2 Agents rebuilt the tooling | 24:06 to 24:27 | poteto | "when we didn't have a CLI ... it would basically rebuild the world each time and every agent did it differently"; "not just about context usage, but also speed" |
| 2 How much could be deterministic | 24:51 | poteto | "how much of your skills and rules could actually be deterministic" |
| 2 Codemods for migrations | 25:27 to 25:57 | poteto | "code mods ... crawling the AST ... transforming code literally mechanically, like a script does it for you instead of the agent" |
| 3 Agents take shortcuts | 07:09 to 07:29 | poteto | "agents from my experience, even the frontier ones, tend to take shortcuts ... How do I make the easy thing the right thing?" |
| 3 Make the mistake impossible | 32:56 to 33:07 | poteto | "observing how agents fail ... every time you see a mistake ... How do I make it so that the code base makes this impossible" |
| 3 Strict rules, one way, feature folders | 31:31 to 32:07 | poteto | "really, really restrictive lint rules"; "only one way to do something"; "Every feature has its own directory" |
| 3 No god files | 32:27 to 32:45 | poteto | "I'm not going to append to a god file"; "eight god files, which were like at least 10,000 lines long" |
| 3 Rules in the environment | 33:54 to 34:06 | Matt | "you're not overloading your agent ... it stumbles into the rules and bounces off them" |
| 3 Easy to change | 26:35 to 26:41 | Matt | "a good code base is a code base that's easy to make changes in" |
| 4 Inner loop, stale snapshot | 36:38 to 37:06 | poteto, hedged: "I don't know if I'm using the definition correctly" | "my inner loop is ... my agent engineers ... building towards ... a snapshot of my intent ... the snapshot can go stale" |
| 4 Connect the sources | 36:24, 38:14 to 38:45, 42:24 | poteto | bug reports go to "slack or linear or x"; connectors to "your email, your calendar, Slack"; "subscribe to the Slack channel ... go off and triage ... reproduce the issue ... verify that the bug actually still exists on main" |
| 4 Where am I the bottleneck | 39:08 to 39:54 | poteto | "where am I the bottleneck ... how do I actually get the agent to answer its own question ... not by guessing, but actually real data" |
| 4 Context, not control | 42:43 to 42:54 | poteto, quoting Netflix managers | "managers would talk about was this idea of context, not control" |
| 5 Coordinator delegates | 40:20 to 40:38, 44:00 to 44:15 | poteto | "coordinator agents ... delegating and not doing work of their own"; "It doesn't do the work itself" |
| 5 Related work together | 45:47 to 46:17 | poteto | spawning "one agent per task" loses "that sort of thread between them ... you may duplicate work" |
| 6 Queue before fixes | 48:47 to 49:20 | poteto | "I tell it to append it to a document ... every couple of days, I look at it"; "sometimes that's actually more effective" |
| 7 Review by sampling | 50:41 to 51:26 | poteto | "you cannot be tasting every single dish ... it becomes more about sampling"; "you go in there every day ... scrutinize it very rigorously" |
| 7 Fix the environment, not the agent | 51:33 to 52:16 | poteto | "course correct the environment, right? Not that single agent"; "if it was a one-off incident, it's fine" |
| 8 It takes time | 52:30 to 52:56 | poteto | "it's very hard to get to this point ... It takes a lot of time and effort" |
| 8 Verifier agents per PR | 53:58 to 54:36 | poteto | "spawn a bunch of Verifier agents for every pull request and it will fuzz ... click around ... look for regressions ... fix it itself" |
| 8 Token cost, tunable | 54:36 to 54:50 | poteto | "quite token intensive ... instead of like 10 verifier agents, you might do like one, right, or just tell the agent to verify its own work" |
| 8 Verification plus environment | 54:55 to 55:04 | poteto | "I guess verification plus the environment. It's the combination of these two things that allow me to step away" |
| 8 Review the commit history | 55:05 to 55:13 | poteto | "I'll review it in the morning by looking at my commit history. And if I see problems, I go in course correct ... revert or modify, add new lint rules" |
| 8 One-way doors | 57:11 to 58:44 | Matt asks, poteto answers | "for domains where the work is verifiable ... the one-way doors become two-way doors in a sense"; "verifiability of the domain is an important aspect"; "a great question that I don't really have the answer to" |
| Mine past transcripts | 61:44 to 62:14, 63:35 to 64:05 | Matt's tip, restated and extended by poteto | "I think you showed a tip actually, today ... look at your previous transcripts ... where you correct them ... turn that into a reusable skill"; "suggest turning them into lint rules or new skills"; "the real process, not an abstract idea in your head" |
| Skills get smaller | 64:55 to 65:29 | poteto | "with the latest models, you can just delete those parts and just really focus on the workflow"; "I think over time, we'll see that skills get smaller and smaller" |
| State intent precisely | 08:00 to 08:42, 10:36 to 11:11 | poteto, citing Matt's tip | "the bottleneck ... becomes your ability to express your intent"; "one of my favorite ones that you've shared ... tautological tests ... You compress a lot of intent and meaning into words" |

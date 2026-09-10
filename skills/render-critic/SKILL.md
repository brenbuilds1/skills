---
name: render-critic
description: >
  Use when agent-built 3D looks flat, sloppy, or AI-made: a three.js or
  WebGPU game scene, a Blender render, a glTF export, a Unity or Unreal
  level. Also use when a
  make-it-look-better loop keeps circling, when animation looks floaty or
  feet slide, when self-graded scores inflate while the scene stays ugly, or
  before shipping a scene people judge by eye.
---

# Render Critic

The builder does not grade its own work here. Asked to, it leans on the
description of the work, and an open-ended "loop until 8/10" inflates the
score while the scene stays ugly. This skill splits the roles and pins everything the
grade could leak through: same shots, same rubric, worst axis wins, capped
rounds, failure allowed.

## When To Use

Any agent-built 3D output judged by eye, from a single glTF prop to a
whole three.js level or Blender render. Run it after the scene loads and
animates correctly. It scores looks. Gameplay and frame rate are out of scope.

## Setup: Keeping The Critic Blind

The critic is a fresh agent with no conversation history. Its entire
input is the rubric section below plus the screenshot files. It never
sees code, the builder's notes, prior rounds, or prior scores.

Get that empty context however your harness allows: a subagent, a fresh
exec, a new session, a clean chat with the rubric pasted in. The mechanism
is the fresh start, never the tool name. The
critic starts on the cheapest tier with vision as a budget default, not a
tested quality floor; step it up the first time its receipts get vague or
it misses a flaw you can see. Save the strong model for the builder.

## Shot List (identical every round)

| shot | framing rule |
|---|---|
| wide | whole scene in frame, horizon visible, 30-40 degrees elevation |
| gameplay | camera at player height (1.6-1.7m), the view a player actually gets |
| orbit | 45 degrees off the wide angle, same distance |
| closeup | busiest material fills over half the frame |

Fixed resolution (1280x800), a fixed lighting state and time of day, all
UI hidden, so every receipt has to point at the scene and never at a
loading tile or a button. Pause animation and reuse four saved camera poses so the stills
compare round to round. In three.js with `WebGLRenderer`, capture the
canvas in the same frame as a render call, or set
`preserveDrawingBuffer: true`, or the shot comes back blank. In Blender, render headless with a pinned config (example:
Cycles, 96 samples, a fixed seed, AgX view transform) so the same script
gives the same render every round.

**Animated scenes add a motion burst.** Whatever moves in play (a run
cycle, a chase, water, particles) hides its worst tells between frames:
foot sliding, pop-in, LOD popping, shadow shimmer. With animation
RUNNING, capture six frames at a fixed interval (about 150ms) from the
gameplay camera, named `round-N-burst-k.png`, and hand them to the critic
as an ordered strip. A static prop render skips this.

## Method

1. Round 0: shoot the shot list and have the critic score the scene as-is.
   This baseline is the before/after receipt.
2. Builder fixes the lowest axis first. Before the next round it states, in
   one line, what changed. No stated change, round forfeited.
3. Reshoot the same shot list.
4. Improvement gate: take the file the last report cited for the failing
   axis, pair it with the new shot from the same camera, and hand a fresh
   critic the two unlabeled. It gets the axis name, that axis's anchor
   row, and the question: which one is better on this axis. Ask twice,
   order swapped. The new shot must win both; a split or a loss counts as no
   improvement, and the round is forfeited. For the motion axis,
   compare the old burst strip against the new one the same way.
5. A fresh critic instance scores the new round against the full rubric,
   with no history of prior rounds.
6. Repeat to pass or cap. Cap: 5 scored rounds. Forfeited rounds (builder
   side: no stated change, or a failed improvement gate) burn a cap slot;
   voided rounds (critic side, see below) do not. At the cap the critic
   issues PASS or FAIL-FINAL. FAIL-FINAL is a valid ending; the rubric is
   never renegotiated mid-run.

## Rubric (each axis 0-10; anchors are binding)

| axis | 0-3 | 7 | 9+ |
|---|---|---|---|
| silhouette + readability | shapes mush together | every object reads at gameplay distance | composition leads the eye |
| lighting + tone mapping | flat ambient, blown highlights, default tone mapping, reflective water reads flat | one clear key light, filmic or AgX transform, grounded shadows | deliberate mood |
| materials | uniform plastic albedo | plausible PBR: metal reads metal, roughness varies, no untextured fills | wear and variation look authored |
| scale + coherence | props float, clip, or z-fight; mixed texel density; the same asset visibly tiled | consistent scale and density, nothing intersects | looks arranged by hand |
| artifact check | seams, stretching, missing faces, shadow acne | none visible in any shot | survives the closeup |
| motion + continuity (animated only) | foot sliding, pop-in, LOD popping, shadow shimmer across the burst | locomotion reads, feet plant, nothing pops | weight and secondary motion sell it |

Scoring rules:

- Overall = the minimum axis score, never the mean. One bad axis fails the
  round.
- PASS = every axis at 8 or above.
- Every score cites its file and a pixel-level receipt. No receipt, no
  score.
- Scores between anchors interpolate; when unsure, score lower.
- Absolute scores drift between critic instances. The improvement gate,
  never the score delta, decides whether a round moved.
- Motion + continuity is scored from the burst strip, not the stills. A
  static render has no motion; skip that axis and take the minimum over
  the axes that apply.

## Calibrate The Critic

Prose anchors drift between critic instances. Pin one reference image per
axis: a shot you judge a true 8 on that axis, handed to the critic beside
the rubric as "this is an 8 for lighting." It costs one image per axis
and gives the critic something to compare against instead of a sentence
to interpret.

## Signs The Loop Is Gamed

- the score rose and the builder stated no change
- a score row cites no file or no pixel receipt
- the builder proposes edits to the rubric or the shot list
- scores land at exactly 8 across the board in round 4 or 5

Any of these voids the round. Rerun it with a fresh critic; a voided
round does not count against the cap, its rerun replaces it.

## Critic Report (fixed format)

```markdown
round 2 verdict: FAIL (overall 5; 2 rounds left)
| axis | score | file | receipt |
|---|---|---|---|
| lighting + tone mapping | 5 | round-2-wide.png | sky blown to white, foreground uniformly gray |
| materials | 6 | round-2-closeup.png | column albedo is one flat value, roughness identical to floor |
| silhouette | 8 | round-2-gameplay.png | columns, walls, roof slab separate cleanly |
lowest axis: lighting + tone mapping. fix that first.
```

## Fix Map (builder starts here on a failing axis)

| failing axis | first moves |
|---|---|
| silhouette | delete or merge clutter meshes; separate object value from sky and ground |
| lighting | set `renderer.toneMapping` (`ACESFilmicToneMapping` or `AgXToneMapping`; the default `NoToneMapping` is the flat look) and `toneMappingExposure`; one shadow-casting key light plus `scene.environment` from an HDRI |
| materials | set roughness and metalness per material, no defaults left; put one texture with variation on the largest surface |
| scale | meters as units, props rescaled against a 1.7m reference; settle floating props to contact |
| artifacts | apply transforms and recalculate normals before export; fit `light.shadow.camera` to the scene, then tune `shadow.mapSize` and `shadow.bias` |
| motion | lock foot contacts to kill skating; raise LOD and draw distance so nothing pops in the burst; add the secondary motion the eye expects (a swaying tail, kicked-up dust) |

## Stack Notes

- Unity, Unreal, or Godot: the loop and the rubric should transfer
  unchanged, untested here. Swap the capture mechanics and the tone mapping
  names for your engine; the notes below cover three.js and Blender, the
  stack this skill was built on.
- three.js: fix lighting and materials before reaching for the renderer.
  WebGPU buys compute and density headroom, not taste; a flat WebGL2 scene
  ports to WebGPU still flat.
- Blender to web: glTF with transforms applied, textures packed or baked,
  meters as units. Headless bpy with a pinned render config is the path
  this skill was written against and keeps renders repeatable; a live MCP
  plugin is untested for the shot list.
- Postprocessing: at most one pass (bloom or AO) until every axis clears 7.
  Effects come last; keep the receipt the critic cites visible until the
  axis passes.

## Cost

One loop = builder rounds plus, per round, one cheap critic call on four
stills (six more frames when the scene animates) and a two-image
improvement gate asked twice. The expensive version (a strong-model critic, a dozen
shots, no cap, scores argued in the main chat) multiplies spend. The cap
is the budget; the gate is the signal.

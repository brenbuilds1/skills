---
name: render-critic
description: >
  Use when agent-built 3D looks flat, sloppy, or AI-made, or when a scene
  of a real place does not look like the place: three.js or WebGPU
  scenes, Blender renders, glTF exports. Also use when a "make it look
  better" loop keeps circling, or the agent's own scores keep climbing
  while the scene stays ugly.
---

# Render Critic

The critic loop people share works: a separate agent screenshots the
scene from several angles, scores it 0 to 10 against a definition you
wrote down, and the builder keeps going until it clears the bar. This is
that loop plus the three fixes that mattered in use: the critic is
blind, the scene is judged against photos when it is a real place, and
the loop stops when it stops improving instead of running forever.

## The Loop

1. Shoot the scene from four fixed camera positions: wide, at player
   height, a 45 degree orbit, and a closeup of the busiest material.
   Same positions every round, UI hidden, animation paused. If the scene
   animates, add six frames in a row with animation running, so the
   critic can see feet slide or things pop in.
2. Hand the screenshots to a fresh critic agent with the score
   definitions below and nothing else: no code, no builder notes, no
   earlier rounds, no earlier scores. A new session on the same model
   that builds is enough.
3. If the scene is a real place, or you have concept art, give the
   critic the photos too, one matched to each camera position where you
   can. This is the single change that most improved real scenes in
   use. Without it the critic polishes lighting on a scene that does not
   look like the place. With neither photos nor concept art, the critic
   gets screenshots only and the likeness row is skipped.
4. The critic scores each axis and, for every score, says what it sees
   and in which screenshot. A score with no observation does not count.
5. The builder fixes the lowest-scoring axis, states in one line what it
   changed, and reshoots. Repeat.
6. Done when every axis reaches the target: 8, or whatever you set at
   the start. Stop early when two rounds in a row raise no score. Then
   change approach (swap the asset, rebuild the lighting rig, re-export
   from Blender, rewrite the shader) or hand the human the first and
   last screenshots side by side and call it.

## Scores (0 to 10, and what the numbers mean)

| axis | 0 to 3 | 7 | 9 and up |
|---|---|---|---|
| likeness (real places only) | not recognizable; landmarks missing or misplaced | landmarks, tones, haze, and scale match the photos at a glance | a side-by-side reads as the same place |
| readability | shapes mush together | every object reads at gameplay distance | composition leads the eye |
| lighting | flat, blown highlights, no clear light direction, water reads flat | one clear key light, filmic or AgX tone mapping, grounded shadows | deliberate mood |
| materials | plastic and uniform | roughness varies, metal reads metal, nothing untextured | wear and variation look authored |
| scale and placement | things float, clip, intersect, or repeat obviously | consistent scale, nothing intersects | looks placed by hand |
| artifacts | seams, stretching, missing faces, shadow acne | none in any shot | survives the closeup |
| motion (animated scenes) | feet slide, things pop in | movement reads, feet plant | weight and secondary motion |

The overall score is the lowest axis, not the average, so one ugly axis
cannot hide behind the others.

## Two Things To Know About The Critic

Scores drift. The same screenshots scored by two fresh critics can land
a few points apart. Treat the observations as the product and the
number as a rough gauge: a fix that removes the cited problem is
progress even when the number barely moves.

Critics agree with a good story too easily. Never let the builder score
its own work, never show the critic earlier scores, and if the score
keeps rising while the scene looks the same to you, the critic is
grading the description, not the pixels. Look yourself.

## Capture Notes (three.js and Blender)

- three.js with `WebGLRenderer`: capture in the same frame as a render
  call or set `preserveDrawingBuffer: true`, or the shot comes back
  blank. Fix lighting and materials before switching renderers; WebGPU
  adds headroom, not taste.
- Blender: render headless with a pinned config (engine, samples, seed,
  view transform) so rounds compare. Export glTF with transforms applied
  and textures packed.
- Other engines: same loop, your own capture.

---
name: slop-audit
description: >
  Use when a site or app UI might read as AI-generated or vibe-coded:
  before shipping a landing page, after an agent builds a frontend, or
  when someone says it looks like every other AI site. Also use before
  handing an agent a "reasons your site looks vibe-coded" list, so it
  strips defaults and not design choices.
---

# Slop Audit

Every list of "your site looks vibe-coded" tells mixes two things: the
defaults a model reaches for on any brief, and one author's taste. This
audit keeps only the tells that independent sources agree on (the viral
list, a Reddit study of 3.2 million posts, the published catalogs, and
the design guidance shipped with coding agents), names the items those
sources reject, and reports with receipts. It never rewrites; the fixes
are the builder's call.

## Method

1. Grep the source with the checks below, with `grep -E` (or `rg`) so
   the `|` alternations work. Every hit is a receipt: file, line,
   matched text.
2. Screenshot the home page and one inner page at 1440 and 390 wide.
   A fresh session judges the screenshots against the eye checks; the
   agent that built the page does not grade it.
3. Report every finding as one of three verdicts. A **tell** is on the
   agreed list or is a leftover fact. A **smell** is named by one or two
   sources. A **choice** is on someone's list but is design when done on
   purpose; it is reported so the builder can confirm it was chosen, and
   never counted against the page.

## Tells (agreed across sources)

| tell | check |
|---|---|
| Inter as the only typeface (Geist, Roboto, Space Grotesk appear on single lists) | grep `\bInter\b|Geist|Roboto|Space Grotesk` in CSS, font imports, `next/font`; then check whether a display face carries the identity |
| indigo, violet, or purple gradients; purple on black | grep `bg-indigo-|from-(indigo|violet|purple|fuchsia)|linear-gradient` and count |
| gradient text in a headline | grep `bg-clip-text|text-transparent|background-clip: text` |
| centered hero, three equal cards under it | grep `grid-cols-3|repeat(3` and look at the hero |
| one radius and one soft shadow on every card | grep `rounded-2xl|rounded-3xl` and `shadow-lg|shadow-xl`; compare counts to card count |
| untouched component-library defaults (shadcn zinc, indigo-500/600 or blue-600 buttons) | grep `bg-blue-600|bg-indigo-(5|6)00|zinc-`; look for unmodified Card and Button |
| frosted glass panels | grep `backdrop-blur|backdrop-filter` |
| Sparkles, Zap, ArrowRight icons; emoji used as icons or bullets | grep `Sparkles|Zap|ArrowRight` next to `lucide-react`; search markup with `rg "\p{Extended_Pictographic}"` (catches ✔ and ➡ bullets too) |
| the same entrance animation on every section, a hover effect on every card | grep `fade-in-up|animate-fade|whileInView|hover:` and compare to section count |
| three pricing tiers | eye |
| weightless copy: elevate, seamless, empower, unlock, transform, "Build faster. Ship smarter." | grep the words in copy, not class names (`transform` collides with CSS; skip it in the grep) |

## Leftovers (facts, not taste)

Builder residue proves how the page was made regardless of how it
looks: `lovable-tagger`, `gpteng.co`, `/lovable-uploads/`, `@base44/sdk`,
"Edit with Lovable", "Made in Bolt", "Built with v0", a builder
subdomain, and section comments like `<!-- Hero -->` or
`{/* Testimonials */}` left in shipped markup (a weak signal on its
own). Grep for each.

## Smells (one or two sources)

Bento grids; a four-column footer; the hero, features, proof, pricing,
FAQ, footer sequence in that order; ALL-CAPS eyebrow labels over every
heading; one word in a headline in a different color; an arrow glued to
every link; meta strings joined with middle dots; neon glow nobody
asked for; radial orbs behind the hero; testimonials with stock names
or placeholder text (grep `lorem ipsum`); no page linked at `/terms` or
`/privacy`; em dashes and the negation-then-elevation line in the copy
(grep `—` and `it'?s not .{1,40}, it'?s`). Also the second default: cream
background, high-contrast serif, terracotta accent. Swapping purple for
that is trading one predetermined look for another.

## Choices (one list names them, or a source calls them fine when chosen)

Drop shadows, hover states, rounded corners, a white background, a dot
grid, a terminal window, checkmark bullets, a colored left stripe,
pastel palettes, a font from the tells row paired with a display face
that carries the identity. The tell is never the element; it is the
element applied everywhere with nothing chosen. Report these as
choices, ask once, and move on.

## Report (fixed format)

```markdown
slop-audit: 4 tells, 2 smells, 3 choices (choices not counted)
| verdict | tell | where | receipt |
|---|---|---|---|
| tell | purple gradient | src/Hero.tsx:14 | `bg-gradient-to-r from-indigo-500 to-purple-600` |
| tell | three equal cards | src/Features.tsx:9 | `grid md:grid-cols-3 gap-6`, three identical cards |
| tell | emoji as icons | src/Features.tsx:12 | `🚀 Fast`, `✨ Simple`, `🔒 Secure` |
| tell | copy | src/Hero.tsx:8 | "Elevate your workflow" |
| smell | eyebrow labels | screenshot home@1440 | tracked-out caps over every section |
| smell | orbs | src/Hero.tsx:3 | `radial-gradient` blurred behind the hero |
| choice | drop shadow | src/Card.tsx:5 | `shadow-lg` on 3 of 3 cards. chosen? |
```

Reports only. Never rewrites.

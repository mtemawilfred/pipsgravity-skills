# PIPSGRAVITY SKILLS IMPROVEMENT GUIDE
# Version 1.0 — May 2026
# 
# PURPOSE: This document is both a reference and an active prompt.
# When Wilfred adds new material (course PDFs, strategy notes, video examples),
# paste this document + the new material into a session and say:
# "Update the skills using this guide."
# The model will know exactly what to add, where to add it, and how.

---

## WHAT THIS SYSTEM IS

The PipsGravity Chart Scene Pipeline converts a video idea into a rendered educational
forex video automatically. At its core are 4 Claude calls, each with a specific job:

```
User Video Idea
      ↓
[CALL 1 — Haiku]
Text Generator
Outputs: hook_text, youtube_title, youtube_description
No skills needed. Unchanged.
      ↓
[CALL 2 — Haiku]
Story Builder (Skill 1)
Files: forex_concepts.md + skill_1_story_builder.md
Outputs: blueprint JSON
      ↓
[CALL 3 — Sonnet]
Skeleton Builder (Skill 2)
File: skill_2_skeleton_builder.md
Outputs: skeleton JSON (market structure shapes + swings)
      ↓
[CALL 4 — Sonnet]
Chart Grammar Engine (Skill 3)
File: skill_3_chart_grammar.md
Outputs: CHART_SCENE JSON (candles + overlays)
      ↓
Parse & Validate (n8n)
      ↓
Remotion Render
      ↓
Final Video
```

Each call only knows what it needs. Nothing more.

---

## THE FOUR SKILL FILES — WHAT EACH ONE IS

---

### FILE 1: forex_concepts.md
**Role:** The concept dictionary. Pure definitions.
**Fed to:** Call 2 (Haiku story builder)
**Does NOT contain:** Candles, prices, OHLC, phases, overlays

This file makes Claude understand trading concepts before it does anything else.
Think of it as the glossary. Every concept the system can teach must be defined here.

**Currently contains:**
- Market Structure (bullish/bearish, strong/weak zones, timeframe alignment)
- Supply and Demand Zones (three validity conditions, mitigated vs unmitigated)
- Fair Value Gap / FVG (definition, math, minimum size)
- BOS — Break of Structure
- CHoCH — Change of Character
- Flip Setup
- Continuation Setup
- Equilibrium Entry
- Liquidity (all types: EQL, EQH, trendline, entry traps/IDM)
- Available setups table (what is ready to generate vs not ready)

**What to add here when adding a new concept:**
- Concept name and aliases
- Plain English definition (what it IS, not how to draw it)
- Why it matters in trading
- Rules that make it valid vs invalid
- How it connects to other concepts
- Mark it as READY or NOT READY in the available setups table

---

### FILE 2: skill_1_story_builder.md
**Role:** The teacher brain. Classify → Select setup → Build blueprint.
**Fed to:** Call 2 (Haiku story builder) alongside forex_concepts.md
**Does NOT contain:** Candles, prices, shapes, swings, overlays

This file decides WHAT story to tell for any given video idea.
Think of it as the curriculum designer.

**Currently contains:**

Section 1 — Concept Classifier
  Keyword table: maps user words → concept key → default setup → default type
  Example: "order block" → order_block → bullish_order_block → TYPE_2

Section 2 — Setup Selection
  Maps concept + bias + video type → exact setup_type key

Section 3 — Narrative Sequences
  Per setup: the story beats in order (what happens on the chart, step by step)
  Example bullish_order_block: equal_lows → liquidity_sweep → order_block → displacement → fvg → bos → retracement → idm → launch

Section 4 — Phase Structure Table
  Fixed candle counts per phase per setup
  Example bullish_order_block: A0=4, A=5, B=10, C=2, D=4, E=1, F=10, G=5

Section 5 — Required vs Optional Components
  What MUST appear in each setup vs what is optional

Blueprint output format (the JSON Skill 2 receives)

**Currently supports these setups:**
| Setup | Type | Status |
|---|---|---|
| bullish_order_block | TYPE_2 | ✅ READY |
| bearish_order_block | TYPE_2 | ✅ READY |
| bullish_demand_zone | TYPE_2 | ✅ READY |
| bearish_supply_zone | TYPE_2 | ✅ READY |
| eql_sweep | TYPE_1 | ✅ READY |
| eqh_sweep | TYPE_1 | ✅ READY |
| trendline_liquidity | TYPE_1 | ✅ READY |
| range_liquidity | TYPE_1 | ✅ READY |
| fvg_standalone | TYPE_1 | ✅ READY |
| bos_standalone | TYPE_1 | ✅ READY |
| choch_reversal | TYPE_1/2 | ❌ NOT READY |
| flip_setup | TYPE_2 | ❌ NOT READY |
| continuation_setup | TYPE_2 | ❌ NOT READY |
| equilibrium_entry | TYPE_2 | ❌ NOT READY |

**What to add here when adding a new concept:**
1. Add keywords to the Concept Classifier table
2. Add the setup_type to the Setup Selection table
3. Write the narrative sequence (story beats in order)
4. Add phase counts to the Phase Structure table
5. Define required and optional components
6. Update the status from NOT READY to READY

---

### FILE 3: skill_2_skeleton_builder.md
**Role:** The chart artist (shape level). Convert blueprint into visual structure.
**Fed to:** Call 3 (Sonnet skeleton builder)
**Does NOT contain:** OHLC prices, pip values, overlay coordinates

This file decides HOW the chart looks — the shape, rhythm, and swings —
before any numbers exist. Think of it as the pencil sketch before painting.

**Currently contains:**

Section 1 — Market Rhythm Library
  Visual shapes for each concept: what it looks like drawn with a pencil
  Swing patterns per rhythm: rise/drop/bounce/impulse sequences
  Example: equal_lows = drop, bounce, drop, bounce, drop (with visible bounces)

Section 2 — Setup Skeleton Library
  Per setup_type: which rhythm goes in which phase
  Example bullish_order_block:
    A0 = uptrend_context, A = downtrend_context, B = equal_lows,
    C = consolidation+sweep, D = displacement, E = bos,
    F = retracement+idm_fake_bounce+idm_sweep, G = launch

Section 3 — Swing Generator
  Table of swing types with direction, size label, candle count
  Size reference: tiny=2-5 pips, small=5-10 pips, medium=10-20 pips, large=20-40 pips

Section 4 — Teaching Clarity Rules (6 rules)
  Every concept identifiable without labels
  Sweep must visually stand out
  Displacement must be the strongest move
  Retracement must look slower than impulse

Section 5 — Event Mapping
  Every story beat → which phase it belongs in

Skeleton output format (the JSON Skill 3 receives)

**What to add here when adding a new concept:**
1. Add the rhythm to Section 1 (what does it look like as a pencil sketch)
2. Add the swing pattern for that rhythm
3. Add the setup skeleton to Section 2 (phase → rhythm mapping)
4. Add any new event → phase mappings to Section 5
5. Add any new teaching clarity rule if the concept has unique visibility requirements

---

### FILE 4: skill_3_chart_grammar.md
**Role:** The chart grammar engine. Convert skeleton into actual OHLC + overlays.
**Fed to:** Call 4 (Sonnet candle generator)
**Does NOT contain:** Trading decisions, concept explanations, setup selection logic

This file controls what the user actually sees.
Think of it as the technical drawing spec — exact measurements, validation, output format.

**Currently contains:**

Section 1 — Educational Chart Philosophy
  Educational realism > market realism
  Clean, readable, obvious, structured
  7-second test: can a beginner identify the concept before labels appear?

Section 2 — Market Geometry Rules
  Push/pullback rhythm rule
  Candle size hierarchy: tiny (2-5), normal (6-15), large (20-40)
  Wick rules per candle type
  Impulse decay rule
  Rhythm sizes per swing type

Section 3 — Candle Generation Rules
  OHLC validity (h >= max(o,c), l <= min(o,c))
  Generation process (5 steps)
  Starting price, phase candle counts, candle variety

Section 4 — Pattern Templates
  Exact candle patterns per concept:
  equal_lows, equal_highs, trendline_touch, sweep, ob_candle,
  displacement, bos, fvg_sequence, idm_fake_bounce, idm_sweep,
  retracement, launch, reversal_momentum, continuation

Section 5 — Overlay Grammar
  4-step process: generate candles → math checks → place overlays → validate
  All overlay types with exact schemas
  Placement rules per overlay type
  Teaching order per setup type

Section 6 — Mathematical Validation
  14 checks covering all concepts
  Each check has a FAIL action (what to fix)

Section 7 — Educational Visibility Validation
  7 yes/no questions
  All must be YES before outputting

Output format (CHART_SCENE JSON)

**What to add here when adding a new concept:**
1. Add a Pattern Template for the new concept's unique candle shape
2. Add any new mathematical checks to Section 6
3. Add the overlay teaching order for the new setup to Section 5
4. Add any new overlay type to the schemas if needed
5. Add any new educational visibility rule to Section 7 if the concept requires it

---

## HOW TO ADD A NEW CONCEPT — STEP BY STEP

When Wilfred has new material (a course PDF, a strategy document, a video example):

### STEP 1 — Read the material
Identify:
- What is the concept called? (name + aliases)
- What conditions make it valid?
- How does it connect to existing concepts?
- What does it look like on a chart (sketch in words)?
- What story does it tell?

### STEP 2 — Update forex_concepts.md
Add a new section with:
```
## [CONCEPT NAME]

What it is: [plain English definition]
Why it matters: [trading reason]
Valid conditions: [rules — ALL required vs optional]
Connects to: [other concepts it works with]
```

### STEP 3 — Update skill_1_story_builder.md

Add to Concept Classifier:
```
| concept_key | "keyword1, keyword2, keyword3" | default_setup | TYPE_X |
```

Add to Setup Selection table:
```
| concept | bias | type | setup_type_key |
```

Add to Narrative Sequences:
```
### [setup_type_key]
[Step by step story in plain English]
story array: ["beat_1", "beat_2", "beat_3", ...]
```

Add to Phase Structure table:
```
| setup_type_key | A0 | A | B | C | D | E | F | G |
```

Add to Required vs Optional:
```
### [setup_type_key]
Required: [list]
Optional: [list]
```

Update status table from ❌ NOT READY to ✅ READY.

### STEP 4 — Update skill_2_skeleton_builder.md

Add to Market Rhythm Library (if the concept introduces a new visual shape):
```
### RHYTHM: [rhythm_name]
Shape: [ASCII sketch]
Swings: [swing sequence]
Rules: [visual requirements]
```

Add to Setup Skeleton Library:
```
### SKELETON: [setup_type_key]
phaseX: [rhythm_name]   → [what happens here]
phaseY: [rhythm_name]   → [what happens here]
```

Add to Event Mapping if new story beats are introduced:
```
| new_story_beat | phase | notes |
```

### STEP 5 — Update skill_3_chart_grammar.md

Add to Pattern Templates:
```
### TEMPLATE: [concept_name]
Purpose: [what this pattern teaches]
[exact candle structure]
Mathematical check: [formula]
Educational visibility: [what must be true for it to be clear]
```

Add to Overlay Grammar — teaching order:
```
**For [setup_type]:**
  [overlay_1] → [overlay_2] → [overlay_3]
```

Add to Mathematical Validation if new checks are needed:
```
### [CONCEPT] CHECK
[formula]
FAIL: [what to fix]
```

---

## CONCEPT READINESS TRACKER

| Concept | forex_concepts | skill_1 | skill_2 | skill_3 | Status |
|---|---|---|---|---|---|
| Bullish Order Block | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Bearish Order Block | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Valid Demand Zone | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Valid Supply Zone | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Equal Lows Sweep | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Equal Highs Sweep | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Trendline Liquidity | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| Range Liquidity | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| FVG Standalone | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| BOS Standalone | ✅ | ✅ | ✅ | ✅ | ✅ READY |
| CHoCH Reversal | ✅ | ❌ | ❌ | ❌ | ❌ NOT READY |
| Flip Setup | ✅ | ❌ | ❌ | ❌ | ❌ NOT READY |
| Continuation Setup | ✅ | ❌ | ❌ | ❌ | ❌ NOT READY |
| Equilibrium Entry | ✅ | ❌ | ❌ | ❌ | ❌ NOT READY |

CHoCH, Flip, Continuation, and Equilibrium are defined in forex_concepts.md
but not yet added to Skills 1, 2, or 3. Add course material to unlock them.

---

## IMPROVEMENT SESSIONS — HOW TO RUN ONE

When you want to improve the skills (better charts, new concept, fixing a pattern):

**Option A — Adding a new concept:**
1. Paste this guide into the session
2. Share the new material (course PDF, strategy notes, video examples)
3. Say: "Add [concept name] to all four skills following this guide"
4. Review the additions before pushing to GitHub

**Option B — Improving an existing concept (bad chart output):**
1. Paste this guide into the session
2. Share the bad output (screenshot or JSON) and describe what is wrong
3. Say: "The [concept] is generating [specific problem]. Fix the relevant skill."
4. The model identifies which skill file contains the wrong rule and updates only that file

**Option C — Improving visual quality (chart looks wrong but math is correct):**
1. The problem is in skill_2 (wrong swings) or skill_3 Section 1-4 (wrong geometry)
2. Share the output and say: "The chart shape is wrong. Fix skill_2 and/or skill_3."

**Option D — Improving educational clarity (concept not obvious to viewer):**
1. The problem is in skill_3 Section 4 (pattern templates) or Section 7 (visibility checks)
2. Share the output and say: "A beginner cannot identify [concept]. Fix the pattern template."

---

## QUALITY STANDARDS — WHAT GOOD OUTPUT LOOKS LIKE

Use these benchmarks when reviewing chart output:

**Equal Lows:** Two touches visible at the same level. Bounces between them clearly visible (10+ pips). Level looks obviously horizontal.

**Sweep candle:** Wick dominates visually. Surrounding candles are clearly smaller. Wick is at least 2× the body.

**Displacement:** Visibly the largest movement on the chart. At least 3× larger than context candles. Decays naturally (large → medium → medium → small).

**Retracement:** Visibly slower and smaller than displacement. Mixed direction. Not a straight line. Ends near the zone.

**IDM fake bounce:** Looks like a real reversal starting. 2-3 clear bullish candles. Then fails and sweeps.

**BOS:** ONE candle body clearly closes beyond the structural level. Not explosive. Just a clean confirmation close.

**FVG:** Empty space clearly visible between candle 1 high and candle 3 low. At least 10 pips wide.

**OB zone and liquidity line:** Visible gap between them (10+ pips). They do not touch or overlap.

**Launch:** Price clearly reaches TP on screen. Proportional to displacement.

---

## NOTES FOR FUTURE IMPROVEMENT

These are known areas to improve as more material is added:

1. **CHoCH** — needs course section on change of character + examples
2. **Flip Setup** — needs examples of what a flip looks like (before/after)
3. **Multiple timeframe setups** — higher TF zone + lower TF entry
4. **Confluence setups** — OB + FVG + trendline all aligned
5. **Prop firm context** — setups common in FTMO/prop firm challenges
6. **Phase count calibration** — phase counts are currently fixed estimates.
   After running 20+ videos, review which phase counts produce the best-looking charts
   and update the Phase Structure table in skill_1_story_builder.md.
7. **Opus review pass** — once 50+ videos are generated, collect good and bad examples
   and ask Opus to identify hidden rules that distinguish them. Add those rules to skill_3.

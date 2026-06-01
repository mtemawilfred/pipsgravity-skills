# PIPSGRAVITY SKILLS IMPROVEMENT GUIDE
# Version 2.0 — June 2026
#
# PURPOSE: This document is both a reference and an active prompt.
# When Wilfred adds new material (course PDFs, strategy notes, video examples),
# paste this document + the new material into a session and say:
# "Update the skills using this guide."
# The model will know exactly what to add, where to add it, and how.

---

## WHAT THIS SYSTEM IS

The PipsGravity Chart Scene Pipeline converts a video idea into a rendered
educational forex video automatically. Four Claude calls, each with one job:

```
User Video Idea
      ↓
[CALL 1 — Haiku]
Text Generator
Outputs: hook_text, youtube_title, youtube_description
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
[LABEL ENGINE — n8n Code node]
Reads structural metadata, generates overlays deterministically
Labels fire at confirmed_at values — never on prediction
      ↓
[Parse & Validate — n8n Code node]
Mathematical checks (OHLC, EQL gap, BOS coherence, IDM position)
      ↓
Remotion Render → Final Video
```

---

## THE LABEL ENGINE — FIFTH COMPONENT

The Label Engine is an n8n Code node between Call 4 and Parse & Validate.
It is NOT a Claude call. It is deterministic JavaScript.

### WHY IT EXISTS
Claude generates candles and labels simultaneously. It knows the blueprint
says "equal lows at candle 17" and labels at candle 9. This is prediction,
not confirmation. The Label Engine fixes this by reading `confirmed_at` values
from the structures object and firing labels AFTER the structure is complete.

### LABEL LIFECYCLE RULES (enforced by code — not Claude)

| Structure | Label fires at | Condition |
|---|---|---|
| Equal Lows / Highs | touch_2_candle (confirmed_at) | Both touches must exist |
| FVG | candle_c (confirmed_at) | All 3 candles must close |
| Order Block | confirmed_at (after displacement) | FVG + BOS must exist |
| BOS | bos_candle (confirmed_at) | Body close must be confirmed |
| IDM unswept | bounce_start_candle | When fake bounce begins |
| IDM swept | confirmed_at (after reversal) | Reversal must be complete |
| Trade Setup | launch_candle | After IDM confirmed |

### LABEL NAMING STANDARD (in code — not in Claude prompts)

| Concept | Label (unswept) | Label (swept) |
|---|---|---|
| Equal Lows | $$$ EQUAL LOWS | $$$ EQUAL LOWS SWEPT |
| Equal Highs | $$$ EQUAL HIGHS | $$$ EQUAL HIGHS SWEPT |
| Trendline LQ | $$$ TRENDLINE LQ | $$$ TRENDLINE LQ SWEPT |
| Range LQ | $$$ RANGE LQ | $$$ RANGE SWEPT |
| IDM | $$$ IDM | IDM SWEPT |
| OB | ORDER BLOCK | — |
| Demand Zone | VALID DEMAND ZONE | — |
| Supply Zone | VALID SUPPLY ZONE | — |
| FVG | FVG CREATED | — |
| BOS | BOS CONFIRMED | — |

### ADDING A NEW CONCEPT TO THE LABEL ENGINE

When adding any new concept to the skills, add a section to the Label Engine
code node following this template:

```javascript
// ── [CONCEPT NAME] ───────────────────────────────────────────────────────
const newConcept = structures.new_concept_key;
if (newConcept && [applicable setup type conditions]) {
  overlays.push({
    type:        '[overlay_type]',
    // ... overlay fields ...
    start_ms:    msAt(newConcept.confirmed_at)  // ALWAYS fires at confirmed_at
  });
}
```

Rules for every new concept:
1. Use `confirmed_at` for `start_ms` — never the structure's first candle
2. Use the label name from the naming standard table above
3. Follow teaching order: evidence before conclusion
4. IDM equivalent concepts: swept:false and swept:true must use identical price_level
5. Anti-overlap: no two candle_labels on same candle, same side

### WHAT CALL 4 MUST OUTPUT FOR EACH NEW CONCEPT

Add a structures schema entry to Skill 3's output format section:
```json
"new_concept_key": {
  "anchor_candle": <where the structure is located>,
  "confirmed_at":  <the candle index when the structure becomes valid>,
  "price_level":   <or price_top/price_bottom as needed>
}
```

No labels. No start_ms. Only facts about where structures live.

---

## THE VALIDITY HIERARCHY — CORE PRINCIPLE

Every concept in this system has three layers:
1. Its own validity conditions (what makes IT valid)
2. Its connection requirements (what must exist before it)
3. Its connection outputs (what it enables after it)

A concept cannot appear on a chart unless ALL its conditions are met.
Concepts build on each other in a chain. The chain is the setup.

EXAMPLE — Bullish Order Block chain:
  Liquidity (EQL valid) →
  Sweep (sweep conditions met) →
  OB candle (sits below swept level, 10+ pip gap) →
  FVG (originates from OB candle, 10+ pip gap) →
  BOS (body close, 5-15 pips above structural high) →
  IDM (inside FVG zone, 15+ pips above OB, convincing bounce) →
  Entry (at zone midpoint)

If ANY link in this chain fails its conditions, the concept label must not appear.

This hierarchy must be maintained in every skill file and in every new concept added.

---

## THE FOUR SKILL FILES

---

### FILE 1: forex_concepts.md
**Role:** Concept dictionary with validity hierarchy.
**Fed to:** Call 2 (Haiku story builder)

STRUCTURE PER CONCEPT (every concept must follow this format):
  DEFINITION — what it is in plain English
  VALID CONDITIONS — ALL must be met (numbered list)
  INVALID CONDITIONS — what disqualifies it
  CONNECTS TO — what it requires before it, what it enables after

**Currently contains (with validity hierarchy):**
  Market Structure, Liquidity (EQL/EQH/Trendline/Range + Sweep), Order Block,
  Fair Value Gap, BOS, IDM, CHoCH*, Flip*, Continuation*, Equilibrium*
  (* = defined but NOT READY for generation)

**When adding a new concept:**
  Add a section following the exact format above.
  Include the validity chain: what must exist before this concept, what it enables.
  Mark as READY or NOT READY in the available setups table at the bottom.

---

### FILE 2: skill_1_story_builder.md
**Role:** Teacher brain. Classify → select setup → build blueprint.
**Fed to:** Call 2 alongside forex_concepts.md

**Currently supports:**

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

**When adding a new concept:**
  1. Add keywords to Concept Classifier table
  2. Add setup_type to Setup Selection table
  3. Write the narrative sequence (story beats in order) — must follow validity chain
  4. Add phase counts to Phase Structure table
  5. Define required and optional components
  6. Update status to ✅ READY

---

### FILE 3: skill_2_skeleton_builder.md
**Role:** Chart artist (shape level). Convert blueprint into visual structure.
**Fed to:** Call 3

**Contains:**
  Market Rhythm Library (visual shapes per concept)
  Setup Skeleton Library (phase → rhythm mapping per setup_type)
  Swing Generator (swing types, sizes, decay patterns)
  Teaching Clarity Rules (6 rules for visual quality)
  Event Mapping (story beat → phase)

**When adding a new concept:**
  1. Add rhythm to Market Rhythm Library (ASCII shape + swing sequence + rules)
  2. Add skeleton to Setup Skeleton Library (phase → rhythm per new setup_type)
  3. Add new event → phase mappings to Event Mapping
  4. Add teaching clarity rule if concept has unique visibility requirements

---

### FILE 4: skill_3_chart_grammar.md
**Role:** Chart grammar engine. Convert skeleton into OHLC + overlays.
**Fed to:** Call 4

**Contains:**
  Educational Chart Philosophy (educational realism > market realism)
  Validity Hierarchy Rule (every concept must meet its conditions before labelling)
  Market Geometry Rules (push/pullback, candle sizes, decay)
  Candle Generation Rules (OHLC validity, generation process)
  Pattern Templates (per concept: exact candle structure + verify conditions)
  Overlay Grammar (placement rules, label names, anti-overlap rules)
  Mathematical Validation (all checks, all FAIL actions)
  Educational Visibility Validation (10 yes/no questions)

**LABEL NAMING STANDARD (must be followed for all concepts):**
  Macro liquidity: "$$$ EQUAL LOWS" / "$$$ EQUAL HIGHS" / "$$$ TRENDLINE LQ" / "$$$ RANGE LQ"
  Macro swept: "$$$ EQUAL LOWS SWEPT" / "$$$ EQUAL HIGHS SWEPT" / "$$$ TRENDLINE LQ SWEPT"
  IDM unswept: "$$$ IDM"
  IDM swept: "IDM SWEPT"
  Never use: "$$$ ENTRY LQ" or "LQ SWEPT — ENTRY" — these are generic and non-educational

**ANTI-OVERLAP RULES (must be respected for all concepts):**
  1. One candle_label per candle per side
  2. OB candle: only "OB CANDLE" label (no separate "LIQUIDITY SWEEP" on same candle)
  3. IDM swept:false and swept:true MUST have identical price_level
  4. EQL swept:false and swept:true MUST have identical price_level
  5. EQL candle_start = Phase B start (P&V Step 10n corrects timing via start_ms)

**When adding a new concept:**
  1. Add Pattern Template (candle structure + mathematical check + visibility rule)
  2. Add overlay teaching order to the relevant setup type
  3. Add label name to the naming standard above
  4. Add any new mathematical check to Section 6
  5. Add any new visibility question to Section 7 if needed

---

## HOW TO ADD A NEW CONCEPT — STEP BY STEP

When Wilfred has new material (PDF, notes, video examples):

### STEP 1 — Understand the validity chain
Before touching any file, answer these questions:
  - What conditions make this concept VALID?
  - What must exist BEFORE this concept (what it requires)?
  - What does this concept ENABLE (what comes after it)?
  - How does it connect to adjacent concepts in the chain?

### STEP 2 — Update forex_concepts.md
Add section with: DEFINITION, VALID CONDITIONS, INVALID CONDITIONS, CONNECTS TO.
Update the available setups table.

### STEP 3 — Update skill_1_story_builder.md
Add: keyword mapping, setup selection, narrative sequence (following validity chain),
phase counts, required/optional components. Update status to ✅ READY.

### STEP 4 — Update skill_2_skeleton_builder.md
Add: rhythm shape, swing sequence, skeleton (phase → rhythm), event mapping.

### STEP 5 — Update skill_3_chart_grammar.md
Add: pattern template with conditions, overlay rules, label name, math check, visibility check.

### STEP 6 — Update this guide
Update concept readiness tracker. Add any new label naming standard. Document the validity chain.

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

---

## IMPROVEMENT SESSION TYPES

**Option A — Add a new concept:**
  1. Paste this guide + new material
  2. Say: "Add [concept] to all skills using this guide"
  3. Review additions before pushing to GitHub

**Option B — Fix bad chart output:**
  - Wrong shape → fix skill_2 skeleton or rhythm
  - Wrong candles → fix skill_3 pattern template
  - Label in wrong place → fix skill_3 overlay placement rules + P&V
  - Wrong label name → fix skill_3 label naming standard + P&V Step 10n-ii
  - Concept conditions not met → fix skill_3 mathematical validation

**Option C — Improve visual quality:**
  - Concept not obvious → fix skill_3 Section 7 visibility check + pattern template
  - Labels overlapping → fix skill_3 anti-overlap rules + P&V

---

## QUALITY STANDARDS

**Equal Lows:** flat horizontal line, 2 touches within 3 pips, 10+ pip bounce between.
**Sweep:** wick >= 2× body, dominates visually over surrounding candles.
**OB candle:** bearish (bullish setup), sits 10+ pips below swept EQL level.
**Displacement:** 3x+ larger than context, decaying (large→medium→medium→small).
**FVG:** 10+ pip gap from OB candle high visible as empty space.
**BOS:** body close 5-15 pips beyond structural high. One candle. Stops.
**IDM:** inside FVG zone, 15+ pips above OB, 2-3 convincing bullish candles.
**Retracement:** slower and smaller than displacement. Mixed direction.
**Launch:** proportional to displacement. Last candle reaches TP.
**Entry:** at zone midpoint (50% level), never at the top edge.

---

## KNOWN FUTURE IMPROVEMENTS

1. CHoCH — needs course section + visual examples
2. Flip Setup — needs before/after examples
3. Multi-timeframe setups — higher TF zone + lower TF entry
4. Confluence setups — OB + FVG + trendline aligned
5. Phase count calibration — review after 20+ videos, update Phase Structure table
6. Opus review pass — after 50+ videos: good vs bad examples → Opus finds hidden rules
7. Equilibrium entry — apply when OB wick > 50% of total candle range
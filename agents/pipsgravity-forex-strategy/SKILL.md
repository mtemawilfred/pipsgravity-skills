---
name: pipsgravity-forex-strategy
description: "PipsGravity forex strategy skill for CHART_SCENE video generation. Mathematical verification, candle sequences, teaching-order rules. Sources: PipsGravity Academy Course, Pietrus-914/skills-repo, pattern-recognition skill."
---

## MATHEMATICAL SOURCE REFERENCES

**Source 1 — Pietrus-914/skills-repo**
File: forex-trading-expert/references/smart-money-concepts.md
Defines: Order Block detection, FVG detection, BOS/CHoCH detection, Liquidity levels
Key insight: All conditions written as executable MQL5 and Pine Script detection code.

**Source 2 — Pattern Recognition Skill**
Defines: Failed Breakout (Liquidity Grab), Breakout and Retest, Double Top/Bottom,
Support/Resistance Flip
Key insight: Liquidity grab = wick through + close back inside = mathematically
distinct from BOS.

**Source 3 — PipsGravity Academy Course**
The teaching authority. All concept definitions, validity conditions, and teaching
sequences come from here. The skill must teach exactly what this course teaches.

---

## WHO THIS IS FOR

This skill teaches Claude the PipsGravity forex strategy for generating educational
CHART_SCENE videos. Every rule comes directly from the PipsGravity Academy course
and the mathematical references above. Follow every rule precisely.

---

## VIDEO STRUCTURE

**HOOK:** Static hook_text visible from frame 1. Candles also start at frame 1 (start_ms=0).
**CHART:** Candles draw one by one from frame 1. Overlays appear in teaching order.

The chart teaches. hook_text is the only text the viewer reads.

---

## VIDEO TYPES — CHOOSE ONE BEFORE GENERATING ANYTHING

Every CHART_SCENE video is one of three types. Decide which type before writing
a single candle. This determines what phases are required and whether trade_setup
is included.

### TYPE 1 — CONCEPT ONLY
**What it teaches:** What one concept IS — how it forms, what it looks like, why it matters.
**No entry. No SL. No TP. No trade_setup overlay.**
The video ends when the concept has been shown and labelled.

Examples:
- "This is what equal lows look like and how they get swept"
- "This is what a Fair Value Gap is"
- "This is what an Order Block looks like before it gets tested"

Required phases: Whatever the concept needs to be visible. Nothing more.
Required overlays: Concept labels only. No trade_setup.

### TYPE 2 — FULL SETUP
**What it teaches:** How to identify AND enter a trade using one or more concepts.
**Includes entry, SL, TP, and trade_setup overlay.**
**HARD RULE: Liquidity MUST be swept before the trade_setup appears.**

The entry must have proof of liquidity being taken. Without this, the setup
has no fuel — it is incomplete and the video cannot show a valid trade.

Required overlays: All concept labels PLUS at least one `liquidity` overlay
with `swept: true` appearing before the `trade_setup`.

### TYPE 3 — COMBINATION
**What it teaches:** How two or more concepts connect to form a complete picture.
Can be concept-only or include a full setup.

Examples:
- "Equal lows form → get swept → OB is created → BOS confirms → entry at OB"
- "How liquidity + FVG + BOS work together"

Use the full phase structure. Label each concept as it appears.
If showing an entry: same rule as TYPE 2 — liquidity must be swept first.

---

## THE LIQUIDITY-BEFORE-ENTRY RULE (Hard Rule — TYPE 2 and TYPE 3 with entry)

**This rule applies to every full-setup video without exception.**

Before price launches from any zone or order block, liquidity must be swept.
This is not optional. An entry without a prior liquidity sweep is an incomplete
setup — it has no institutional backing and will not sustain.

**What "liquidity swept before entry" means:**
At the moment the trade_setup appears, at least ONE of these must already be
visible with swept: true in the overlays:

Option A — MACRO liquidity swept:
  The original equal highs/lows (Phase B) were swept during the impulse (Phase D).
  A `liquidity` overlay with swept: true must appear after the impulse candles.

Option B — ENTRY liquidity swept (IDM):
  The inducement pattern formed during Phase F and the IDM low was swept.
  A `liquidity` overlay with swept: true must appear before Phase G launch.

Option C — BOTH (strongest setup):
  Macro LQ swept at Phase D + IDM entry LQ swept at Phase G entry.
  Show both swept overlays.

**The IDM is Option B — it is one way to satisfy the liquidity rule.**
It is not the only way. Consolidation lows near the OB, equal lows during
retrace, any identifiable liquidity pool that gets swept before launch all
count. The requirement is that SOME liquidity was taken and SHOWN with a
swept: true overlay before the trade_setup appears.

**If the candle data does not contain a liquidity sweep before Phase G:**
Add candles to Phase F that show an IDM pattern, or adjust Phase D to
confirm the macro liquidity was swept. Do not place a trade_setup without
at least one swept: true liquidity overlay appearing first.

---

## SCHEMA

```json
{
  "scene_id": 1,
  "render_type": "CHART_SCENE",
  "duration_ms": <calculated — see formula>,
  "hook_text": "<one punchy line>",
  "brand": {
    "primary": "#1B2A4A", "accent": "#C9A84C",
    "danger": "#991B1B", "success": "#166534",
    "font_heading": "Oswald", "font_body": "Inter"
  },
  "chart": {
    "start_ms": 0,
    "candles": [ { "o": number, "h": number, "l": number, "c": number } ],
    "candle_interval_ms": <400 minimum>,
    "visible_count": <MUST equal candles.length>,
    "background": "white",
    "bullish_color": "#26a69a",
    "bearish_color": "#ef5350"
  },
  "overlays": [ ... ],
  "assets": { "sound_effects": [] },
  "transition_in": { "type": "fade", "duration_ms": 300 },
  "transition_out": { "type": "fade", "duration_ms": 300 }
}
```

**Duration formula:**
```
duration_ms = (candles.length × candle_interval_ms) + 4000
chart.start_ms = 0  (candles start immediately on frame 1 — no delay)
```

---

## CANDLE COUNT RULE — DYNAMIC PHASE-BASED

This is how you decide how many candles to generate.
The count is not a fixed number — it is determined by the concept's required phases.

**Maximum: 60 candles. Minimum: enough to complete all required phases.**

**Before generating any candles, calculate your target count:**

```
STEP 1: Identify which phases this concept requires.
STEP 2: Assign a candle count to each phase using these ranges:

  Phase A — Context/Trend:        4-8  candles
  Phase B — Liquidity Formation:  3-5  candles
  Phase C — Base / OB Zone:       1-3  candles
  Phase D — Impulse/Expansion:    3-6  candles
  Phase E — BOS Confirmation:     1-2  candles
  Phase F — Retrace to Zone:      4-10 candles  (include only if concept needs it)
  Phase G — Continuation:         2-5  candles  (include only if concept needs it)

STEP 3: Sum the phases. That is your candle count.
STEP 4: Check: is it above 60? Trim Phase F or G first.
         Is it below 10? The concept is not fully represented — add context.
```

**Rules:**
- Do NOT add candles after the concept story is complete
- Do NOT pad with drifting price to reach a round number
- Do NOT stop before all required phases are shown
- A simple FVG video may need 15 candles. A full demand zone with retrace may need 50. Both are correct if the phases are complete.
- visible_count MUST equal candles.length — all candles visible on screen simultaneously for full context
- candle_interval_ms minimum 400ms. Recommended 500ms for most concepts.

---

## OHLC VALIDITY — EVERY CANDLE

```
h >= max(o, c)   AND   l <= min(o, c)
```
Verify every candle before outputting.
If violated, fix h upward or l downward — do not leave it broken.

---

## THE CORE RULE — MATHEMATICAL VERIFICATION BEFORE LABELING

After generating your candle array, BEFORE placing any overlay, run the
mathematical check for that overlay against your actual candle data.

If the check FAILS — the condition does not exist in your data.
Either adjust the candle prices until the condition is TRUE,
or do not place the label.

Never label a pattern that is not mathematically present in the candle array.

---

## MATHEMATICAL CONDITIONS — REFERENCE

### FVG (Fair Value Gap)

**From Pietrus-914 SMC reference:**
```
Bullish FVG — condition between candle X and candle X+2:
  candles[X+2].l > candles[X].h

  FVG box:
    price_top    = candles[X+2].l
    price_bottom = candles[X].h
    candle_start = X+1

Bearish FVG:
  candles[X+2].h < candles[X].l

  FVG box:
    price_top    = candles[X].l
    price_bottom = candles[X+2].h
    candle_start = X+1
```

**Self-check:**
```
IF candles[X+2].l <= candles[X].h → NO FVG. Adjust prices. Do not place label.
IF candles[X+2].l >  candles[X].h → FVG confirmed. Place fvg overlay.
```

---

### BOS (Break of Structure)

**From Pietrus-914 SMC reference:**
```
Bullish BOS: candles[N].c > structural_high
  The candle BODY closes above. A wick through is NOT a BOS.

Bearish BOS: candles[N].c < structural_low
```

**Self-check:**
```
structural_high = highest high of Phase A candles — the specific candle that set the high
structural_high_candle_index = that candle's index (for candle_start field)
price_level = candles[structural_high_candle_index].h

IF candles[bos_index].c <= price_level → NOT a BOS. Raise candles[bos_index].c.
IF candles[bos_index].c >  price_level → BOS confirmed.

bos_label overlay:
  candle_start = structural_high_candle_index   (left anchor of line)
  candle_index = bos_index                      (right anchor, where line ends)
  price_level  = candles[structural_high_candle_index].h
```

---

### Equal Highs / Equal Lows (Liquidity)

**From Pattern Recognition skill (Tweezer detection) + SMC reference:**
```
Equal Highs: abs(candles[A].h - candles[B].h) <= 0.0003  (within 3 pips)
Equal Lows:  abs(candles[A].l - candles[B].l) <= 0.0003

price_level for overlay = candles[A].h (or .l for lows)
```

---

### Trendline Liquidity

**Mathematical formula (derived from two anchor candle/price points):**
```
anchor_1: candle_A (first swing point index), price_A (price at that swing)
anchor_2: candle_B (second swing point index), price_B (price at that swing)

slope = (price_B - price_A) / (candle_B - candle_A)
price_at_candle_N = price_A + slope × (N - candle_A)

Touch validation — every subsequent touch candle must be within 5 pips:
  Down trendline (connecting lower highs):
    abs(candles[N].h - price_at_candle_N) <= 0.0005
  Up trendline (connecting higher lows):
    abs(candles[N].l - price_at_candle_N) <= 0.0005

Sweep confirmation:
  Down trendline swept: candles[sweep].h > price_at_candle_sweep
                    AND candles[sweep].c < price_at_candle_sweep
  Up trendline swept:   candles[sweep].l < price_at_candle_sweep
                    AND candles[sweep].c > price_at_candle_sweep
  Wick size through trendline >= 0.0005 (5 pips minimum)
```

**Self-check before placing trendline overlay:**
```
STEP 1: Choose anchor_1 (first swing high/low touching the line)
STEP 2: Choose anchor_2 (second swing high/low)
STEP 3: Calculate slope
STEP 4: For every subsequent touch candle, verify:
         price_at_that_candle = price_A + slope × (candle_N - candle_A)
         abs(candles[N].h or .l - price_at_candle_N) <= 0.0005
         If FALSE: adjust candle price or choose different anchors
STEP 5: Sweep candle: h or l crosses the trendline, body closes back
```

**Overlay schema:**
```json
{
  "type": "trendline",
  "candle_start": <candle index of anchor 1>,
  "price_start": <price at anchor 1>,
  "candle_end": <candle index of anchor 2>,
  "price_end": <price at anchor 2>,
  "extend_to": <optional: candle index to extend line to>,
  "direction": "down",
  "swept": false,
  "label": "$$$ TRENDLINE LQ",
  "start_ms": <after anchor 2 candle draws>
}
```
When swept: change swept to true on a second overlay at the sweep candle timing.

**Self-check:**
```
IF abs(candles[A].h - candles[B].h) > 0.0003 → NOT equal. Adjust prices.
IF abs(candles[A].h - candles[B].h) <= 0.0003 → Equal highs confirmed. Place liquidity overlay.
```

### Entry Liquidity (from Mastermind Trading Plan)

Entry liquidity is a SECOND smaller liquidity pool that forms during the
retrace (Phase F) near the OB/demand zone. It is separate from the macro
equal highs/lows that set up the original move.

**What it is:**
As price retraces toward the OB, the IDM (inducement) pattern forms:
2-3 candles with equal lows. Retail traders who see price approaching a
"support level" place their buy stops below those lows.

Price then wicks BELOW those equal lows (sweeping the entry liquidity)
before closing back above and launching. The wick is the entry signal.
This is called "liquidity for entry" in the Mastermind Trading Plan.

**Mathematical conditions:**
```
Entry LQ level = IDM_low (the specific low formed during the fake bounce)
  abs(candles[A].l - candles[B].l) <= 0.0003
  IDM_low = candles[A].l
  Sits 0-5 pips above OB_bottom

Entry LQ sweep — Phase G first candle MUST be a wick candle:
  candles[phase_g_start].o > IDM_low  (opens above — no gap down)
  candles[phase_g_start].l < IDM_low  (wick pierces below)
  candles[phase_g_start].c > IDM_low  (body closes ABOVE — not a breakdown)
  wick_size = IDM_low - candles[phase_g_start].l >= 0.0005 (5+ pips)
  body_size = candles[phase_g_start].c - candles[phase_g_start].o (small positive)
  The wick must be clearly visible — larger than the body.

This sweep is the entry confirmation.
If NO entry liquidity forms: the launch is still valid but the signal is weaker.
```

**Two types of liquidity in every OB/demand zone setup:**
1. MACRO liquidity — the original equal highs/lows that set up the whole move
2. ENTRY liquidity — the IDM low formed during Phase F retrace (inducement pattern)

---

### THE GOLDEN RULE — Every Entry Needs Liquidity Taken First

**Source: PipsGravity Liquidity and Manipulation document**

Price needs FUEL to move. Liquidity is that fuel. Before every large move,
liquidity must be swept. Without sweeping liquidity first, a move will not
sustain or may not happen at all.

**The rule stated simply:**
Before entering at an OB or demand zone, liquidity must have been swept
either at the macro level (the EQH/EQL that set up the original move) OR
at the entry level (the small equal lows near the OB during retrace) OR BOTH.

If no liquidity was swept before price launches from the zone, the setup is
incomplete. Do not label it as a confirmed entry.

---

### LIQUIDITY TYPES — Full Reference (from PipsGravity Academy + Types of Liquidity doc)

**WHY LIQUIDITY EXISTS:**
Institutions (large banks, hedge funds) have position sizes too large to fill at a
single price without a counterpart. They NEED retail stop losses to fill their orders.
Every liquidity type is a different way retail creates clustered stops that institutions
use as fuel. Price does not move impulsively without first collecting liquidity.
"Price has fuel to move" = stops have been swept = institutional orders are now filled.

---

**TYPE 1 — TRENDLINE LIQUIDITY**

How it forms:
  An uptrend or downtrend trendline forms with 3+ touches (wicks meeting the line).
  This is not coincidence — institutions know retail watches trendlines.
  Retail traders on an uptrend trendline: buy on each touch, stops below the trendline.
  Retail breakout traders: place orders on a trendline break, stops just inside.
  Both groups create clustered liquidity AT and AROUND the trendline.

What happens:
  Price sweeps the trendline — breaks through it briefly with a wick — taking all
  those stops from both the bounce traders AND the breakout traders.
  After the sweep: momentum in the opposite direction proportional to liquidity collected.

Mathematical representation:
  Two anchor points define the trendline:
    anchor_1: (candle_A, price_A) — first swing high or low touching the line
    anchor_2: (candle_B, price_B) — second touch (later candle)
  slope = (price_B - price_A) / (candle_B - candle_A)
  price_at_candle_N = price_A + slope × (N - candle_A)

  Touch validation (each subsequent touch must be close to the trendline):
    For down trendline: abs(candles[N].h - price_at_candle_N) <= 0.0005
    For up trendline:   abs(candles[N].l - price_at_candle_N) <= 0.0005

  Sweep confirmation:
    Down trendline swept: candles[N].h > price_at_candle_N AND candles[N].c < price_at_candle_N
    Up trendline swept:   candles[N].l < price_at_candle_N AND candles[N].c > price_at_candle_N

Overlay: use `trendline` overlay type with candle_start, price_start, candle_end, price_end.
  swept: false while forming, swept: true after the sweep candle.

---

**TYPE 2 — RANGE LIQUIDITY (Support and Resistance)**

How it forms:
  Price bounces between two horizontal levels multiple times (2-3+ touches each side).
  Buyers at support: stops placed below support.
  Sellers at resistance: stops placed above resistance.
  Both sides accumulate. The more touches, the more stops.

What happens:
  Price breaks below support (sweeping buy stops) then reverses upward — OR
  Price breaks above resistance (sweeping sell stops) then reverses downward — OR
  Both: sweeps below support then above resistance (or vice versa), collecting from
  both sides before the real directional move.
  Maximum fuel = both sides swept = largest expected momentum.

Visual pattern on chart:
  Horizontal consolidation with clear top and bottom levels.
  Equal highs at resistance = $$$ above.
  Equal lows at support = $$$ below.
  Sweeps show as wicks through one or both levels.

Overlay: use `liquidity` overlay for each level (swept: false then swept: true).

---

**TYPE 3 — EQUAL HIGHS / EQUAL LOWS (EQH / EQL)**

How it forms:
  Two or more highs at virtually the same price level (double/triple tops) = EQH.
  Two or more lows at virtually the same price level (double/triple bottoms) = EQL.
  These are classic retail patterns — every trader knows them.
  Sellers at EQH put stops above the double top.
  Buyers at EQL put stops below the double bottom.
  That common knowledge IS what makes them liquidity traps.

What happens:
  Price wicks through the level — taking the stops — and closes back on the other side.
  The wick IS the sweep. The body closing back confirms it.
  After the sweep: impulsive move in the opposite direction.

  Key insight from the document: a wick on the 1H timeframe is an OB or supply/demand
  zone on the 15M timeframe. Trendline and EQH/EQL sweeps create zones on lower TFs.

Mathematical check:
  EQH: abs(candles[A].h - candles[B].h) <= 0.0003 (within 3 pips)
  EQL: abs(candles[A].l - candles[B].l) <= 0.0003

  Sweep (EQH):  candles[N].h > EQH_level AND candles[N].c < EQH_level
  Sweep (EQL):  candles[N].l < EQL_level AND candles[N].c > EQL_level

Overlay: use `liquidity` overlay with swept: false → swept: true.

---

**TYPE 4 — ENTRY LIQUIDITY (IDM — near the OB/Zone)**

How it forms:
  During Phase F retrace (after 60%+ of the retrace is complete), price forms a small
  manipulation move near the OB — the Inducement (IDM).
  Creates a specific low (for buys) or high (for sells) that traps early entries.
  Those early traders put stops just below the IDM low.

What happens:
  Price wicks below the IDM low (taking those entry stops) and closes back above.
  This is the final fuel collection before the real launch from the OB.
  The sweep IS the entry signal from the Mastermind Trading Plan.

Mathematical check:
  candles[sweep].l < IDM_low AND candles[sweep].c > IDM_low
  wick = IDM_low - candles[sweep].l >= 0.0005 (5 pips minimum)

---

**COMBINED LIQUIDITY MOVES:**
The most powerful moves happen when MULTIPLE liquidity types are swept:
  EQL swept at macro level (Phase B) → OB formed → BOS → EQL near OB (IDM) swept
  Each additional sweep = more fuel = stronger sustained move.
  A move backed by only one liquidity sweep is weaker than one backed by two or three.

---

### Liquidity Sweep — What Makes It Valid

**From PipsGravity Liquidity and Manipulation document:**

A valid sweep requires all three:
1. Price reaches the liquidity level (wick touches or exceeds it)
2. Candle body closes on the OPPOSITE side of the level (does NOT continue)
3. Next 1-2 candles move impulsively in the opposite direction (momentum)

If any one of these is missing, it is NOT a confirmed sweep.

The size of the wick matters:
- A large wick (10+ pips above/below the level) = strong sweep, high momentum expected
- A tiny wick (2-3 pips) = weak sweep, momentum may be weaker

**Mathematical check for a valid sweep:**
```
For bullish setup (sweeping sell-side EQL):
  sweep candle: l < EQL_level AND c > EQL_level
  wick size: EQL_level - candle.l >= 0.0005 (at least 5 pips)
  confirmation: next candle is bullish and closes above sweep candle open
```

---

### Liquidity Sweep (Grab — NOT a BOS)

**From Pattern Recognition skill (Failed Breakout):**
```
Grab = ALL THREE conditions true for spike candle:
  1. candles[spike].h > equal_high_level     (wick broke above)
  2. candles[spike].c < equal_high_level     (body closed back below)
  3. candles[spike].h - candles[spike].c > 0.0015  (significant upper wick)

BOS = candles[N].c > structural_high        (body CLOSED above — continuation)

A wick above + close below + reversal = GRAB
A close above + continuation           = BOS
Never confuse them.
```

**Self-check:**
```
IF spike.h <= equal_high_level → did not reach the level. Not a sweep.
IF spike.c >= equal_high_level → closed above — this is BOS not grab.
IF spike.h > equal_high_level AND spike.c < equal_high_level → SWEEP confirmed.
```

---

### Order Block

**From Pietrus-914 SMC reference (FindBullishOB):**
```
Bullish OB:
  candles[ob_index].c < candles[ob_index].o   (it is bearish)
  It is the LAST bearish candle before the bullish impulse
  Impulse size: candles[ob_index+2].h - candles[ob_index].l >= 0.0020

  OB box — uses FULL candle range including wicks:
    price_top    = candles[ob_index].h  ← full candle high
    price_bottom = candles[ob_index].l  ← full candle low
    candle_index = ob_index
    This marks the entire candle from wick to wick — not just the body.
    The full range shows the complete area where institutional orders exist.

  VISUAL SEPARATION FROM LIQUIDITY LINE:
    The liquidity line and the OB box must NOT overlap.
    liquidity candle_end = ob_index - 1  (line stops one candle before the OB)
    The OB box then sits cleanly to the right of the liquidity line.
    This makes both elements clearly readable on screen.

  direction field in overlay:
    direction = the colour of the OB candle itself, not what it signals
    Bullish OB (last BEARISH candle before bullish impulse) → direction: "bearish"
    Bearish OB (last BULLISH candle before bearish impulse) → direction: "bullish"
    This controls the box colour in the renderer — bearish = red box, bullish = blue box.
    A red box on the last red candle is correct. Do not set direction to match the trade.

Bearish OB:
  candles[ob_index].c > candles[ob_index].o   (it is bullish)
  It is the LAST bullish candle before the bearish impulse
```

**Self-check:**
```
IF candles[ob_index].c > candles[ob_index].o → NOT bearish. Find last bearish candle.
IF impulse_move < 0.0020 → Impulse too small. Make impulse candles larger.
IF both pass → OB confirmed. BOS must also exist after impulse. Place order_block overlay.
```

---

## GENERATION WORKFLOW — FOLLOW IN ORDER

```
STEP 1: Write hook_text — one punchy line, max 12 words

STEP 2: Identify required phases for this concept
        Calculate candle count from phase ranges
        Confirm total is between 10 and 60

STEP 3: Generate candle prices phase by phase
        Context: 5-12 pip bodies
        Impulse: 30-60 pip bodies (3-5x larger than context)

STEP 4: RUN MATHEMATICAL VERIFICATION on your array:
        FVG check    → if concept needs FVG
        BOS check    → if concept needs BOS
        Equal highs  → if concept needs liquidity
        OB check     → if concept needs order block
        Sweep check  → if liquidity sweep concept
        Adjust prices until ALL checks PASS

STEP 5: Calculate candle finish times
        Candle N finishes at: (N+1) × candle_interval_ms

STEP 6: Place overlays in teaching order
        Evidence labels first, concept label last
        Each overlay start_ms >= that candle's finish time
        Minimum 800ms gap between consecutive overlays

STEP 7: Calculate duration_ms = (candles.length × candle_interval_ms) + 4000
chart.start_ms = 0  (candles start immediately on frame 1 — no delay)

STEP 8: Run final checklist
```

---

## CONCEPT 1 — VALID DEMAND / SUPPLY ZONE

### What Makes It Valid (ALL 3 required)
1. Created a Fair Value Gap — verify FVG check
2. Broke structure — verify BOS check (CLOSE, not wick)
3. Created or swept liquidity — verify equal lows check

### Phase Breakdown — Bullish Demand Zone

```
Phase A0 — Rising Context before Swing High (3-5 candles):
  Small bullish candles rising toward the peak. Bodies 5-10 pips each.
  Shows the viewer the uptrend that created the structural high.

Phase A — Downtrend from Swing High (4-6 candles):
  Small bearish candles falling after the peak. Bodies 5-12 pips each.
  VERIFY: structural_high_candle_index >= 1 (never 0)

Phase B — Equal Lows / Liquidity (4-8 candles):
  EQUAL LOWS PATTERN — 2-3 touches with bounces between them:
    Touch 1: price drops to level → small bounce up (3-8 pips)
    Touch 2: price returns to same level → small bounce up again
  Each bounce is visible. Lows within 3 pips of each other.
  VERIFY: abs(candles[touch1].l - candles[touch2].l) <= 0.0003

Phase C — Base / OB Zone (1-3 candles):
  Very small bodies. Institutional accumulation.
  Last bearish candle = the Order Block.
  VERIFY OB: candles[last_base].c < candles[last_base].o

Phase D — Impulse (3-6 large bullish candles):
  Bodies 30-60 pips. Must leave FVG.
  VERIFY FVG: candles[last_base + 3].l > candles[last_base].h

Phase E — BOS (1-2 candles):
  The BOS candle closes ABOVE the structural high — that is all that is needed.
  Phase E should be short. 1 BOS candle + 1 small continuation candle maximum.
  The BOS candle does NOT need to be explosive. A candle that closes 5-15 pips
  above the structural high is sufficient — the close is the confirmation.
  After the close above, 1 optional follow-through candle (5-12 pip body) settles
  the move. Phase E then ends. Phase F retrace begins immediately.
  Do NOT add multiple large explosive candles continuing far above the structural high.
  VERIFY: candles[bos_index].c > Phase A structural high

Phase F — Retrace (4-10 candles):
  Price returns toward the demand zone.
  REQUIRED: The last candle of Phase F must close within 10 pips of the zone.
    candles[last_retrace].c must be between zone_bottom and zone_bottom + 0.0010
  If the retrace candles do not reach within 10 pips of the zone, add more
  retrace candles until this condition is met. Do not end Phase F early.
  End with a small bounce or pause at the zone level to show the entry area.

Phase G — Launch from Zone to TP (3-8 candles):
  Price touches the demand zone and launches all the way to the structural high.
  trade_setup candle_start = first Phase G candle.

  First candle: opens inside or at zone, closes above zone top.
  Remaining candles: bullish expansion, body range 20-40 pips each.

  REQUIRED — PRICE MUST REACH THE TP:
    Last Phase G candle must close AT or ABOVE tp_price.
    tp_price = max high of Phase D + Phase E impulse candles (the peak of the BOS move).
    VERIFY: candles[last_phase_g].c >= tp_price
    If FALSE: add more bullish expansion candles until TRUE.
```

### Overlay Teaching Sequence

MANDATORY ORDER: Evidence before conclusion. Never label the zone before showing the evidence.

```
1. After Phase B last candle → liquidity
   label: "$$$ EQUAL LOWS", swept: false
   price_level: candles[last_equal_low].l
   PURPOSE: show stop cluster location first

2. After Phase D third candle → fvg
   label: "STEP 1: FVG ✓"
   price_top: candles[last_base + 3].l
   price_bottom: candles[last_base].h
   PURPOSE: first evidence confirmed

3. After Phase E BOS candle → bos_label
   label: "STEP 2: BOS ✓", direction: "up"
   candle_start: Phase A candle with MAX(h) — NOT always candle 0
   candle_index: bos_index
   price_level: candles[candle_start].h
   PURPOSE: second evidence confirmed

4. 800ms after BOS → floating_label
   text: "STEP 3: LIQUIDITY ✓"
   PURPOSE: third evidence confirmed — all three conditions met

5. 800ms after step 4 → demand_zone (THE CONCLUSION — last concept label)
   label: "VALID DEMAND ZONE"
   price_top: max(Phase C highs)
   price_bottom: min(Phase C lows)
   PURPOSE: only NOW label the zone — after all three conditions shown

6. floating_label "PRICE RETURNS TO ZONE" (if used):
   candle_index: last Phase F candle within 10 pips of zone
   price_level: zone_bottom + 0.0005
   VERIFY: abs(candles[candle_index].c - zone_bottom) <= 0.0010

7. At Phase G first candle → trade_setup (LAST overlay always)
   candle_start: first Phase G candle (touch-and-go)
   entry_price: midpoint of demand zone
   sl_price: zone_bottom - 0.0010
   tp_price: THE HIGHEST HIGH OF THE IMPULSE THAT BROKE STRUCTURE
             = max(candles[N].h) across ALL Phase D and Phase E candles
             = where the BOS move actually peaked — NOT the level it broke from
             VERIFY: tp_price = max(Phase D + Phase E highs)
   direction: "long"
   rr_ratio: floor((tp - entry) / (entry - sl)) — real R:R. Not capped.
   start_ms: chartStartMs + (candle_start + 1) * candle_interval_ms + 300
             (appears immediately after entry candle draws)
```

---

## CONCEPT 2 — ORDER BLOCK

### What It Is
Last candle of opposite colour before an impulsive move that caused a BOS.
No BOS = no valid OB.

### Phase Breakdown — Bullish OB

```
Phase A0 — Rising Context before Swing High (3-5 candles):
  Before the swing high, price must be seen RISING toward it.
  3-5 small bullish candles climbing upward, bodies 5-10 pips each.
  This shows the viewer WHERE the high came from — the trend that built it.
  Without this, the swing high appears from nowhere with no context.
  These candles are part of the overall candle count.

Phase A — Downtrend from Swing High (4-6 candles):
  After the Phase A0 peak, price falls forming LH-LL structure.
  Bodies 5-12 pips each. Small bearish candles drifting lower.
  SWING HIGH RULE: The peak candle is the LAST candle of Phase A0 / FIRST candle of Phase A.
  It sits between rising candles (before) and falling candles (after).
  VERIFY: structural_high_candle_index >= 1 (never 0 — never the very first candle)

Phase B — Equal Lows / Macro Sell-Side Liquidity (8-14 candles):
  This is Type 2 liquidity (EQL). The equal lows look like strong support.
  Retail buyers see "price always bounces here" and place stops below.
  THIS IS THE MANIPULATION SETUP — the more convincing it looks, the better.

  EQUAL LOWS PATTERN — 2-3 specific touches with real bounces between them:
  The key: only the TOUCH candles reach the level. All other candles are
  normal varying-size candles ABOVE the level creating natural price action.
  This makes it look like a real support level, not consolidation.

  Per touch sequence:
    - 2-4 normal candles drifting down toward the level (varied sizes, wicks)
    - Touch candle: bearish, body 6-12 pips, low exactly at the level
    - Bounce candle 1: bullish, 6-10 pip body — immediate reaction from level
    - Bounce candle 2: bullish or doji, 3-7 pips — momentum continuing
    - 2-4 normal candles drifting back down (mixed sizes, not all same direction)
    - [repeat for touch 2, touch 3 if needed]

  The bounce after each touch must be visible (10-20 pips total from low).
  Between touches: price drifts naturally — mixed candles, varied sizes.
  This creates the "ranging but respecting support" look of a real chart.

  Total Phase B candle count: 8-14 candles.
  Touch lows: abs(touch1.l - touch2.l) <= 0.0003 (within 3 pips)

  IMPORTANT: Phase B equal lows use consolidation style (realistic ranging).
  Phase F entry uses INDUCEMENT style (fake bounce + sweep) — these are DIFFERENT.
  Do NOT use inducement style for Phase B. Use the ranging/consolidation style.

Phase C — OB Candle (1-2 candles):
  VERIFY: candles[ob_index].c < candles[ob_index].o (bearish)
  This is the last bearish candle before the impulse.

Phase D — Impulse (3-6 candles):
  Bodies 30-60 pips.
  VERIFY FVG: candles[ob_index].h < candles[ob_index + 3].l
  VERIFY size: candles[ob_index+2].h - candles[ob_index].l >= 0.0020

Phase E — BOS (1-2 candles):
  The BOS candle closes ABOVE the structural high — that is all that is needed.
  Phase E should be short. 1 BOS candle + 1 small continuation candle maximum.
  The BOS candle does NOT need to be explosive. A candle that closes 5-15 pips
  above the structural high is sufficient — the close is the confirmation.
  After the close above, 1 optional follow-through candle (5-12 pip body) settles
  the move. Phase E then ends. Phase F retrace begins immediately.
  Do NOT add multiple large explosive candles continuing far above the structural high.
  VERIFY: candles[bos_index].c > Phase A structural high

Phase F — Retrace to OB (4-10 candles):
  Price retraces toward the OB box.
  REQUIRED: The last candle of Phase F must close within 10 pips of the OB.
    candles[last_retrace].c must be between OB_bottom and OB_bottom + 0.0010
  If the retrace candles do not reach within 10 pips of the OB, add more
  retrace candles until this condition is met. Do not stop the retrace early.
  VERIFY:
    OB zone = [price_bottom] to [price_top]
    Last retrace candle close = [price]
    CHECK: close <= OB_bottom + 0.0010 = TRUE/FALSE


  INDUCEMENT (IDM) — forms during Phase F, after 60%+ of retrace is complete:

  WHAT IS INDUCEMENT:
  After Phase F has retraced 60%+ toward the OB, price forms a manipulation move
  to trap early buyers. It looks like the reversal started — buyers enter long,
  stops below the IDM low. Then price sweeps those stops before the real launch.

  IDM TIMING RULE:
  retrace_distance = Phase E peak high - OB_bottom
  IDM begins only after price has fallen at least: retrace_distance × 0.60
  Candles before that level = plain retrace candles. IDM only after 60%.

  IDM PATTERN — three parts:

  Part 1 — The IDM Low (1 candle):
    A bearish candle that creates the specific low to be swept.
    Must have a visible lower wick (looks like a natural support reaction).
    IDM_low = candles[idm_candle].l

  Part 2 — The Fake Bounce (minimum 5 candles, typically 5-9):
    Bullish candles bouncing UP from the IDM low. This is the TRAP.
    More candles = more convincing trap = better teaching moment.
    Price looks reversed — early buyers enter long, stops below IDM low.

    TWO CRITICAL RULES:
    RULE A — NEVER BREAK STRUCTURE:
      Close of EVERY bounce candle must be below the last Lower High in Phase F.
      The LH-LL downtrend structure of Phase F must remain intact.
      VERIFY: max(bounce_candles.c) < last_lower_high_in_Phase_F
      If any bounce candle would exceed this: shorten its body.

    RULE B — NEVER APPROACH THE OB:
      IDM bounce candles must stay at least 20 pips above OB_top.
      This keeps the OB and IDM visually separate so the viewer can
      clearly see the two distinct levels.
      VERIFY: min(all_bounce_candle_lows) >= OB_top + 0.0020 (20 pips)
      If this fails: the IDM_low was placed too close to OB — revise.

    Bounce candle pattern (make it look like a real reversal attempt):
      - First 3-4 candles: rising bullish, bodies 6-12 pips each, varied
      - Middle candles: some small doji/indecision candles mixed in
      - Final 1-2 candles: slightly smaller, showing momentum slowing
      - Total rise from IDM low: 20-40 pips (convincing but not structure-breaking)
      - All bodies varied in size — never uniform

  Part 3 — The IDM Sweep (1 candle — this becomes Phase G candle 1):
    ONE candle sweeps below IDM_low, closes back above.
    SHAPE — wick candle only:
      o: above IDM_low (opens in bounce territory)
      l: below IDM_low by at least 5 pips (wick takes the stops)
      c: above IDM_low (closes above — the real entry signal)
    Prefer bullish body (c > o). Wick >= 5 pips below IDM_low.

  VERIFY IDM:
    60% retrace reached: [price] = Phase E high - (retrace_distance × 0.60) = [price] ✓/✗
    IDM_low = [price], last LH in Phase F = [price]
    Max bounce close = [price] < last LH = TRUE/FALSE
    Sweep: l=[price] < IDM_low ✓, c=[price] > IDM_low ✓, wick=[N]pips ✓


Phase G — Launch from OB to TP (3-8 candles):
  Price touches the OB zone and launches all the way to the structural high (TP).
  trade_setup candle_start = first Phase G candle (the touch-and-go candle).

  Phase G candle rules:
    First candle: opens inside or at OB zone, closes above OB top.
    Remaining candles: bullish expansion, body range 20-40 pips each.

  REQUIRED — PRICE MUST REACH THE TP:
    The last candle of Phase G must close AT or ABOVE tp_price.
    tp_price = the HIGHEST HIGH of Phase D + Phase E impulse candles.
    This is the peak of the move that broke structure — NOT the bos_label price level.
    VERIFY: candles[last_phase_g].c >= tp_price
    If FALSE: extend Phase G with more bullish candles until TRUE.
    The video must show price completing the full move — not stopping halfway.
```

### Overlay Teaching Sequence

MANDATORY ORDER: Evidence appears before conclusion.
FVG and BOS must be on screen BEFORE the OB label appears.
A candle_label pointing to the OB candle must appear BEFORE the order_block box.
The viewer must watch conditions form — then see the conclusion.

```
1. After Phase B last candle → liquidity
   label: "$$$ STOPS HERE", swept: false
   PURPOSE: show where stops cluster before anything else

2. After Phase D third candle → fvg
   label: "FVG CREATED"
   PURPOSE: first evidence — impulse left institutional footprint

3. After Phase E BOS candle → bos_label
   label: "BOS CONFIRMED", direction: "up"
   candle_start: Phase A candle with MAX(h) — NOT always candle 0
   candle_index: bos_index
   price_level: candles[candle_start].h
   PURPOSE: second evidence — structure is broken, OB is now valid

4. 800ms after BOS → candle_label (point to the OB candle)
   text: "OB CANDLE" (max 5 words, one line, no \n)
   candle_index: ob_index
   price_level: candles[ob_index].l - 0.0005
   side: "right"
   PURPOSE: identify the specific candle now that evidence confirms it

5. 800ms after candle_label → order_block (THE CONCLUSION — last concept label)
   label: "ORDER BLOCK"
   candle_index: ob_index
   direction: "bearish" (for bullish OB setup — matches candle colour)
   PURPOSE: only NOW label the OB — FVG + BOS already confirmed it

6. Near last Phase F candles → liquidity (ENTRY LIQUIDITY)
   type: "liquidity", label: "$$$ ENTRY LQ", swept: false
   price_level: IDM_low (specific low formed before the fake bounce)
   candle_start: IDM low candle index
   candle_end: last equal-low candle before Phase G
   PURPOSE: show the entry liquidity pool that will be swept on entry

7. Phase G first candle wicks below entry LQ → liquidity (swept = true)
   type: "liquidity", label: "LQ SWEPT — ENTRY", swept: true
   price_level: same IDM_low as step 6
   PURPOSE: confirm the sweep happened — this is the entry trigger

8. floating_label "PRICE RETURNS TO ORDER BLOCK" (if used):
   candle_index: last Phase F candle within 10 pips of OB
   price_level: OB_bottom + 0.0005

9. At Phase G first candle → trade_setup (LAST overlay always)
   candle_start: first Phase G candle (touch-and-go candle)
   entry_price: (OB_top + OB_bottom) / 2
   sl_price: OB_bottom - 0.0010
   tp_price: Phase A structural high
   direction: "long"
   rr_ratio: floor((tp - entry) / (entry - sl)) — round DOWN to whole number
     Show the real R:R to structural high. Not capped.
   start_ms: chartStartMs + (candle_start + 1) * candle_interval_ms + 300
             (appears immediately after entry candle draws)
```

---

## CONCEPT 4 — TRENDLINE LIQUIDITY

### What It Is
A trendline forms when price repeatedly touches a diagonal slope (3+ touches).
Retail traders buy on uptrend touches or sell on downtrend touches, placing stops
just below/above the trendline. Breakout traders place orders on a trendline break.
Both groups cluster stops at the trendline area.
Institutions sweep through the trendline with a wick, collect all those stops,
then reverse with momentum proportional to the liquidity collected.

This is a TYPE 1 (concept-only) or TYPE 2 (full setup) video.
TYPE 1: Show the trendline forming, touches, the sweep. No trade_setup.
TYPE 2: After the sweep, price finds an OB or demand zone and launches.
         Include trade_setup and IDM if showing full entry.

### Phase Breakdown — Down Trendline (most teachable)

```
Phase A — Downtrend Context (3-5 candles):
  LH-LL structure establishing the bearish bias.
  Shows the viewer we are in a downtrend. Context only.
  Bodies 5-12 pips, varied.

Phase B — Trendline Formation (10-16 candles):
  The trendline forms through 3 touches of the descending line.
  Between touches: normal bearish candles staying BELOW the trendline.
  Each touch is a separate bounce reaction.

  TOUCH PATTERN per touch (same structure as equal lows but diagonal):
    Approach candle: drifts toward the trendline (2-4 candles)
    Touch candle: wick reaches trendline, body closes below (retail sees "respect")
    Bounce candle(s): small bullish reaction 3-8 pips before resuming lower
    Between next touch: 2-4 bearish candles drifting back toward trendline

  TRENDLINE MATH — must verify all 3 touches before generating JSON:
    anchor_1: (candle_A, price_A) = first touch candle high
    anchor_2: (candle_B, price_B) = second touch candle high
    slope = (price_B - price_A) / (candle_B - candle_A)

    For each subsequent touch candle N:
      trendline_price_at_N = price_A + slope × (N - candle_A)
      VERIFY: abs(candles[N].h - trendline_price_at_N) <= 0.0005
      If FALSE: adjust candles[N].h until it meets the trendline within 5 pips.

  After touch 3: retail sellers are confident. Stops sitting above each touch high.
  Breakout traders have buy stops waiting above the trendline.

Phase C — The Sweep (1-2 candles):
  ONE candle wicks ABOVE the trendline (taking all the sell stops and buy stops)
  and CLOSES BACK BELOW the trendline.
  This is the liquidity grab. Body must close below the trendline.

  Sweep candle math:
    trendline_price_at_sweep = price_A + slope × (sweep_candle - candle_A)
    VERIFY: candles[sweep].h > trendline_price_at_sweep (wick above)
    VERIFY: candles[sweep].c < trendline_price_at_sweep (body closes below)
    wick_above = candles[sweep].h - trendline_price_at_sweep >= 0.0005

Phase D — Post-Sweep Momentum (3-5 candles):
  After the sweep, price reverses with momentum.
  Bearish candles 20-40 pips each — the fuel from the collected stops.
  This confirms the trendline was a liquidity trap, not real resistance.

(For TYPE 2 full setup: add Phase E as OB/demand zone reaction and entry)
```

### Overlay Teaching Sequence

```
1. After touch 2 candle → trendline (swept: false)
   type: "trendline"
   candle_start: candle_A (touch 1 index), price_start: touch_1_high
   candle_end:   candle_B (touch 2 index), price_end: touch_2_high
   extend_to: sweep_candle_index (line extends to show where price will approach)
   direction: "down"
   label: "$$$ TRENDLINE LQ"
   PURPOSE: show the pattern forming — viewer sees the trendline and the stops

2. After touch 2 → candle_label on touch 1
   text: "TOUCH 1", candle_index: touch_1_candle
   price_level: touch_1_high + 0.0005

3. After touch 2 → candle_label on touch 2
   text: "TOUCH 2", candle_index: touch_2_candle
   price_level: touch_2_high + 0.0005

4. After touch 3 → candle_label on touch 3
   text: "TOUCH 3", candle_index: touch_3_candle
   price_level: touch_3_high + 0.0005
   PURPOSE: confirm the third touch — viewer understands the pattern

5. After sweep candle → trendline (swept: true)
   Same anchor points as overlay 1.
   PURPOSE: show the trendline was just swept — liquidity was taken

6. After sweep → floating_label
   text: "RETAIL STOPS TAKEN"
   candle_index: sweep_candle
   color: brand.danger (#991B1B)
   PURPOSE: name what just happened — stops taken = fuel for the move

7. After Phase D first big candle → floating_label
   text: "MOMENTUM AFTER SWEEP"
   color: brand.accent (#C9A84C)
   PURPOSE: connect the sweep to the momentum — "this is why price moved"

(TYPE 2 only — after all the above):
8. liquidity (swept: false) → liquidity (swept: true) → trade_setup
   Same IDM pattern as OB/demand zone concept.
```

### Mathematical Checklist

```
- [ ] slope = (touch_2_high - touch_1_high) / (touch_2_candle - touch_1_candle) = [value]
- [ ] Touch 1: abs(candles[touch_1].h - price_A) = 0 (anchor, always exact)
- [ ] Touch 2: abs(candles[touch_2].h - (price_A + slope × (touch_2 - touch_1))) <= 0.0005
- [ ] Touch 3: abs(candles[touch_3].h - (price_A + slope × (touch_3 - touch_1))) <= 0.0005
- [ ] Sweep: candles[sweep].h > trendline_at_sweep AND candles[sweep].c < trendline_at_sweep
- [ ] Sweep wick: candles[sweep].h - trendline_at_sweep >= 0.0005 (5 pips through trendline)
- [ ] Phase D candles show clear momentum (20-40 pip bodies)
- [ ] If TYPE 2: liquidity swept before trade_setup
```

---

## CONCEPT 3 — LIQUIDITY SWEEP

### What It Is
Institutions spike price through equal highs/lows to grab stop losses,
then reverse sharply. The wick is the grab.

### Phase Breakdown — Buy-Side Sweep then Reversal

```
Phase A — Uptrend Context (4-8 candles):
  HH-HL structure. Prior bullish move.
  The swing HIGH for a bearish sweep should sit in the MIDDLE of Phase A,
  not at the last candle. This creates readable context on both sides.

Phase B — Equal Highs Formation (4-6 candles):
  VERIFY: abs(candles[A].h - candles[B].h) <= 0.0003
  Retail stops sit above these highs.

Phase C — Consolidation below highs (2-4 candles):
  Tight range. More stops accumulate.

Phase D — Spike / Sweep (1-2 candles):
  VERIFY ALL THREE:
    candles[spike].h > equal_high_level
    candles[spike].c < equal_high_level
    candles[spike].h - candles[spike].c > 0.0015

Phase E — Bearish Reversal (3-6 candles):
  Large bearish bodies 25-50 pips. Institutional move.

Phase F — Continuation (2-5 candles):
  Confirms reversal. Price continues lower.
```

### Overlay Teaching Sequence

```
1. After Phase B last candle → liquidity
   label: "$$$ EQUAL HIGHS", swept: false
   price_level: candles[first_eq].h

2. After Phase D spike → candle_label
   text: "LIQUIDITY SWEEP", side: "right"
   candle_index: spike_index, price_level: candles[spike].h

3. 800ms after sweep → candle_label
   text: "WICK = GRAB, NOT BOS", side: "left"
   candle_index: spike_index, price_level: candles[spike].c

4. After Phase E second candle → floating_label
   text: "SELL-SIDE NOW IN CONTROL", color: "#ef5350"

5. After Phase E third candle → liquidity (swept = true)
   label: "SWEPT ✓", swept: true
   price_level: candles[first_eq].h
```

---

## CANDLE QUALITY RULES

### candle_label Text Rule
candle_label text must be:
- Maximum 5 words
- One line only — NO \n characters, no line breaks
- SVG does not render newlines — multi-line text becomes one broken string
- If two labels are needed on the same candle, create TWO separate candle_label
  overlays at different price_level values (e.g. one at the high, one 20 pips below)

**Pip reference for EUR/USD (1 pip = 0.0001):**
```
3 pips  = 0.0003    10 pips = 0.0010    30 pips = 0.0030
5 pips  = 0.0005    20 pips = 0.0020    50 pips = 0.0050
```

**Context candles:** body range 5-12 pips (0.0005 to 0.0012)
**Impulse candles:** body range 30-60 pips (0.0030 to 0.0060)
**Ratio:** impulse must be at least 3x larger than context in body size.
If they look the same, the concept is invisible to the viewer.

---

## OVERLAY TIMING RULES

```
Candle N (0-indexed) finishes at:
  (N + 1) × candle_interval_ms

Every overlay start_ms >= that candle's finish time.
Minimum 800ms gap between consecutive overlays.
trade_setup: start_ms = (candle_start + 1) × candle_interval_ms + 300
```

---

## ALLOWED CONCEPTS (v4)

| Concept                          | Status     | Generate? | Notes |
|----------------------------------|------------|-----------|-------|
| Order Block                      | READY      | YES       | Full IDM + entry |
| Fair Value Gap                   | PARTIAL    | YES       | As part of OB; standalone needs work |
| BOS                              | READY      | YES       | Used in OB/demand sequence |
| Valid Demand/Supply Zone         | READY      | YES       | Same structure as OB |
| Liquidity — EQH/EQL              | READY      | YES       | Horizontal. TYPE 2 and TYPE 3 |
| Liquidity — Trendline            | READY      | YES       | New `trendline` overlay added |
| Liquidity — Range (S&R)          | READY      | YES       | Two `liquidity` overlays, both sides |
| Liquidity — Entry (IDM)          | READY      | YES       | Part of OB/demand full setup |
| Liquidity Sweep (concept video)  | PARTIAL    | YES       | No entry, TYPE 1 concept |
| CHoCH                            | NOT READY  | NO        | Need more material |
| Flip Setup                       | NOT READY  | NO        | Need more material |
| Equilibrium                      | NOT READY  | NO        | Need more material |

---

## HOOK TEXT FORMAT

Use a `|` character to split the hook into blue | black for visual emphasis.
Everything before | renders in BLUE (brand colour, key concept word).
Everything after | renders in BLACK (the supporting context).

HOOK WRITING RULES:
- Blue part (before |): 1-4 words MAX — the striking concept or claim
- Black part (after |): the supporting line. Total hook max 12 words.
- The hook must stop the scroll. It should feel like a statement or a challenge.
- IMPORTANT: Do NOT copy or closely imitate the examples below.
  The examples show STYLE and TONE only — create your own original version
  that fits the specific concept being taught in the video.
- Write as if speaking directly to a frustrated retail trader.
- Tone: confident, slightly confrontational, never academic.

Style examples (DO NOT COPY — use as tone reference only):
  Style 1 — Bold claim:     "TRADING IS DEAD|And this is why you keep losing."
  Style 2 — Direct address: "YOUR TRADING FEELS STUCK|Here is exactly why."
  Style 3 — Pattern call:   "STOP USING THIS SETUP|Try this one instead."
  Style 4 — Challenge:      "THIS ADVICE SOUNDS SMART|But it doesn't actually work."

Apply the same energy to whatever concept the video teaches — Order Block,
FVG, Liquidity, BOS etc. The concept name is usually the blue part.

## HOOK TEXT EXAMPLES

**Order Block:**
"Most traders see a candle. Smart Money sees an order block."
"The last bearish candle before the explosion. That is your order block."

**Demand Zone:**
"Before you mark a demand zone — make sure it earned the label."
"Three conditions. Most traders know zero of them."

**Liquidity Sweep:**
"Your stop loss is not safe. It is a target."
"Equal highs are not resistance. They are fuel."

**FVG:**
"This gap is not a mistake. It is institutional footprint."

---

## FINAL CHECKLIST

- [ ] hook_text present — one line, max 12 words
- [ ] Every candle_label text: maximum 5 words, one line, NO \n characters
- [ ] bos_label candle_start = Phase A candle with MAX(h), NOT always candle 0
- [ ] Phase count calculated before generating candles
- [ ] candles.length between 10 and 60
- [ ] candles.length = sum of required phases only — no padding
- [ ] visible_count = candles.length exactly
- [ ] candle_interval_ms >= 400
- [ ] duration_ms = (candles.length × candle_interval_ms) + 4000
chart.start_ms = 0  (candles start immediately on frame 1 — no delay)
- [ ] Every candle: h >= max(o,c) AND l <= min(o,c)
- [ ] Impulse candles are 3-5x larger than context in body size
- [ ] FVG verified: candles[X+2].l > candles[X].h (if concept needs FVG)
- [ ] BOS verified: candles[bos].c > structural_high (if concept needs BOS)
- [ ] Equal highs/lows verified: abs(diff) <= 0.0003 (if concept needs liquidity)
- [ ] OB verified: last bearish candle before impulse (if concept needs OB)
- [ ] Sweep verified: wick above + close below (if sweep concept)
- [ ] All overlays use price values and candle indexes — no percentages
- [ ] All overlay start_ms >= their candle finish time
- [ ] Overlays in teaching order: liquidity → fvg → bos_label → candle_label → order_block/demand_zone → trade_setup
- [ ] order_block and demand_zone appear AFTER fvg and bos_label — never before
- [ ] GOLDEN RULE: liquidity was swept before the entry — macro LQ (Phase B) and/or entry LQ (Phase F/G)
- [ ] IDM sweep verified: sweep candle l < IDM_low, c > IDM_low, wick >= 5 pips
- [ ] Phase G price reaches TP: candles[last_phase_g].c >= tp_price (structural high)
- [ ] Minimum 800ms between consecutive overlays
- [ ] trade_setup start_ms = entry candle finish + 300ms (NOT duration_ms - 2000)
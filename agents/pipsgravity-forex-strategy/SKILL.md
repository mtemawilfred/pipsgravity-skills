---
name: pipsgravity-forex-strategy
description: The PipsGravity forex strategy skill for generating CHART_SCENE videos. Teaches Claude the exact concepts, mathematical verification conditions, candle sequences, and teaching-order rules. v4 — candle max raised to 60, dynamic phase-based candle counting added, visible_count = candles.length confirmed correct. Sources: PipsGravity Academy Course, Pietrus-914/skills-repo, pattern-recognition skill.
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

**HOOK (0ms to 1800ms):** Clean screen. Static hook_text title. No candles.
**CHART (1800ms onward):** Candles draw one by one. Overlays appear in teaching order.

**NO stt_timestamps. NO narration script. NO captions.**
The chart teaches. The hook_text is the only text the viewer reads.

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
    "start_ms": 1800,
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
duration_ms = 1800 + (candles.length × candle_interval_ms) + 4000
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
structural_high = highest close or high of Phase A candles
IF candles[bos_index].c <= structural_high → NOT a BOS. Raise candles[bos_index].c.
IF candles[bos_index].c >  structural_high → BOS confirmed. Place bos_label.
```

---

### Equal Highs / Equal Lows (Liquidity)

**From Pattern Recognition skill (Tweezer detection) + SMC reference:**
```
Equal Highs: abs(candles[A].h - candles[B].h) <= 0.0003  (within 3 pips)
Equal Lows:  abs(candles[A].l - candles[B].l) <= 0.0003

price_level for overlay = candles[A].h (or .l for lows)
```

**Self-check:**
```
IF abs(candles[A].h - candles[B].h) > 0.0003 → NOT equal. Adjust prices.
IF abs(candles[A].h - candles[B].h) <= 0.0003 → Equal highs confirmed. Place liquidity overlay.
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

  OB box:
    price_top    = max(candles[ob_index].o, candles[ob_index].c)
    price_bottom = min(candles[ob_index].o, candles[ob_index].c)
    candle_index = ob_index

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
        Candle N finishes at: 1800 + (N+1) × candle_interval_ms

STEP 6: Place overlays in teaching order
        Evidence labels first, concept label last
        Each overlay start_ms >= that candle's finish time
        Minimum 800ms gap between consecutive overlays

STEP 7: Calculate duration_ms = 1800 + (candles.length × candle_interval_ms) + 4000

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
Phase A — Downtrend Context (4-8 candles):
  Small bearish bodies 5-12 pips. Establishes structural high.

Phase B — Equal Lows / Liquidity (3-5 candles):
  VERIFY: abs(candles[A].l - candles[B].l) <= 0.0003
  Sell-side stops clustered here.

Phase C — Base / OB Zone (1-3 candles):
  Very small bodies. Institutional accumulation.
  Last bearish candle = the Order Block.
  VERIFY OB: candles[last_base].c < candles[last_base].o

Phase D — Impulse (3-6 large bullish candles):
  Bodies 30-60 pips. Must leave FVG.
  VERIFY FVG: candles[last_base + 3].l > candles[last_base].h

Phase E — BOS (1-2 candles):
  VERIFY: candles[bos_index].c > Phase A structural high

Phase F — Retrace (4-10 candles):
  Price returns toward demand zone.
  Show the potential entry area.
```

### Overlay Teaching Sequence

```
1. After Phase B last candle → liquidity
   label: "$$$ EQUAL LOWS", swept: false
   price_level: candles[last_equal_low].l

2. After Phase D third candle → fvg
   label: "STEP 1: FVG ✓"
   price_top: candles[last_base + 3].l
   price_bottom: candles[last_base].h

3. After Phase E BOS candle → bos_label
   label: "STEP 2: BOS ✓", direction: "up"
   price_level: Phase A structural high

4. 800ms after BOS → floating_label
   text: "STEP 3: LIQUIDITY ✓"

5. 800ms after step 3 → demand_zone (LAST concept label)
   label: "VALID DEMAND ZONE"
   price_top: max(Phase C highs)
   price_bottom: min(Phase C lows)
```

---

## CONCEPT 2 — ORDER BLOCK

### What It Is
Last candle of opposite colour before an impulsive move that caused a BOS.
No BOS = no valid OB.

### Phase Breakdown — Bullish OB

```
Phase A — Downtrend Context (4-8 candles):
  LH-LL structure. Establishes structural high. Bodies 5-12 pips.

Phase B — Equal Lows (3-5 candles):
  VERIFY: abs(candles[A].l - candles[B].l) <= 0.0003

Phase C — OB Candle (1-2 candles):
  VERIFY: candles[ob_index].c < candles[ob_index].o (bearish)
  This is the last bearish candle before the impulse.

Phase D — Impulse (3-6 candles):
  Bodies 30-60 pips.
  VERIFY FVG: candles[ob_index].h < candles[ob_index + 3].l
  VERIFY size: candles[ob_index+2].h - candles[ob_index].l >= 0.0020

Phase E — BOS (1-2 candles):
  VERIFY: candles[bos_index].c > Phase A structural high

Phase F — Retrace to OB (4-10 candles):
  Price returns toward OB box. Shows the entry.
  End with small bounce from OB level.
```

### Overlay Teaching Sequence

```
1. After Phase B → liquidity
   label: "$$$ STOPS HERE", swept: false

2. After Phase D third candle → fvg
   label: "FVG CREATED"

3. After Phase E BOS candle → bos_label
   label: "BOS CONFIRMED", direction: "up"

4. 800ms after BOS → order_block
   label: "ORDER BLOCK"
   candle_index: ob_index

5. After Phase F retrace → trade_setup (LAST)
   entry: midpoint of OB box
   sl: below OB low with buffer
   tp: Phase A structural high or beyond
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
  1800 + (N + 1) × candle_interval_ms

Every overlay start_ms >= that candle's finish time.
Minimum 800ms gap between consecutive overlays.
trade_setup always last: start_ms = duration_ms - 2000
```

---

## ALLOWED CONCEPTS (v4)

| Concept              | Status     | Generate? |
|----------------------|------------|-----------|
| Order Block          | READY      | YES       |
| Fair Value Gap       | READY      | YES       |
| BOS                  | READY      | YES       |
| Valid Demand/Supply  | READY      | YES       |
| Liquidity Sweep      | READY      | YES       |
| CHoCH                | NOT READY  | NO        |
| Flip Setup           | NOT READY  | NO        |
| Equilibrium          | NOT READY  | NO        |

---

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
- [ ] No stt_timestamps field anywhere
- [ ] Phase count calculated before generating candles
- [ ] candles.length between 10 and 60
- [ ] candles.length = sum of required phases only — no padding
- [ ] visible_count = candles.length exactly
- [ ] candle_interval_ms >= 400
- [ ] duration_ms = 1800 + (candles.length × candle_interval_ms) + 4000
- [ ] Every candle: h >= max(o,c) AND l <= min(o,c)
- [ ] Impulse candles are 3-5x larger than context in body size
- [ ] FVG verified: candles[X+2].l > candles[X].h (if concept needs FVG)
- [ ] BOS verified: candles[bos].c > structural_high (if concept needs BOS)
- [ ] Equal highs/lows verified: abs(diff) <= 0.0003 (if concept needs liquidity)
- [ ] OB verified: last bearish candle before impulse (if concept needs OB)
- [ ] Sweep verified: wick above + close below (if sweep concept)
- [ ] All overlays use price values and candle indexes — no percentages
- [ ] All overlay start_ms >= their candle finish time
- [ ] Overlays in teaching order — evidence before conclusion
- [ ] Minimum 800ms between consecutive overlays
- [ ] trade_setup is last: start_ms = duration_ms - 2000
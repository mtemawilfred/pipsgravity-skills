---
name: pipsgravity-forex-strategy
description: The PipsGravity forex trading strategy for generating chart education videos. Teaches Claude the exact concepts, candle sequences, and teaching-order rules for producing CHART_SCENE videos. Every rule comes directly from the PipsGravity Academy course. Load this skill before generating any chart scene. v2 — captions removed, hook_text added, labels are earned.
---

## WHO THIS IS FOR

This skill teaches you the PipsGravity forex strategy as taught in the PipsGravity Academy. When generating CHART_SCENE videos for PipsGravity, follow every rule in this document precisely. Not a generic version — this specific framework.

---

## TERMINOLOGY

Supply/Demand zones and Order Blocks are the same thing. Imbalance, inefficiency, fair value gap, and FVG all mean the same thing. Use these terms interchangeably.

---

## VIDEO STRUCTURE — READ THIS FIRST

Every CHART_SCENE video has two phases:

**PHASE 1 — HOOK (0ms to 1800ms):** Clean screen. No candles. The `hook_text` field is displayed as a static title. One punchy line that names the concept and creates curiosity. Examples: "Most traders see a candle. Smart Money sees an order block." / "This is why your stops keep getting taken out." / "Equal highs are not resistance. They are a target."

**PHASE 2 — CHART (1800ms onward):** Candles draw one by one. Overlays appear in teaching order — each label is earned by the candle evidence that precedes it. The viewer learns by watching conditions form, not by being told conclusions.

---

## SCHEMA

```json
{
  "scene_id": 1,
  "render_type": "CHART_SCENE",
  "duration_ms": <number — calculated, see formula below>,
  "hook_text": "<one punchy line — displayed as static title 0ms to duration_ms>",
  "brand": {
    "primary": "#1B2A4A",
    "accent": "#C9A84C",
    "danger": "#991B1B",
    "success": "#166534",
    "font_heading": "Oswald",
    "font_body": "Inter"
  },
  "chart": {
    "start_ms": 1800,
    "candles": [ { "o": number, "h": number, "l": number, "c": number } ],
    "candle_interval_ms": <400ms minimum — choose based on concept pacing>,
    "visible_count": <MUST equal candles.length exactly>,
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

**NO stt_timestamps field. It does not exist in v2. Never generate it.**

### Duration Formula
```
duration_ms = chart.start_ms + (candles.length × candle_interval_ms) + 4000
```
The +4000ms gives 4 seconds after the last candle for overlays to settle. No maximum. Let the concept determine the length.

### Candle Rules
- **Maximum 30 candles.** Every candle must serve the story. If the concept needs 15, use 15. If it needs 25, use 25. Never exceed 30.
- **candle_interval_ms minimum 400ms.** Slower is better for education. 500-600ms is ideal for most concepts. Viewers need time to see each candle.
- **visible_count MUST equal candles.length.** Always. No exceptions.
- **OHLC validity:** Every candle must satisfy `h >= max(o,c)` AND `l <= min(o,c)`.
- **Impulsive candles** (the expansion/momentum candles) must be visually larger than context candles. Make bodies 3-5× the size of context candles. This is how viewers see momentum.
- **Context candles** should be smaller, overlapping, corrective — showing a ranging or drifting market.

---

## OVERLAY TIMING RULES

**Candle N (0-indexed) finishes drawing at:**
```
chart.start_ms + (N + 1) × candle_interval_ms
```

Every overlay `start_ms` MUST be >= the finish time of the candle it references. Never show a label before its candle has finished drawing.

**Minimum 800ms gap between consecutive overlays.** Labels must be staggered so the viewer can read each one.

---

## THE TEACHING PRINCIPLE — LABELS ARE EARNED

This is the most important rule in this skill.

Every label must be preceded by the candle evidence that earns it. The viewer learns by watching conditions appear in order, not by seeing labels before the evidence.

**Wrong:** Show candles, then immediately label the demand zone.
**Right:** Show liquidity form → show FVG form → show BOS confirm → THEN label the demand zone.

Each condition gets its own overlay label when it appears. The final concept label (Order Block, Demand Zone) only appears AFTER all its conditions have been labelled first.

Use `floating_label` overlays with bold short text like "STEP 1: FVG ✓" or "STEP 2: BOS ✓" to guide the viewer through the evidence chain before the conclusion.

---

## CONCEPT 1 — VALID SUPPLY AND DEMAND ZONE

### What Makes It Valid

A Supply/Demand zone (Order Block) is ONLY valid when ALL THREE conditions are present:

1. **It created a Fair Value Gap (FVG)** — price moved so fast from that level that it left an unfilled gap. The gap is between the low of the candle before the impulse and the high of the candle after the impulse (for bullish). This gap proves significant orders existed at that level.

2. **It broke structure (BOS)** — the move from the zone must close a candle BEYOND the previous structural high (for bullish) or structural low (for bearish). A wick through does NOT count. The candle body must CLOSE beyond the level.

3. **It created or took liquidity** — the zone formed near equal highs or equal lows (where stop losses are clustered), OR the move swept liquidity before the zone formed.

**A zone without all three conditions is not valid and must not be labelled.**

### How to Mark the Zone

**Demand zone:** Take the low of the consolidation range and the highest candle body within it. The zone spans from the lowest wick to the highest body of the base candles.

**Supply zone:** Take the high of the consolidation range and the lowest candle body within it. The zone spans from the highest wick to the lowest body of the base candles.

The demand zone is created once the expansion (the impulsive move + BOS) takes place. Price must subsequently fall back INTO the zone from the top side for the entry to be valid.

### Candle Sequence — Bullish Demand Zone

Total: 20-25 candles.

```
PHASE A — Context (4-5 candles):
  Bearish corrective candles. Small bodies. Price drifting lower.
  This shows we are in a downmove, approaching a potential demand area.

PHASE B — Liquidity formation (3-4 candles):
  Equal lows. Two or more candles with lows at approximately the same price level.
  These equal lows ARE the liquidity. Retail stop losses sit below them.
  Candles are small, overlapping — a ranging, consolidating market.

PHASE C — Base / Consolidation (2-3 candles):
  Small candles, very tight range. This is the demand zone.
  The last bearish candle in this sequence will become the Order Block.
  These candles represent institutional accumulation before the expansion.

PHASE D — Impulsive Expansion (3-4 candles):
  Large bullish candles. Bodies must be 3-5× larger than Phase C candles.
  These candles leave a Fair Value Gap between Phase C and Phase D.
  The FVG is between: the LOW of the last Phase C candle and the HIGH of the 3rd Phase D candle.
  Verify: candles[last_base].l > candles[first_impulse + 2].h

PHASE E — BOS Candle (1-2 candles):
  One candle that CLOSES above the most recent structural high from Phase A.
  This is the Break of Structure. The candle body must close above — not just wick.

PHASE F — Optional Retrace (3-5 candles):
  Price pulls back toward the demand zone. Does not need to touch it.
  This shows the potential entry area. Include if time allows.
```

### Overlay Teaching Sequence — Bullish Demand Zone

Apply overlays in this exact order:

```
1. After Phase B last candle finishes:
   type: "liquidity"
   label: "$$$ EQUAL LOWS"
   purpose: Show where retail stops are clustered BEFORE the zone is labelled

2. After Phase D third candle finishes (FVG now visible):
   type: "fvg"
   label: "STEP 1: FVG ✓"
   price_top: candles[last_base].l
   price_bottom: candles[first_impulse + 2].h
   purpose: First condition confirmed — show the gap

3. After Phase E BOS candle finishes:
   type: "bos_label"
   label: "STEP 2: BOS ✓"
   direction: "up"
   price_level: the structural high that was broken
   purpose: Second condition confirmed — candle CLOSED above structure

4. 800ms after BOS label:
   type: "floating_label"
   text: "STEP 3: LIQUIDITY ✓"
   purpose: Third condition confirmed — the equal lows from Phase B were the liquidity

5. 800ms after Step 3 label:
   type: "demand_zone"
   label: "VALID DEMAND ZONE"
   price_top: max high of Phase C candles
   price_bottom: min low of Phase C candles
   candle_start: first Phase C candle index
   purpose: Final label — only appears AFTER all 3 conditions are labelled
```

---

## CONCEPT 2 — ORDER BLOCK

### What an Order Block Is

An Order Block is the LAST candle of the opposite colour before an impulsive move that caused a Break of Structure.

- **Bullish OB:** The LAST BEARISH candle before the bullish impulse that broke structure to the upside.
- **Bearish OB:** The LAST BULLISH candle before the bearish impulse that broke structure to the downside.

The OB is where institutions placed their final orders before the move. They were selling (bearish candle) even as they planned to push price up. They must come back to mitigate that position. When price returns to the OB, institutions close their opposing trade and continue the original move.

**The OB MUST have caused a BOS.** If the impulse from that candle did not break structure, it is NOT a valid OB.

### How to Mark the OB Box

The OB box covers the BODY of the last opposite-direction candle:
- `price_top` = max(open, close) of the OB candle
- `price_bottom` = min(open, close) of the OB candle

The wicks are not included in the box unless the wick is much larger than the body (in which case mark from wick to body or 50% of full candle range).

### Candle Sequence — Bullish Order Block

Total: 18-22 candles.

```
PHASE A — Downtrend Context (4-5 candles):
  LH-LL structure. Bearish trend. Small-to-medium bearish candles.
  Establishes the structural high that will be broken by BOS.

PHASE B — Equal Lows / Liquidity (3-4 candles):
  Two or more candles with equal lows. Stop losses cluster here.
  Price is ranging tightly. Building sell-side liquidity.

PHASE C — The Base: 1-2 small candles (these are the OB area):
  The last 1-2 candles before the impulse. Often small, indecisive.
  The LAST bearish candle in this sequence is THE ORDER BLOCK.
  Its body (open-to-close) defines the OB box.

PHASE D — Impulse: 3-4 large bullish candles:
  Large bodies. 4-6× the size of Phase C candles. Fast momentum.
  Leaves a clear Fair Value Gap after Phase C.
  FVG: candles[OB_index].l > candles[OB_index + 3].h

PHASE E — BOS: 1 candle closes above Phase A structural high:
  Candle body closes ABOVE the previous structural high. 
  This is what validates the OB. Without this, the OB is invalid.

PHASE F — Retrace to OB (3-5 candles):
  Price pulls back toward the OB box. This is the entry.
  Show price approaching but not necessarily filling the OB.
  Ends with a reaction (small bounce) from the OB level.
```

### Overlay Teaching Sequence — Order Block

```
1. After Phase B last candle:
   type: "liquidity"
   label: "$$$ STOPS HERE"
   candle_start: first equal-low candle index
   candle_end: last equal-low candle index
   purpose: Show where stops are — this is what gets swept

2. After Phase D third candle (FVG visible):
   type: "fvg"
   label: "FVG CREATED"
   purpose: Gap confirms the impulse was institutional

3. After Phase E BOS candle:
   type: "bos_label"
   label: "BOS CONFIRMED"
   direction: "up"
   price_level: the structural high broken
   purpose: Validates the OB — without this, no OB exists

4. 800ms after BOS label:
   type: "order_block"
   label: "ORDER BLOCK"
   candle_index: the last bearish candle before Phase D (the OB candle)
   price_top: max(o, c) of that candle
   price_bottom: min(o, c) of that candle
   direction: "bearish"
   purpose: NOW we label the OB — only after BOS confirms it is valid

5. After Phase F retrace reaches OB:
   type: "trade_setup"
   entry_price: midpoint of OB box
   sl_price: below OB low with small buffer
   tp_price: previous structural high (or higher)
   direction: "long"
   start_ms: duration_ms - 2000
   purpose: Show the entry — always the last overlay
```

---

## CONCEPT 3 — LIQUIDITY

### What Liquidity Is

Liquidity is where stop losses are clustered. Institutions need liquidity to fill large positions. They move price to where retail traders have their stops, activate those stops, then reverse.

**Where liquidity forms:**
- **Equal Highs (EQH):** Two or more highs at the same price level. Retail traders sell at double/triple tops and place stop losses ABOVE the highs. This creates buy-side liquidity above.
- **Equal Lows (EQL):** Two or more lows at the same level. Retail traders buy at double/triple bottoms with stops BELOW. This creates sell-side liquidity below.

**The rule:** Before a large move UP, sell-side liquidity (below equal lows) must be swept first to fuel the move. Before a large move DOWN, buy-side liquidity (above equal highs) must be swept.

### Liquidity Grab vs BOS — Critical Distinction

This is the most important distinction in the liquidity concept:

**Liquidity Grab (NOT a BOS):**
- One candle wicks aggressively THROUGH the equal highs or lows
- The candle BODY stays near or below the level
- Price IMMEDIATELY reverses in the opposite direction after the wick
- The large wick IS the grab — it represents stop losses being triggered
- This is manipulation: institutions spiked price to collect stops before reversing

**Break of Structure (BOS — NOT a grab):**
- A candle CLOSES BEYOND the level — the body closes above/below
- Price does NOT immediately reverse
- Price continues in the direction of the break
- Creates a new structural point (new high or new low)

**Visual test:** Wick through + close on the other side + immediate reversal = GRAB. Close beyond + continuation = BOS. Never confuse them.

### Candle Sequence — Liquidity Sweep (Buy-Side, then Reversal Down)

Total: 18-22 candles.

```
PHASE A — Uptrend Context (4-5 candles):
  HH-HL structure. Bullish. Shows price has been rising.

PHASE B — Equal Highs Formation (4-6 candles):
  Price reaches a high, pulls back, then rises again to the SAME high level.
  Second peak is at approximately the same price as the first.
  Optionally a third touch (triple top) for even more liquidity.
  Candles must show: high1 ≈ high2 (within a few pips).
  These equal highs mark the buy-side liquidity pool.

PHASE C — Consolidation Below the Highs (2-3 candles):
  Price ranges just below the equal highs. Building more liquidity.
  Retail traders now selling at the "double top resistance."
  Their stop losses are ABOVE the equal highs = buy-side liquidity.

PHASE D — Spike Through / Liquidity Sweep (1-2 candles):
  ONE large candle. Very long upper wick that breaks ABOVE the equal highs.
  CRITICAL: The candle BODY must close BACK BELOW the equal high level.
  This is the grab — the wick swept the stops, the body shows rejection.
  Immediately followed by: large bearish candle or gap down.

PHASE E — Reversal / Bearish Expansion (3-4 candles):
  Large bearish candles. The move institutions actually wanted.
  Fuelled by all the liquidity grabbed in Phase D.
  Price moves sharply lower — clearly impulsive.

PHASE F — Continuation (2-3 candles):
  Continued bearish movement. Shows the reversal was real, not a retrace.
```

### Overlay Teaching Sequence — Liquidity Sweep

```
1. After Phase B last equal-high candle:
   type: "liquidity"
   label: "$$$ EQUAL HIGHS"
   price_level: the equal-high price
   candle_start: first equal-high candle
   candle_end: last equal-high candle
   swept: false
   purpose: Label the liquidity pool BEFORE it is swept

2. After Phase D spike candle finishes:
   type: "candle_label"
   text: "LIQUIDITY SWEEP"
   candle_index: the spike candle
   price_level: the high of the spike candle
   side: "right"
   purpose: Label the sweep at the moment it happens

3. 800ms after sweep label:
   type: "candle_label"
   text: "WICK = GRAB, NOT BOS"
   candle_index: the spike candle
   price_level: the close of the spike candle
   side: "left"
   purpose: Teach the critical distinction — wick closed back below

4. After Phase E second candle finishes:
   type: "floating_label"
   text: "SELL-SIDE NOW IN CONTROL"
   candle_index: Phase E second candle
   price_level: close of that candle
   color: "#ef5350"
   purpose: Confirm the direction after the sweep

5. Optional — if Phase A created an OB:
   type: "order_block"
   label: "SUPPLY ZONE"
   direction: "bullish" (last bullish candle before Phase E bearish impulse)
   purpose: Show where the institutional entry was
```

---

## CANDLE DATA QUALITY RULES

**Impulsive candles must LOOK impulsive:**
A typical context candle range: 8-15 pips. A typical impulsive candle range: 30-60 pips. The visual difference must be obvious. Viewers identify momentum by candle size. If your impulse candles are the same size as your context candles, the concept will not be visible.

**Equal highs must BE equal:**
For a liquidity concept, equal highs must share the same high price (or within 1-2 pips). candles[A].h ≈ candles[B].h. Do not generate "equal highs" where one high is 10 pips above the other — that is not a valid liquidity pool.

**FVG must be mathematically present:**
For bullish FVG: candles[X].l must be GREATER than candles[X+2].h.
If this condition is not met, there is no FVG. Adjust your candle prices until the gap exists.

**BOS candle must CLOSE beyond structure:**
The BOS candle's CLOSE (not its high) must exceed the structural high.
If candles[bos_index].c <= structural_high, it is NOT a valid BOS. Adjust.

---

## OVERLAY REFERENCE

All overlays use real price values and candle indexes. Never use y_pct or x_pct.

```
order_block:      candle_index, price_top, price_bottom, direction, label
demand_zone:      price_top, price_bottom, candle_start, label
supply_zone:      price_top, price_bottom, candle_start, label
fvg:              price_top, price_bottom, candle_start, label
bos_label:        candle_index, price_level, direction (up/down)
liquidity:        price_level, candle_start, candle_end, label, swept (true/false)
candle_label:     text, candle_index, price_level, side (left/right)
floating_label:   text, candle_index, price_level, color
trade_setup:      entry_price, sl_price, tp_price, candle_start, direction, rr_ratio
```

Overlay stagger pattern:
- First overlay: X ms (after candle finishes)
- Second overlay: X + 800ms
- Third overlay: X + 1600ms
- trade_setup: always last — start_ms = duration_ms - 2000

---

## HOOK_TEXT EXAMPLES PER CONCEPT

**Valid Demand Zone:**
- "Three things make a demand zone valid. Most traders know zero of them."
- "This is the zone. Most traders never see it until price already left."
- "Before you mark a demand zone — make sure it earned the label."

**Order Block:**
- "Most traders see a candle. Smart Money sees an order block."
- "This is where institutions left their footprint. Price always comes back."
- "The last bearish candle before the explosion. That is your order block."

**Liquidity:**
- "Your stop loss is not safe. It is a target."
- "Equal highs are not resistance. They are fuel."
- "This is why price always spikes before reversing."

---

## WHAT NOT TO DO

**Do not label an OB before the BOS exists.** The impulse must close a candle beyond structure before any OB label appears.

**Do not label a demand zone before showing the FVG and BOS as steps.** Each condition gets its own label first. The zone label is always last.

**Do not generate 400 candles of slowly drifting price.** That is not a concept. That is noise. Every candle serves the story.

**Do not generate impulsive candles that are the same size as context candles.** Momentum must be visually obvious.

**Do not show a wick through structure and label it BOS.** A wick through = liquidity grab. A candle CLOSE beyond = BOS. This distinction must be reflected in your candle data.

**Do not generate equal highs that are not equal.** If the highs differ by more than 3 pips, it is not a valid liquidity pool.

**Do not exceed 30 candles.** Count your candles before outputting.

**Do not use candle_interval_ms below 400.** Viewers cannot see candles drawing at less than 400ms each.

**Do not generate stt_timestamps.** That field does not exist in v2.

---

## FINAL CHECKLIST BEFORE OUTPUTTING

- [ ] hook_text is one punchy line — no stt_timestamps anywhere
- [ ] candles.length <= 30
- [ ] visible_count equals candles.length exactly
- [ ] candle_interval_ms >= 400
- [ ] duration_ms = 1800 + (candles.length × candle_interval_ms) + 4000
- [ ] Every candle: h >= max(o,c) AND l <= min(o,c)
- [ ] Impulsive candles are 3-5× larger than context candles
- [ ] FVG is mathematically present: candles[X].l > candles[X+2].h
- [ ] BOS candle CLOSE is beyond the structural point
- [ ] All overlay start_ms >= candle finish times
- [ ] Overlays appear in teaching order (evidence first, label last)
- [ ] Minimum 800ms between consecutive overlays
- [ ] trade_setup is last overlay: start_ms = duration_ms - 2000
- [ ] All overlays use price values and candle indexes — no percentages

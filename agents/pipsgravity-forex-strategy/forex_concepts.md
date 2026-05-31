# FOREX CONCEPTS — PipsGravity
# Source: PipsGravity Academy Course + Mastermind Trading Plan
#
# STRUCTURE OF THIS FILE:
# Every concept follows the same format:
#   DEFINITION — what it is
#   VALID CONDITIONS — all must be met for the concept to be used
#   INVALID CONDITIONS — what disqualifies it
#   CONNECTS TO — what it requires before it and enables after it
#
# This hierarchy matters. A concept cannot be placed on a chart unless
# ALL of its valid conditions are met. Concepts build on each other.
# The connection rules define how they chain together into a setup.

---

## MARKET STRUCTURE

### DEFINITION
Markets move in sequences of highs and lows.
BULLISH: Higher Highs (HH) → Higher Lows (HL) → Higher Highs
BEARISH: Lower Lows (LL) → Lower Highs (LH) → Lower Lows

### VALID CONDITIONS
- At least 2 swing points visible to establish the trend direction
- Each swing must be clearly higher (bullish) or lower (bearish) than the previous
- The structural high = highest wick of the swing candle that set the high
- The structural low = lowest wick of the swing candle that set the low

### INVALID CONDITIONS
- A single candle alone cannot establish market structure
- Sideways/choppy price action with no clear HH-HL or LL-LH = no structure

### CONNECTS TO
- Requires: nothing (market structure is the foundation)
- Enables: BOS, CHoCH, identifying the swing high/low for liquidity formation

### STRONG vs WEAK STRUCTURE
Strong zone: broke through the opposite zone with momentum
Weak zone: failed to break the opposite zone
Never trade weak demand when supply is in control above it.

### TIMEFRAME ALIGNMENT
Higher TF (H4, H1): identify direction and key zones — never enter here
Mid TF (15m, 5m): find unmitigated zones and confirmation
Lower TF (1m): refine entry
Sequence: Weekly→4hr→15min→1min OR Monthly→Daily→1hr→5min

---

## LIQUIDITY

### DEFINITION
Liquidity = clusters of retail stop losses. Smart Money (banks, institutions)
needs existing stop losses to fill their large orders. They move price INTO
liquidity pools to trigger those stops, filling their orders, then push price
in the intended direction. "Price has fuel" = liquidity collected = orders filled.

Marked on charts with $$$ signs (PipsGravity convention).

### TYPES OF LIQUIDITY

#### EQUAL LOWS (EQL) / EQUAL HIGHS (EQH)
Two or more highs/lows at virtually the same price.
Retail sees double/triple top or bottom and places stops just beyond.

VALID CONDITIONS (EQL):
  1. Minimum 2 touches at the same level
  2. Touch lows within 3 pips of each other: abs(touch1.l - touch2.l) <= 0.0003
  3. Visible bounce between touches: price rises >= 10 pips from each low before returning
  4. Level must be visually obvious — a clear flat horizontal line
INVALID: touches more than 3 pips apart — that is NOT equal lows
INVALID: no visible bounce between touches — looks like one continuous drop

VALID CONDITIONS (EQH): mirror of EQL, same rules for highs

CONNECTS TO:
  Requires: market structure context (forming during a trend or consolidation)
  Enables: liquidity sweep (price must eventually take these stops)
  In OB setups: EQL forms in Phase B, gets swept in Phase C/D

#### TRENDLINE LIQUIDITY
Diagonal trendline with 3+ touches. Retail places stops beyond the trendline.

VALID CONDITIONS:
  1. Minimum 3 touches along the diagonal slope
  2. Each touch within 5 pips of the slope price: abs(candles[N].h - trendline_at_N) <= 0.0005
  3. Clear diagonal angle — not nearly flat
  4. Bounces visible between touches (retail reaction at each touch)
INVALID: fewer than 3 touches
INVALID: flat line — that is range/EQL liquidity, not trendline

CONNECTS TO:
  Requires: clear trending context (LH-LL for downtrend, HL-HH for uptrend)
  Enables: trendline sweep → momentum after

#### RANGE LIQUIDITY
Horizontal range with multiple touches at both top and bottom.

VALID CONDITIONS:
  1. Minimum 2 touches at resistance (top) AND 2 touches at support (bottom)
  2. Top touches within 3 pips of each other
  3. Bottom touches within 3 pips of each other
  4. Clear consolidation character — price bouncing between levels
INVALID: only one side has touches

### LIQUIDITY SWEEP — VALID CONDITIONS
A sweep occurs when price raids the liquidity level and returns.
ALL THREE required:
  1. Wick reaches and EXCEEDS the liquidity level (touches are not enough)
  2. Candle BODY closes BACK on the original side of the level
  3. Next 1-2 candles move impulsively AWAY from the level
INVALID: body closes BEYOND the level — that is a BOS, not a sweep
INVALID: no impulsive move after — the sweep had no fuel

### SWEEP vs BOS — CRITICAL DISTINCTION
Sweep: wick through + body closes back = liquidity grab = REVERSAL expected
BOS:   candle body CLOSES beyond = structure break = CONTINUATION expected
Never confuse them. They are opposite signals.

### CONNECTS TO (Liquidity in general)
  Requires: visible stops accumulated at a clear level
  Enables: the move AFTER the sweep (OB formation, displacement, CHoCH)
  In OB setup: sweep confirms the OB candle that formed during/before the sweep

---

## ORDER BLOCK (OB)

### DEFINITION
The last candle of OPPOSITE colour before an impulsive move that caused a BOS.
This is where Smart Money loaded their positions before pushing price.
Price returns to this zone to offer retail a late entry while Smart Money
adds more positions before the continuation.

### VALID CONDITIONS — ALL REQUIRED
  1. LIQUIDITY FIRST: there must be a liquidity level (EQL/EQH/trendline) that was
     swept BEFORE or DURING the OB formation. No liquidity sweep = no valid OB.
  2. OB CANDLE COLOUR: for bullish OB = last BEARISH candle (c < o) before impulse.
     For bearish OB = last BULLISH candle (c > o) before impulse.
  3. OB POSITION: the OB candle must sit BELOW the swept liquidity level (bullish).
     Gap between liquidity level and OB_top >= 10 pips. This gap proves the sweep happened.
  4. IMPULSE AFTER: a strong impulsive move must immediately follow the OB candle.
     This impulse must leave a Fair Value Gap (see FVG conditions below).
  5. BOS AFTER: the impulse must break structure (BOS). No BOS = no confirmed OB.
  6. UNMITIGATED: the OB zone must not have been previously tested.
     A tested OB is no longer valid for entry.

### INVALID CONDITIONS
  - OB candle high is ABOVE the liquidity level (geometry is backwards)
  - No liquidity was swept before the impulse
  - No Fair Value Gap left by the impulse
  - No BOS after the impulse
  - OB already mitigated (price already returned to it)

### CONNECTS TO
  Requires: Liquidity (sweep) → OB candle → FVG → BOS (in that order)
  Enables: retracement back to OB → IDM fake entry → launch
  The OB is only valid BECAUSE of the three things that happened after it

### OB ZONE COORDINATES
  price_top    = OB candle high (full wick — not body only)
  price_bottom = OB candle low (full wick)
  direction    = "bearish" if OB candle is bearish (for bullish setup)

---

## FAIR VALUE GAP (FVG)

### DEFINITION
When price moves with very high momentum, it leaves a void — a gap between
candle 1's extreme and candle 3's extreme. The middle candle moved so fast
it left an area untested. Also called: imbalance, inefficiency, IFC.
Price commonly returns to this zone before continuing.

### VALID CONDITIONS — ALL REQUIRED
  1. THREE-CANDLE SEQUENCE: candle 1 (normal) → candle 2 (strong impulse) → candle 3 (normal)
  2. GAP EXISTS: for bullish FVG: candles[C3].l > candles[C1].h (no overlap)
     For bearish FVG: candles[C3].h < candles[C1].l (no overlap)
  3. MINIMUM SIZE: gap >= 10 pips. Smaller gaps are invisible on screen and not tradeable.
  4. ORIGIN: in OB setups, the FVG must originate FROM the OB candle.
     candle 1 = OB candle, candle 2 = first impulse, candle 3 = second impulse.
     This proves the imbalance started from the institutional zone.
  5. MOMENTUM: candle 2 must be clearly larger than the surrounding context candles.

### INVALID CONDITIONS
  - Gap < 10 pips — too small to see
  - Candle 3 low overlaps candle 1 high (not a real gap)
  - FVG forms away from the OB candle (gap from a random impulse, not institutional)

### CONNECTS TO
  Requires: a strong impulse move (displacement from the OB)
  Enables: IDM fake entry (retail enters at FVG thinking price will bounce there)
  In OB setup: FVG sits between the OB candle and the BOS level
  The IDM fake bounce must occur INSIDE the FVG zone

### FVG ZONE COORDINATES (bullish, from OB candle)
  price_bottom = candles[ob_index].h (OB candle high = left edge of gap)
  price_top    = candles[ob_index+2].l (second impulse candle low = right edge)
  candle_start = ob_index + 1 (the first impulse candle)

---

## BOS (BREAK OF STRUCTURE)

### DEFINITION
A candle BODY closes beyond a structural high (bullish BOS) or structural low
(bearish BOS). Confirms that the impulsive move has enough strength to change
structure. Signals continuation in the direction of the move.

### VALID CONDITIONS — ALL REQUIRED
  1. BODY CLOSE: the candle BODY must close beyond the structural level.
     A wick through is NOT a BOS — it is a liquidity sweep.
  2. STRUCTURAL LEVEL: must be a clear, previously established swing high/low.
     The structural high = highest wick of the Phase A0 peak candle.
  3. DISTANCE: body closes 5-15 pips beyond the structural level. No more.
     A 50-pip close beyond is continuation, not a BOS signal.
  4. ONE CANDLE: the BOS is confirmed by ONE candle (+ optional small follow-through).
     Do NOT continue the impulse after BOS — it should stop and retrace.
  5. CONTEXT: the BOS must come from the displacement that originated at the OB.
     A BOS from an unrelated move is not connected to the setup.

### INVALID CONDITIONS
  - Wick through + body closes back = NOT a BOS (that is a sweep)
  - Close more than 15 pips beyond the level = overkill, not a clean BOS signal
  - BOS candle is part of a continuing impulse (no pause after)
  - Structural level was not clearly established before the BOS

### CONNECTS TO
  Requires: displacement (strong impulse from the OB) that reaches the structural level
  Enables: retracement back toward the OB → IDM → entry
  The BOS is what CONFIRMS the OB is valid — without a BOS, the OB is unproven

### BOS OVERLAY COORDINATES
  candle_start = structural_high_candle_index (left anchor — the swing high candle)
  candle_index = bos_candle_index (right anchor — the candle that confirmed the BOS)
  price_level  = candles[structural_high_candle_index].h

---

## IDM (INDUCEMENT)

### DEFINITION
A convincing fake entry signal that forms during the retracement back toward
the OB/entry zone. Price reaches the FVG zone and bounces convincingly —
looking like a valid entry to retail traders. Those retail traders enter early.
Price then reverses and continues to the actual entry zone (OB/demand).
The IDM is not about sweeping stops. It is about creating a fake entry signal.

### VALID CONDITIONS — ALL REQUIRED
  1. POSITION: IDM must form INSIDE the FVG zone (between FVG price_bottom and price_top).
     This is where retail expects "price to fill the imbalance and bounce."
  2. SEPARATION: IDM level must be >= 15 pips above the entry zone top (OB_top).
     Without this space, the fake bounce has no room to form.
  3. CONVINCING BOUNCE: 2-3 clean bullish candles rising 20-35 pips from the IDM level.
     Must look like a real reversal starting. This is what traps retail.
  4. REVERSAL: after the fake bounce peak, 2-3 bearish candles decline back toward OB.
     No sweep required. A plain reversal is enough.
  5. ZONE RESPECT: no bounce candle may touch the entry zone top during the fake bounce.
  6. STRUCTURE RESPECT: no bounce candle may close above the last lower high in Phase F.

### INVALID CONDITIONS
  - IDM below the FVG zone (too close to OB — no room for the fake)
  - IDM above the FVG top (price never retraced into the imbalance)
  - Bounce is too small (<20 pips) — not convincing to retail
  - Bounce breaks structure (closes above Phase F lower highs) — invalidates the retrace

### CONNECTS TO
  Requires: FVG zone (IDM must be inside it), retracement from BOS
  Enables: the real entry at the OB/demand zone
  The IDM is valid BECAUSE the FVG creates a zone retail traders find credible

### IDM LABEL
  Label: "$$$ IDM" (not "$$$ ENTRY LQ" — IDM is the specific concept)
  swept:false overlay: appears when fake bounce forms
  swept:true overlay: appears when price reverses from the bounce
  Both swept:false and swept:true must use the SAME price_level

---

## CHOCH (CHANGE OF CHARACTER)

### DEFINITION
The first BOS in the OPPOSITE direction of the current trend.
Signals a potential reversal — the trend is changing character.

### VALID CONDITIONS
  1. Current trend must be clearly established (2+ swing points)
  2. Candle body closes beyond the OPPOSITE structural level (above high in downtrend)
  3. Most effective when price breaks through 2+ supply/demand zones
  4. Forms after a higher timeframe mitigation
INVALID: CHoCH on a single swing with no trend context

### CONNECTS TO
  Requires: established trend, higher TF mitigation
  Enables: new trend direction, new OB/demand zone in the new direction

STATUS: ❌ NOT READY for video generation — add course material first

---

## FLIP SETUP

### DEFINITION
A zone that was broken impulsively flips polarity. Old demand becomes supply.
Price retests the flipped level → entry.

### VALID CONDITIONS
  1. Price must create a new high, then test last demand zone
  2. Price FAILS to create new Higher High — instead breaks through last demand impulsively
  3. The broken demand zone left a supply zone behind
  4. Price retests the flipped zone from below
INVALID: gradual break (not impulsive) — must be aggressive push through the zone

### CONNECTS TO
  Requires: existing demand/supply structure, failed higher high
  Enables: entry at the retested flipped zone

STATUS: ❌ NOT READY for video generation — add course material first

---

## CONTINUATION SETUP

### DEFINITION
Used when price mitigated a zone and made a CHoCH/BOS and you missed the entry.
Entry is on the next unmitigated zone in the direction price is heading.

### VALID CONDITIONS
  1. A CHoCH or BOS has already occurred
  2. The initial entry zone was already mitigated (missed)
  3. An unmitigated zone exists in the direction of the move
  4. TP = next unmitigated opposite zone
INVALID: no confirmed CHoCH/BOS first

STATUS: ❌ NOT READY for video generation — add course material first

---

## EQUILIBRIUM ENTRY

### DEFINITION
When the zone candle has a very long wick (>50% of total candle height),
use the 50% level (midpoint) of the candle as the entry zone instead of
the full candle range. Avoids getting stopped before the real move.

### VALID CONDITIONS
  1. Zone candle wick > 50% of total candle height (high to low)
  2. Applied only to OB/demand/supply candles
  3. Entry = candle midpoint (50% between high and low)
INVALID: wick <= 50% of candle — use full candle range instead

### CONNECTS TO
  Modifies: OB entry price calculation
  The midpoint rule replaces the standard midpoint entry when wick is dominant

STATUS: ❌ NOT READY for video generation — add course material first

---

## AVAILABLE SETUPS FOR VIDEO GENERATION

| Setup | Concepts Required | Status |
|---|---|---|
| Bullish Order Block | EQL + Sweep + OB + FVG + BOS + IDM | ✅ READY |
| Bearish Order Block | EQH + Sweep + OB + FVG + BOS + IDM | ✅ READY |
| Valid Demand Zone | EQL + Sweep + FVG + BOS + Zone + IDM | ✅ READY |
| Valid Supply Zone | EQH + Sweep + FVG + BOS + Zone + IDM | ✅ READY |
| Equal Lows Sweep | EQL + Sweep | ✅ READY |
| Equal Highs Sweep | EQH + Sweep | ✅ READY |
| Trendline Liquidity | Trendline + Sweep | ✅ READY |
| Range Liquidity | Range + Sweep (both sides) | ✅ READY |
| FVG Standalone | FVG only | ✅ READY |
| BOS Standalone | BOS only | ✅ READY |
| CHoCH Reversal | CHoCH | ❌ NOT READY |
| Flip Setup | Flip | ❌ NOT READY |
| Continuation | Continuation | ❌ NOT READY |
| Equilibrium Entry | Equilibrium | ❌ NOT READY |
# FOREX CONCEPTS — PipsGravity
# Source: PipsGravity Academy Course + Mastermind Trading Plan

This is the concept dictionary. Pure definitions only.
No candles. No prices. No JSON. No phases.
This file makes Claude a trader first, before it draws anything.

---

## MARKET STRUCTURE

Markets move in sequences of highs and lows.

BULLISH STRUCTURE: Higher Highs (HH) → Higher Lows (HL) → Higher Highs
BEARISH STRUCTURE: Lower Lows (LL) → Lower Highs (LH) → Lower Lows

STRONG STRUCTURE: A zone is strong if it broke through the opposite zone.
  Example: A demand zone that broke through a supply zone above it = Strong Demand.
WEAK STRUCTURE: A zone that failed to break the opposite zone = Weak.
  Weak Demand cannot break Weak Supply and become Strong. It needs to break Strong Supply.

The safest trades follow the main trend direction on higher timeframes.
Counter-trend trades exist but are unpredictable.

TIMEFRAME ALIGNMENT (PipsGravity approach):
  Higher TF (H4, H1): Identify overall direction and key zones. Never take entries here.
  Mid TF (15m, 5m): Find unmitigated zones and confirmation.
  Lower TF (1m): Refine entry.
  Sequence: Weekly→4hr→15min→1min OR Monthly→Daily→1hr→5min

WHO IS IN CONTROL:
  Supply in control: price comes from an unmitigated supply zone above.
  Demand in control: price comes from an unmitigated demand zone below.
  Never trade demand when supply is in control. Wait for a flip or CHoCH first.

---

## SUPPLY AND DEMAND ZONES (Order Blocks)

A supply/demand zone is where price pushed away rapidly, leaving a Fair Value Gap and breaking structure.
These are also called Order Blocks. The terminology differs but the concept is identical.

THREE CONDITIONS for a valid zone (ALL required):
  1. Created an inefficiency (Fair Value Gap / imbalance)
  2. Broke or changed structure (BOS or CHoCH)
  3. Created or took liquidity

DEMAND ZONE (bullish): Area where price pushed up rapidly. Entry for longs.
SUPPLY ZONE (bearish): Area where price pushed down rapidly. Entry for shorts.

MITIGATED vs UNMITIGATED:
  Unmitigated = zone has not been tested yet = still valid for entry.
  Mitigated = zone was already tapped = used up, no longer valid.

VALID ZONE identification:
  1. Find where price formed momentum (the FVG / imbalance).
  2. Identify the last candle before the FVG formed — this is the zone candle.
     Note: does not always have to be the opposite-colour candle. If the FVG was created
     in a bullish market, an upside candle can be the zone as long as it was the last
     candle that created the inefficiency.
  3. Confirm structure break (close above/below the opposite zone).

WICK AS ZONE: When the zone candle has a wick larger than 50% of the whole candle,
  use the wick midpoint (equilibrium / 50%) as the zone entry instead of the full candle.
  This is the Equilibrium Entry — avoids being stopped before price moves in direction.

ZONE STRENGTH:
  High probability: price pushed away rapidly with large candles.
  Low probability: slow, grinding departure with small candles.

---

## FAIR VALUE GAP (FVG)

Also called: imbalance, inefficiency, IFC, unfairness. They are all the same thing.

Definition: When price moves with very high momentum, it leaves a void — a gap between
candle 1's high and candle 3's low (bullish) or candle 1's low and candle 3's high (bearish).
The middle candle moved so fast it left an area untested.

WHY IT MATTERS: Price commonly returns to fill this gap before continuing.
Combined with supply/demand zones and liquidity, FVGs help avoid early entries.

Bullish FVG: candle[3].low > candle[1].high (gap above candle 1, below candle 3)
Bearish FVG: candle[3].high < candle[1].low (gap below candle 1, above candle 3)
Minimum visible size: 10 pips. Smaller gaps are not worth marking.

---

## BOS (BREAK OF STRUCTURE)

Price candle BODY closes above a structural high (bullish BOS) or below a structural low (bearish BOS).
A wick through is NOT a BOS. The body must close beyond the level.

BOS = continuation signal. Price is continuing in the same direction.
After a BOS, unmitigated zones in that direction become valid entry targets.

Used in: Continuation setups. Confirming that a demand or supply zone is valid (Strong).

---

## CHOCH (CHANGE OF CHARACTER)

The first BOS in the OPPOSITE direction of the current trend.
Signals a potential reversal — trend is changing character.

CHoCH is most effective when price breaks through 2 or more supply/demand zones.
CHoCH forms after a higher timeframe mitigation — price impulsively breaks through zones
with few large candles.

Valid CHoCH: candle body must close above (bullish CHoCH) or below (bearish CHoCH)
the opposite supply/demand zone.

Used in: Reversal setups. Identifying when demand or supply takes control.

---

## FLIP SETUP

Price created a new high, tested the last demand zone, pushed away — but FAILED to create
a new Higher High. Instead it broke through the last demand zone with an impulsive move,
leaving a supply zone behind.

The old demand zone that was broken FLIPS to become supply.
Price retests this flipped level → entry.

Most effective when: price aggressively pushes away from zones AND rapidly breaks through
the last demand zone, leaving inefficiency behind.

Flip Types: the course documents multiple flip variations — the core logic is the same.
A zone that fails to hold and gets broken impulsively becomes the entry on retest.

---

## CONTINUATION SETUP

Used when: price mitigated a zone, made a CHoCH or BOS, and you missed the entry.
Or: price went to a higher timeframe zone and broke through it, continuing direction.

Entry: on the next unmitigated zone in the direction price is heading.
TP: next unmitigated opposite zone.

Continuation = scaling in after the move has started. Lower risk than reversal entries.

---

## EQUILIBRIUM ENTRY

When the zone candle has a very long wick (wick > 50% of the total candle height):
  Use the 50% level of the candle (midpoint between high and low) as the entry zone.
  This avoids entries that get stopped out before price moves in your direction.

Only apply when wick is bigger than 50% of the whole candle.

---

## LIQUIDITY

WHAT IT IS:
Liquidity = clustered stop losses. Smart Money (banks, institutions) needs existing stop
losses to fill their large positions. They move price INTO liquidity pools to trigger those
stops, filling their orders, then push price in the intended direction.

"Price has fuel to move" = liquidity has been collected = institutional orders are filled.

Marked on charts with $$$ signs (PipsGravity convention).

TYPES OF LIQUIDITY:

### DOUBLE/TRIPLE TOPS AND BOTTOMS (Equal Highs / Equal Lows)
Two or more highs at the same price = EQH. Two or more lows at the same price = EQL.
Retail traders see these as resistance/support and place stops just beyond them.
Institutions sweep through to collect those stops then reverse.
Most powerful as confirmation when formed above/below a supply/demand zone.
Mathematical: within 3 pips of each other (0.0003).

### TRENDLINE LIQUIDITY
A trendline with 3+ touches creates clustered stops below (uptrend) or above (downtrend).
Retail places stops at the trendline. Breakout traders place orders on a trendline break.
Both groups create stops at the trendline area.
Institutions sweep through the trendline with a wick, close back on the original side,
collect all the stops, then move in the opposite direction with momentum.
This is one of Wilfred's favourite setups.

### LIQUIDITY GRAB vs BOS — CRITICAL DISTINCTION
LQ Grab: ONE impulsive wick movement, large wick, body closes BACK on the original side.
  = stops collected, move continues in the original direction (pro-trend)
BOS: candle body CLOSES beyond the structural level.
  = structure broken, direction has changed

Never confuse them. A wick through + close back = grab. A body close beyond = BOS.

### ENTRY TRAPS
False signals designed to trap retail entries before the real move.
Price looks like it's reversing → retail enters → price sweeps their stops → real move begins.
The IDM (Inducement) pattern is an entry trap used in the Mastermind Trading Plan.

---

## AVAILABLE SETUPS FOR VIDEO GENERATION

Setups currently ready to generate (source: old SKILL.md allowed concepts):

| Setup                         | Type    | Notes |
|-------------------------------|---------|-------|
| Bullish Order Block           | TYPE_2  | Full setup with IDM entry |
| Bearish Order Block           | TYPE_2  | Mirror of bullish |
| Valid Demand Zone             | TYPE_2  | Evidence-first teaching angle |
| Valid Supply Zone             | TYPE_2  | Mirror of demand |
| Equal Lows Sweep (EQL)        | TYPE_1  | Concept only, no entry |
| Equal Highs Sweep (EQH)       | TYPE_1  | Concept only, no entry |
| Trendline Liquidity           | TYPE_1  | Wilfred's favourite — trendline sweep |
| Fair Value Gap (standalone)   | TYPE_1  | Show what FVG is and how it fills |
| BOS (standalone)              | TYPE_1  | Show what BOS is and how to confirm it |
| Range Liquidity               | TYPE_1  | Both sides swept |
| Liquidity Sweep (generic)     | TYPE_1  | Generic grab concept |

NOT READY YET (do not generate): CHoCH, Flip, Continuation, Equilibrium Entry
These require additional course material to be added to the improvement document first.

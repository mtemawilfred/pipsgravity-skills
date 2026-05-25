---
name: pipsgravity-forex-strategy
description: The complete PipsGravity forex trading strategy. Teaches Claude exactly how Wilfred identifies supply/demand zones, order blocks, fair value gaps, BOS, CHoCH, flip setups, liquidity, and entries. Every rule comes directly from the PipsGravity Academy course document. Load this skill before generating any forex educational content, chart scenes, or strategy explanations for PipsGravity.
---

## WHO THIS IS FOR

This skill teaches you the PipsGravity forex strategy exactly as taught in the PipsGravity Academy. Every rule, condition, and sequence in this document comes directly from that course. When generating candle sequences, overlays, or explanations for PipsGravity content, you must follow these rules precisely — not a generic textbook version, not ICT theory in isolation, but this specific framework.

---

## TERMINOLOGY NOTE

In this strategy, Supply/Demand zones and Order Blocks refer to the same thing. The terminology is interchangeable. What matters is the concept — not the label. Imbalance, inefficiency, fair value gap, and IFC (Inefficiency/Fair Value Gap) all mean the same thing.

---

## SECTION 1 — MARKET STRUCTURE

### How to identify market structure

Market structure is identified by tracking Higher Highs (HH), Higher Lows (HL) for uptrends, and Lower Highs (LH), Lower Lows (LL) for downtrends. The safest and most profitable trades follow the main trend.

**Main trend vs Counter trend:**
- Main trend moves are impulsive — large, fast candles with momentum
- Counter trend moves are corrective — smaller, slower, overlapping candles
- Always trade the main trend. Counter trend trades exist but are unpredictable.
- The direction is confirmed by where price is coming from relative to supply and demand zones.

### Weak vs Strong Structures

**Strong structure:** A structural point (high or low) is STRONG if it has broken through the opposite zone.
- Example: A demand zone that broke through a supply zone above it becomes STRONG demand.
- Strong highs and lows are valid targets and reference points.

**Weak structure:** A structural point is WEAK if it failed to break through the opposite zone and rejected from it.
- Weak highs and lows are more likely to be broken.
- A weak demand breaking a weak supply does NOT make either of them strong.

**Why this matters for candle sequences:**
When building a bullish candle sequence, the highs that get broken must be STRONG highs — previously created by demand that broke through supply above. Weak highs that get broken do not create a valid BOS. This is the most common mistake in generic content: showing BOS on a weak structural point.

---

## SECTION 2 — SUPPLY AND DEMAND ZONES (ORDER BLOCKS)

### What is a valid Supply/Demand zone?

A Supply/Demand zone (Order Block) is valid only when ALL THREE of these conditions are met:

1. **It created a Fair Value Gap (inefficiency/imbalance)** — price moved so fast from that level that it left an unfilled gap between two candles' wicks. The momentum proves there were significant orders at that level.

2. **It broke structure (BOS) or changed character (CHoCH)** — the move from the zone must be strong enough to break the previous structural point. A zone that price gently drifted away from is NOT valid.

3. **It created or took liquidity** — the zone formed near equal highs/lows (where stop losses are clustered) or swept liquidity before reversing.

**A zone without all three conditions is not tradeable in this strategy.**

### How to identify the Order Block candle

The Order Block is **the last candle before the Fair Value Gap forms**. This is not always the opposite-direction candle — that is a textbook rule that does not hold in practice.

**Correct identification:**
- Find where price created momentum (Fair Value Gap / inefficiency)
- Go back to the candle immediately before that momentum started
- THAT candle is the Order Block
- It can be bullish or bearish regardless of what direction the FVG moved

**Example for a bullish demand:**
Price drops down, then suddenly launches up with large impulsive candles leaving a gap. The Order Block is the LAST candle before that impulsive launch — could be a small bearish candle, a doji, or even a bullish candle. What matters: it was the last candle before the FVG.

**Example for a bearish supply:**
Price rallies up, then suddenly collapses with large impulsive candles leaving a gap downward. The Order Block is the LAST candle before that collapse.

### What makes a high-probability Order Block?

High probability zones have these characteristics:
- Price pushed **rapidly** away — not slowly, not gradually. Large candles, strong momentum.
- Left a clear Fair Value Gap (visible gap between candles)
- Broke structure or changed character on the move away
- The zone has NOT been previously tapped (it is unmitigated/unused)
- Price has not returned to that level yet

### When a wick is the zone, not the candle body

When price created a very large wick before the impulse, the valid zone is the WICK — not the candle body. This is because:
- The orders that caused the reaction are sitting at the wick level
- The candle body has already been mitigated by the following candle
- Marking the candle body in this case leads to early entries that get stopped out

**Visual test:** If the following candle closes beyond the first candle's body (but not beyond its wick), the wick is the valid zone.

### Mitigated vs Unmitigated Zones

- **Unmitigated (unused):** Price has not returned to this zone yet. Valid for entries.
- **Mitigated (used):** Price has already tapped into this zone. No longer valid for entries.

When price taps a zone for the first time, it becomes mitigated. After mitigation:
- Continuation setups use the next unmitigated zone in the same direction
- Reversal setups wait for CHoCH confirmation

---

## SECTION 3 — FAIR VALUE GAP (FVG)

### What is a Fair Value Gap?

A Fair Value Gap is the space between candle 1's wick and candle 3's wick when candle 2 moves with such momentum that it leaves a void. Price will usually return to fill this void.

**How to identify it:**
- Three consecutive candles
- Candle 2 is large and impulsive (the momentum candle)
- There is a gap between Candle 1's low (for bullish FVG) and Candle 3's high (for bullish FVG)
- That gap = the Fair Value Gap

**In candle data:**
- Bullish FVG: candles[X].l (low of candle before impulse) > candles[X+2].h (high of candle after impulse)
- Bearish FVG: candles[X].h (high of candle before impulse) < candles[X+2].l (low of candle after impulse)

### How FVG works with Order Blocks

The Order Block is not complete without the FVG. Together they form the entry zone:
- The Order Block candle marks WHERE to enter
- The FVG confirms THAT a valid zone exists
- When both align at the same price level, it is the highest probability entry

Price coming back to fill the FVG is often the entry trigger.

---

## SECTION 4 — BOS vs CHoCH

This is the most important distinction in the strategy. Getting this wrong invalidates the entire candle sequence.

### BOS — Break of Structure (Continuation)

**Definition:** Price breaks a structural point IN THE SAME DIRECTION as the current trend.

**In an uptrend:** A new Higher High breaks the previous HH. This is a BOS. It confirms the uptrend continues.

**In a downtrend:** A new Lower Low breaks the previous LL. This is a BOS. It confirms the downtrend continues.

**Conditions for a valid BOS:**
- The candle must CLOSE beyond the structural point — a wick through it alone is NOT a BOS
- The structural point being broken must be a STRONG high/low (not weak)
- The break must be impulsive (large candles, momentum, ideally leaving an FVG)

**What BOS tells you:** The trend is continuing. Look for continuation entries using the unmitigated demand/supply zones formed during the move.

**Candle sequence for BOS:**
Price in uptrend → makes HH → pulls back to HL → breaks the previous HH with a close above it → BOS confirmed → new demand zone formed during the pullback is the entry.

### CHoCH — Change of Character (Reversal)

**Definition:** Price breaks a structural point AGAINST the current trend, signaling a potential reversal.

**In an uptrend:** Price was making HH and HL. Then it breaks a HL (previous low) to the downside with a candle CLOSE below it. This is CHoCH. The trend may be reversing.

**In a downtrend:** Price was making LH and LL. Then it breaks a LH (previous high) to the upside with a candle CLOSE above it. This is CHoCH.

**Conditions for a valid CHoCH:**
- The candle must CLOSE beyond the structural point — wick only = NOT valid
- The break must happen AFTER price has tapped a Higher Timeframe zone (HTF mitigation is what triggers the reversal)
- Most effective when price breaks through 2 or more supply/demand zones in one move
- The move must be impulsive — few large candles, not many small candles

**What CHoCH tells you:** A potential trend reversal. This creates the first opportunity to trade in the new direction.

**Candle sequence for CHoCH:**
Price in downtrend (LH, LL) → taps a HTF demand zone → bounces strongly → closes ABOVE the previous LH → CHoCH confirmed → now look for buys → entry is on the flip/OB that created the CHoCH move.

### The critical difference for candle generation

When generating candles for a BOS video:
- Show price already in a trend (HH HL or LH LL)
- The BOS break is price continuing that trend, not reversing it
- The BOS candle CLOSES beyond the previous high (for bullish) or low (for bearish)
- A supply or demand zone forms during the pullback AFTER the BOS
- That zone is where the entry comes from

When generating candles for a CHoCH video:
- Show price in an established downtrend (LH LL) or uptrend (HH HL)
- Price taps a key HTF zone
- The CHoCH move impulsively breaks the previous opposite structural point with a close
- The entry zone is the OB/FVG from the CHoCH move

---

## SECTION 5 — ENTRY TYPES

### Type 1: The Flip

**What it is:** A former demand zone that price breaks through impulsively becomes a supply zone. Or a former supply zone that price breaks through becomes a demand zone. The level "flips" its role.

**How it works:**
1. Price creates a demand zone at level X
2. Price rises, makes a HH
3. Price comes back down to demand at level X but cannot push higher — the demand is WEAK
4. Price breaks impulsively through level X, leaving a new supply zone behind
5. Price retraces back to the new supply (formerly the demand)
6. Entry: limit order at the flipped supply zone

**What makes a flip high probability:**
- Price pushed aggressively away from the zone on the break (large candles, FVG left behind)
- The original demand gave weak rejection (couldn't create a new HH)
- The flip zone is unmitigated

**Two types of flips:**
- **Reversal flip:** The flip happens after a CHoCH — signals full trend reversal
- **Continuation flip:** The flip happens within the trend — the previous demand broke, now supply continues the downtrend

### Type 2: CHoCH Entry

After CHoCH is confirmed, the entry is on the first pullback to the Order Block/FVG that created the CHoCH move. This is often the best entry because:
- You are entering in the new trend direction
- The entry zone was the origin of the impulsive CHoCH move
- Risk is tight (stop below the OB, target is the next HTF supply/demand)

### Type 3: Continuation (BOS Entry)

After a BOS confirms the trend continues:
1. Price made a BOS (closed above previous HH in uptrend)
2. A demand zone formed during the pullback that led to the BOS
3. Price may retrace to that demand zone
4. Entry: limit order at that demand zone
5. TP: next unmitigated supply zone or the target that caused the BOS

Use this when you missed the flip or CHoCH entry and need to scale in.

### Equilibrium Entry (50% entry)

When the Order Block candle has a very long wick that is bigger than 50% of the whole candle, use the 50% level of the ENTIRE candle (not the body) as the entry zone. This prevents getting stopped out by the wick before price moves in your direction.

---

## SECTION 6 — LIQUIDITY

### What is liquidity?

Liquidity is where stop losses are clustered. Smart Money (banks, institutions) needs liquidity to fill their large positions. They move price to where retail traders have their stop losses, activate those stops, then reverse in the opposite direction.

**Where liquidity forms:**
- Equal highs (two or more highs at the same level) — marked as `$$$`
- Equal lows (two or more lows at the same level) — marked as `$$$`
- Trendline highs/lows (retail traders place stops below/above trendlines)
- Double tops and double bottoms

### Liquidity Grab vs BOS

**Liquidity grab:** One impulsive spike through the liquidity level, leaving a large wick. Price immediately reverses after sweeping the stops. Confirms that smart money has collected orders and will now move the other way.

**BOS:** Price breaks through a level and CLOSES beyond it with momentum. Does not immediately reverse — continues in the break direction.

**How to tell the difference:**
- Liquidity grab: large wick through the level, candle body stays near/below the level
- BOS: candle CLOSES beyond the level, no immediate reversal

### How liquidity is used in this strategy

Liquidity levels above supply or below demand are targets and confirmation:
- If liquidity forms below a demand zone, it confirms that the demand is likely to be targeted (price will sweep the lows before reversing up from demand)
- If liquidity forms above a supply zone, the supply is likely to be targeted
- A sweep of liquidity + reaction from a zone = very high probability entry

---

## SECTION 7 — THE FULL MARKET APPROACH (How trades are actually found)

This is the complete top-down process. Every trade follows this sequence.

### Step 1: Higher Timeframe — Define who is in control

Timeframe pairs used in this strategy:
- **Intraday:** H4 and H1 as higher timeframes, 15m/5m/1m for entries
- **Scalping:** 15m as higher timeframe, 1m for entries

On the higher timeframe (H4):
- Mark all unmitigated supply and demand zones
- Determine if supply or demand is in control (where is price coming from?)
- These zones are NOT entry zones — they are directional references and targets
- If price is coming from an unmitigated HTF supply, supply is in control — only look for sells
- If price is coming from an unmitigated HTF demand, demand is in control — only look for buys

### Step 2: Mid Timeframe — Find the entry zones

On the mid timeframe (15m or 1m):
- Find unmitigated supply or demand zones aligned with the HTF direction
- Wait for either a flip, CHoCH, or BOS to confirm the direction on this timeframe
- The entry zone is the OB/FVG that caused the confirmation move

### Step 3: Entry

Enter at the unmitigated zone after confirmation. The three valid entry triggers:
1. **Flip** — zone flipped its role, entry at the new zone
2. **CHoCH** — trend changed, entry at the OB that created CHoCH
3. **BOS continuation** — trend confirmed, entry at the demand that created BOS

**Who is in control determines whether you trade:**
- Price coming from UNMITIGATED supply → supply is in control → only take sells
- Price coming from MITIGATED supply → demand is in control → take buys
- Never trade against the controlling side without confirmation (CHoCH)

### Step 4: Targets

- TP at the next unmitigated opposite zone (nearest supply for longs, nearest demand for shorts)
- HTF supply/demand levels are the ultimate targets
- Risk: 2% on funded accounts, 5% on personal account
- Hold intraday trades within the session (close before session ends)
- Move stop to breakeven if high-impact news is approaching

---

## SECTION 8 — CANDLE SEQUENCE RULES FOR VIDEO GENERATION

When generating candle data for CHART_SCENE videos about any PipsGravity concept, follow these exact rules.

### For Order Block (Demand) videos:
1. Show 4-6 candles of bearish context (price moving down) — these establish the trend
2. Show price moving with some momentum but not impulsively — building a LH LL structure
3. Show 1-2 small candles (the base) — these will become the Order Block
4. Show 3-5 large bullish impulsive candles launching upward (the FVG-creating move)
5. The FVG is visible between the base candles and the top of the impulse
6. Optionally show price coming back down toward the OB zone (the retrace)

The Order Block overlay marks the LAST candle before the impulsive move — not the first candle of the move.

### For Order Block (Supply) videos:
1. Show 4-6 candles of bullish context
2. Show 1-2 small candles at the top (the Order Block)
3. Show 3-5 large bearish impulsive candles launching downward
4. FVG visible between the OB candles and the bottom of the impulse

### For BOS videos:
1. Show an established uptrend: HH → HL → HH pattern across 8-12 candles
2. The final BOS: a candle that CLOSES above the previous HH with momentum
3. The BOS candle must be large and impulsive — not a small candle barely closing above
4. After the BOS, show a pullback forming a new demand zone
5. Mark the BOS at the exact candle that CLOSES above the previous high

### For CHoCH videos:
1. Show an established downtrend: LH → LL → LH pattern across 8-12 candles
2. Price taps into a key demand zone (this is the HTF mitigation)
3. Price launches upward impulsively from that demand
4. A candle CLOSES above the previous LH — this is the CHoCH
5. The CHoCH candle must close beyond the level — not just wick through it
6. Mark CHoCH at the exact candle close, not before

### For Flip videos:
1. Show a demand zone forming at level X
2. Price rises, makes a high (HH)
3. Price comes back down to the demand zone but shows WEAK reaction (small candles, doesn't push far)
4. Price breaks through the demand with a large bearish impulsive move (FVG left)
5. The broken demand level is now marked as supply (the FLIP)
6. Price retraces back up to the supply/flip zone

### For Liquidity Sweep videos:
1. Show price forming equal highs or equal lows across 4-6 candles (the liquidity pool)
2. Mark the equal highs/lows with a liquidity marker
3. Price spikes aggressively THROUGH the level with one or two large candles
4. Large wick is visible — the body stays near the level
5. Price immediately reverses sharply away from the liquidity
6. Show the demand/supply zone where the reversal came from

---

## SECTION 9 — WHAT NOT TO DO

These are common mistakes that produce incorrect content:

**Do not mark a BOS on a wick:** BOS requires a candle CLOSE beyond the structural point. A wick through the level is a liquidity grab, not a BOS.

**Do not show the Order Block before the FVG exists:** The OB is identified by the FVG. If there is no FVG, there is no valid OB. The OB overlay must appear after the impulsive move has started.

**Do not mark mitigated zones as entry zones:** Once price has tapped a zone, it is used. Show only unmitigated zones as entry areas.

**Do not confuse LQ grab and BOS:** They look similar but are opposite signals. LQ grab → price reverses. BOS → price continues.

**Do not show CHoCH before the impulsive break:** CHoCH is confirmed only when the candle CLOSES beyond the structural point. The label appears at the close of that candle — not before.

**Do not mark a weak structural high as a BOS level:** Only strong highs (those that previously broke structure) are valid BOS reference points.

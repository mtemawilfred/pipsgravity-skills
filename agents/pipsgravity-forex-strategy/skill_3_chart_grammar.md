# SKILL 3 — CHART GRAMMAR ENGINE
# PipsGravity Chart Scene Pipeline
# Role: Blueprint Renderer. Read Skill 2. Resolve anchors. Generate candles. Output JSON.

---

## OUTPUT RULE — READ THIS FIRST, BEFORE ANYTHING ELSE

Your output is ONE thing: a raw JSON object starting with {

No explanation before the JSON.
No reasoning before the JSON.
No step-by-step walkthrough before the JSON.
No "I'll work through this systematically."
No planning text.
No preamble of any kind.

All anchor resolution, price planning, constraint checking, and phase generation
happens INTERNALLY. None of it appears in your output.

The moment you start generating output: output {
The moment you finish: the JSON object closes with }
Nothing else.

If you write ANY text before the opening { you have failed this instruction.

---

## YOUR ROLE

You receive a skeleton from Skill 2. You convert it into candles.

Skill 2 has already decided everything: what concepts to teach, what phases exist,
how many candles each phase has, what each anchor must satisfy, and every numerical
constraint. Your job is to render those decisions into OHLC candle data.

You do NOT invent rules. You do NOT add constraints Skill 2 did not define.
You do NOT redesign, reinterpret, or improve anything.

If Skill 2 says BOS = 5-15 pips, you generate a BOS candle 5-15 pips above the high.
If Skill 2 says EQL max difference = 2 pips, you make the two lows within 2 pips.
If Skill 2 says FVG minimum = 10 pips, you ensure the gap is at least 10 pips.

Read. Resolve. Generate. Output.

---

## GENERATION PHILOSOPHY — THE ONLY RULE THAT MATTERS

Skill 3 is NOT a chart validator.
Skill 3 does NOT generate charts and then check if they are valid.
Skill 3 generates charts that are IMPOSSIBLE to be invalid.

The execution model is a dependency chain. Each structure produces locked values.
The next structure is built using those locked values as hard inputs.

```
STEP 1: Generate structure N
STEP 2: Immediately lock all outputs from structure N
STEP 3: Generate structure N+1 using locked outputs as inputs
STEP 4: Repeat
```

LOCKED VALUES TABLE — built up as each phase completes:

| After Phase | Value locked | Used by |
|---|---|---|
| C (OB) | OB_TOP, OB_BOTTOM | Phase D (FVG), Phase F (IDM), Phase G (launch) |
| C (Sweep) | SWEEP_CLOSE | Phase D candle_b open |
| A (swing high) | STRUCTURAL_HIGH | Phase E (BOS close) |
| D (FVG) | FVG_LOCKED_LOW | candle_c.l — never changes after set |
| E (BOS) | TP_PRICE = max Phase D+E high | Phase G last candle must reach this |
| F (IDM low) | IDM_LEVEL | Phase G sweep candle |

CONSTRAINT ENFORCEMENT RULE:
When a candle violates a locked value: ADJUST THE CANDLE, not the locked value.
The locked value is truth. The candle must conform to it.

Wrong:
  candle_c.l generated as 1.0420
  FVG_LOCKED_LOW is 1.0526
  → report error, output invalid chart

Correct:
  candle_c.l generated as 1.0420
  FVG_LOCKED_LOW is 1.0526
  → set candle_c.l = FVG_LOCKED_LOW = 1.0526
  → recompute h = max(o, c, 1.0526 + wick) to maintain OHLC validity
  → continue

LAUNCH RULE — TP is not a target, it is a hard exit condition:
  Wrong:  generate 5 Phase G candles, check if last candle reaches TP
  Correct: keep generating Phase G candles until last candle.c >= TP_PRICE

Validation at the end is a sanity check only. If the dependency chain was followed,
validation should never fail. If it does fail, the locked value was not enforced
during generation — find where and fix it.

---

## STEP 1 — READ THE BLUEPRINT

Before writing a single candle, extract these values from the Skill 2 skeleton:

```
phase_structure        = skeleton.blueprint.phase_structure
                         e.g. { A0:4, A:5, B:10, C:2, D:4, E:1, F:10, G:5 }

setup_type             = skeleton.blueprint.setup_type
bias                   = skeleton.blueprint.bias  (bullish or bearish)
scene_goal             = skeleton.blueprint.scene_goal (default: trade_setup)

constraints            = skeleton.market_skeleton.[phase].success_condition
                         + skeleton.educational_constraints
                         + skeleton.confirmation_rules

anchor_contracts       = skeleton.anchor_contracts
idm_formation          = skeleton.market_skeleton.phaseF.idm_formation
strength_hierarchy     = skeleton.strength_hierarchy
```

NUMERICAL CONSTRAINTS — read directly from Skill 2, never invent your own:

| Constraint | Where to read it |
|---|---|
| EQL max difference | confirmation_rules.EQL.max_difference_pips |
| EQL minimum bounce | educational_constraints.minimum_bounce_between_eql_touches |
| Sweep minimum depth | educational_constraints.minimum_sweep_depth_pips |
| Displacement minimum | educational_constraints.minimum_displacement_pips |
| FVG minimum gap | confirmation_rules.FVG.minimum_gap_pips |
| BOS minimum break | confirmation_rules.BOS.minimum_break_pips |
| BOS maximum break | confirmation_rules.BOS.maximum_break_pips |
| IDM minimum bounce | confirmation_rules.IDM.minimum_bounce_pips |
| IDM minimum candles | confirmation_rules.IDM.minimum_bullish_candles |
| OB minimum reaction | educational_constraints.minimum_ob_reaction_pips |

---

## STEP 2 — RESOLVE ANCHORS

Build the phase start map first. Then resolve every anchor to an absolute candle index.
All downstream work uses absolute indices only.

### BUILD PHASE START MAP

Compute this ONCE. Store every value. Never recompute or approximate later.

```
phase_structure = read from skeleton.blueprint.phase_structure
phase_starts = {}
running = 0
for each phase in [A0, A, B, C, D, E, F, G]:
    phase_starts[phase] = running
    running += phase_structure[phase]
total_candles = running
```

For skeleton with {A0:4, A:5, B:10, C:2, D:4, E:1, F:10, G:5}:
```
A0 starts at 0,  ends at 3   (candles 0-3)
A  starts at 4,  ends at 8   (candles 4-8)
B  starts at 9,  ends at 18  (candles 9-18)
C  starts at 19, ends at 20  (candles 19-20)
D  starts at 21, ends at 24  (candles 21-24)
E  starts at 25, ends at 25  (candle 25)
F  starts at 26, ends at 35  (candles 26-35)
G  starts at 36, ends at 40  (candles 36-40)
total = 41 candles
```

CRITICAL: Phase B contains EXACTLY phase_structure[B] candles (e.g. 10).
The last Phase B candle is phase_starts[B] + phase_structure[B] - 1.
The first Phase C candle is phase_starts[C]. These are DIFFERENT candles.
The EQL touches happen INSIDE Phase B. They cannot be in Phase C.
If eql_touch_2 lands on or after phase_starts[C], you miscounted — fix it.

### RESOLVE EACH ANCHOR — COMPUTED VALUES, NOT GUESSES

Compute each value explicitly from phase_starts. Write the number down. Use it everywhere.

| Anchor | Formula | Example result |
|---|---|---|
| swing_high_idx | scan candles[phase_starts[A] to phase_starts[A]+phase_structure[A]-1], return index of highest .h | e.g. 4 |
| eql_touch_1_idx | phase_starts[B] + anchor_contracts.eql_touch_1.typical_offset | e.g. 9+2=11 |
| eql_touch_2_idx | phase_starts[B] + anchor_contracts.eql_touch_2.typical_offset | e.g. 9+8=17 |
| ob_idx | phase_starts[C] + 0 | e.g. 19 |
| sweep_idx | phase_starts[C] + 1 | e.g. 20 |
| fvg_candle_a_idx | ob_idx | e.g. 19 |
| fvg_candle_b_idx | ob_idx + 1 = phase_starts[D] | e.g. 21 |
| fvg_candle_c_idx | ob_idx + 2 = phase_starts[D] + 1 | e.g. 22 |
| bos_idx | phase_starts[E] | e.g. 25 |
| idm_low_idx | phase_starts[F] + idm_formation.low_candle | e.g. 26+4=30 |
| idm_peak_idx | phase_starts[F] + idm_formation.peak_candle_offset | e.g. 26+8=34 |
| idm_sweep_idx | phase_starts[G] | e.g. 36 |
| launch_idx | phase_starts[G] + 3 | e.g. 39 |

VERIFY before generating any candle:
  eql_touch_1_idx < eql_touch_2_idx < ob_idx  (EQL touches must be in Phase B, before Phase C)
  ob_idx = phase_starts[C]                     (OB is always first candle of Phase C)
  fvg_candle_b_idx = phase_starts[D]           (candle_b is always first candle of Phase D)
  If any of these is false: recompute phase_starts. Do not proceed until they pass.

---

## STEP 3 — GENERATE CANDLES PHASE BY PHASE

Generate each phase in sequence. Track a running candle index starting at 0.

### OHLC NORMALIZATION — EVERY CANDLE, AUTOMATIC, NO EXCEPTIONS

After setting o and c for any candle, IMMEDIATELY compute h and l as follows:

```
h = max(intended_h, o, c)
l = min(intended_l, o, c)
```

This is not a check. This is how h and l are assigned.
Never set h and l independently and hope they are correct.
Always derive them from o and c plus any desired wick extension.

Pattern for every candle:
  1. Decide o (open price)
  2. Decide c (close price)
  3. Decide wick extensions: upper_wick and lower_wick (both >= 0)
  4. Set h = max(o, c) + upper_wick
  5. Set l = min(o, c) - lower_wick

Result is always OHLC-valid by construction. No violation possible.

NEVER do this:
  o=1.0686, h=1.0690, l=1.0656, c=1.0560  ← c below l — impossible

ALWAYS do this:
  o=1.0686, c=1.0560                        ← decide open and close first
  h = max(1.0686, 1.0560) + 0.0004 = 1.0690  ← high is above both
  l = min(1.0686, 1.0560) - 0.0006 = 1.0554  ← low is below both

### STARTING PRICE
Use 1.0700 EUR/USD. Adjust candles[0].o to start below the future OB zone for bullish,
above for bearish.

### CANDLE BODY SIZES BY SWING TYPE

| Swing type | Body range (pips) | Direction |
|---|---|---|
| rise | 8-12 | bullish |
| drop | 8-12 | bearish |
| small_bounce | 3-6 | bullish |
| small_drop | 3-6 | bearish |
| drift_up | 5-10 | bullish |
| drift_down | 5-10 | bearish |
| bounce | 8-14 | bullish |
| rejection | 8-14 | bearish |
| large_impulse | 28-38 | directional |
| medium_impulse | 16-26 | directional |
| small_impulse | 10-16 | directional |
| single_spike | wick dominant, body 4-8 | directional |
| single_wick_pierce | wick dominant, body 4-8 | directional |
| single_close_beyond | 8-14 | directional |
| reach_tp | 14-22 | directional |
| mixed_small | 2-6 | mixed |

1 pip = 0.0001. Normal wicks: 20-50% of body. Sweep/spike candles: wick >= 2x body.

### PHASE A0 — Uptrend context
Generate rising candles following the swings list. Each candle bullish. Build readable
higher highs so the subsequent drop has clear context.

### PHASE A — Downtrend context
Generate falling candles. The highest high in this phase is swing_high_idx. Mark it.
BOS in Phase E will break this price. Make the swing high visually memorable.

### PHASE B — Equal lows
SET EQL_LEVEL FIRST before generating any Phase B candle.
Pick a price below Phase A's lows. This is the level both lows will touch.

Generate candles following the swings list:
- Touch 1: candles[eql_touch_1_idx].l = EQL_LEVEL exactly
- Bounce between touches: must rise minimum bounce_between_eql_touches pips (from Skill 2)
- Touch 2: candles[eql_touch_2_idx].l = EQL_LEVEL ± (max_difference_pips × 0.0001)
- VERIFY: abs(touch1.l - touch2.l) <= (max_difference_pips × 0.0001)

### PHASE C — OB candle + sweep (exactly 2 candles)

Candle 0 = Order Block (ob_idx):
- Bearish for bullish setup (c < o)
- Body: normal market candle — does not need to be huge. It must be clearly bearish.
- Sits below EQL_LEVEL. OB zone: price_top = candle.h, price_bottom = candle.l
- Set OB_TOP and OB_BOTTOM from this candle's h and l

Candle 1 = Sweep (sweep_idx):
- Wick pierces minimum_sweep_depth_pips below EQL_LEVEL
- Body closes back above EQL_LEVEL
- Wick must be >= 2x body (visually dominant)
- VERIFY: (EQL_LEVEL - candle.l) >= (minimum_sweep_depth_pips × 0.0001)

### PHASE D — Displacement (4 candles)

This is the LARGEST move on the chart. Every candle bullish for bullish setup.

COMPUTE ALL STRUCTURAL PRICES BEFORE WRITING ANY CANDLE.
These are not targets or guidelines — they are computed values that constrain the candles.

BEFORE GENERATING ANY PHASE D CANDLE — LOCK THESE VALUES:

  OB_TOP           = candles[ob_idx].h          (read from generated Phase C)
  SWEEP_CLOSE      = candles[sweep_idx].c        (read from generated Phase C)
  STRUCTURAL_HIGH  = candles[swing_high_idx].h   (read from generated Phase A)

  FVG_LOCKED_LOW   = OB_TOP + (minimum_fvg_size_pips × 0.0001)
                     e.g. OB_TOP=1.0516, min_gap=10 → FVG_LOCKED_LOW = 1.0526
                     THIS VALUE DOES NOT CHANGE. IT IS SET ONCE AND USED AS-IS.

  bos_break_target = pick between min_break_pips and max_break_pips (e.g. 8)
  BOS_CLOSE_TARGET = STRUCTURAL_HIGH + (bos_break_target × 0.0001)

NOW GENERATE EACH CANDLE USING LOCKED VALUES AS INPUTS:

  candle_b = candles[ob_idx+1]  (first Phase D candle — candle_b of FVG):
    o = SWEEP_CLOSE + 0.0002  (opens just above sweep close)
    c = o + 0.0030            (large bullish body, 28-38 pips)
    h = max(o, c) + 0.0003    (small upper wick)
    l = max(o, c) - 0.0002... but ENFORCE: l >= OB_TOP
        If computed l < OB_TOP → set l = OB_TOP + 0.0001
        This preserves the gap — candle_b cannot dip into OB range.

  candle_c = candles[ob_idx+2]  (second Phase D candle — candle_c of FVG):
    l = FVG_LOCKED_LOW          ← THIS IS THE ONLY VALID VALUE FOR l
                                   Assign it directly. Never derive it from o or c.
    o = candle_b.c + 0.0002     (opens above candle_b close)
    c = o + 0.0020              (medium bullish body, 16-26 pips)
    h = max(o, c) + 0.0003      (small upper wick)
    l = FVG_LOCKED_LOW          ← RESTATE IT. This is the floor. No wick goes below.

  candle D3 = candles[ob_idx+3]:
    o = candle_c.c + 0.0002
    c = o + 0.0018              (medium bullish body)
    h = max(o, c) + 0.0003
    l = min(o, c) - 0.0002

  candle D4 = candles[ob_idx+4]:
    o = candle_D3.c + 0.0001
    c = approaches BOS_CLOSE_TARGET (small bullish body, 10-16 pips)
    h = max(o, c) + 0.0002
    l = min(o, c) - 0.0002

  After D4: TP_PRICE = max(h of all Phase D and E candles)
            Lock TP_PRICE now. Phase G must reach it.

SANITY CHECK (should always pass if above was followed):
  gap = candles[ob_idx+2].l - candles[ob_idx].h
  This MUST equal FVG_LOCKED_LOW - OB_TOP = minimum_fvg_size_pips × 0.0001
  If gap is negative: candle_c.l was not assigned correctly. Fix it now.

### PHASE E — BOS (1 candle)

One candle. Body closes above structural_high.
  close = structural_high + randomly between (min_break × 0.0001) and (max_break × 0.0001)
  VERIFY: candles[bos_idx].c > structural_high + (min_break × 0.0001)
  VERIFY: candles[bos_idx].c <= structural_high + (max_break × 0.0001)

### PHASE F — Retracement + IDM (candle_count from Skill 2)

Generates in two parts:

PART 1 — Plain retrace (idm_formation.retrace_candles):
  Alternating drops and small bounces. Candle bodies 4-8 pips.
  REQUIRED: by the end of Part 1, price must be approaching the OB zone.
  retrace_low_target = OB_TOP + 0.0005 (price must reach within 5 pips of zone top)

PART 2 — IDM pattern (remaining Phase F candles):

  IDM low candle (idm_low_idx):
    l = IDM_LEVEL
    IDM_LEVEL must satisfy: OB_TOP + 0.0008 <= IDM_LEVEL <= OB_TOP + 0.0015
    (8-15 pips above OB zone top — close enough to be a convincing trap)

  Bounce candles (idm_formation.bounce_candles — minimum 3):
    Each candle bullish, body 8-14 pips, rising clearly
    Total rise from IDM_LEVEL >= (minimum_idm_bounce_pips × 0.0001)
    VERIFY: min 3 bullish candles. VERIFY: total rise >= minimum.

  Peak candle (idm_peak_idx):
    Highest close of the bounce sequence.

  Decline candles (idm_formation.decline_candles — minimum 2):
    Bearish, bodies 10-16 pips, falling toward OB zone.

  At end of Phase F: at least one candle must have l <= OB_TOP (price enters zone).

### PHASE G — IDM sweep + launch (candle_count from Skill 2)

Candle 0 = IDM sweep (idm_sweep_idx):
  Wick pierces below IDM_LEVEL. Body closes above IDM_LEVEL.
  This candle also enters or touches the OB zone: l <= OB_TOP
  Wick >= 2x body.

Candle 1 = zone tap (price_returns):
  Small candle, 5-8 pip body. Opens at or inside OB zone.
  price_returns.candle_index = this candle.

Candle 2 = hesitation:
  1 small mixed candle, 3-6 pips. Price pausing inside zone.

Candles 3+ = launch:
  launch_candle = phase_starts[G] + 3 (or wherever hesitation ends)
  First launch candle: 24-32 pip body bullish
  Subsequent candles: medium → medium → smaller, each bullish

  TP_PRICE = max high of all Phase D and Phase E candles (locked after Phase E)

  LAUNCH CONTINUES UNTIL TP_PRICE IS REACHED. This is not a target — it is a stop condition.
  Keep generating bullish Phase G candles until last_candle.c >= TP_PRICE.
  Do not stop at the phase_structure[G] count if TP has not been reached —
  add extra candles to Phase G until the condition is satisfied.
  The final Phase G candle close must be >= TP_PRICE.

  After the last Phase G candle closes at or above TP_PRICE: stop. Output the chart.
  Update visible_count and duration_ms to reflect the actual candle count.

---

## STEP 4 — OUTPUT

OUTPUT IS JSON ONLY. No text before {. No text after }.
All reasoning is internal. The output IS the JSON object, nothing else.

```json
{
  "scene_id": 1,
  "render_type": "CHART_SCENE",
  "duration_ms": <candles.length × 500 + 4000>,
  "phase_d_start": <phase_starts[D]>,
  "phase_e_end": <phase_starts[E]>,
  "setup_type": "<from skeleton>",
  "video_type": "<from skeleton>",
  "scene_goal": "<from skeleton, default trade_setup>",
  "brand": {
    "primary": "#1B2A4A", "accent": "#C9A84C",
    "danger": "#991B1B", "success": "#166634",
    "font_heading": "Oswald", "font_body": "Inter"
  },
  "chart": {
    "start_ms": 0,
    "candles": [ { "o": number, "h": number, "l": number, "c": number } ],
    "candle_interval_ms": 500,
    "visible_count": <candles.length>,
    "background": "white",
    "bullish_color": "#2563EB",
    "bearish_color": "#1B2A4A"
  },
  "structures": {
    "macro_liquidity": {
      "type": "eql",
      "price_level": <EQL_LEVEL>,
      "touch_1_candle": <eql_touch_1_idx>,
      "touch_2_candle": <eql_touch_2_idx>,
      "confirmed_at": <eql_touch_2_idx>,
      "phase_start": <phase_starts[B]>,
      "phase_end": <ob_idx - 1>
    },
    "order_block": {
      "anchor_candle": <ob_idx>,
      "price_top": <candles[ob_idx].h>,
      "price_bottom": <candles[ob_idx].l>,
      "direction": "bearish",
      "confirmed_at": <phase_starts[D] + 3>
    },
    "fvg": {
      "candle_a": <ob_idx>,
      "candle_b": <ob_idx + 1>,
      "candle_c": <ob_idx + 2>,
      "price_top": <candles[ob_idx + 2].l>,
      "price_bottom": <candles[ob_idx].h>,
      "confirmed_at": <ob_idx + 2>
    },
    "bos": {
      "structural_high_candle": <swing_high_idx>,
      "structural_high_price": <candles[swing_high_idx].h>,
      "bos_candle": <phase_starts[E]>,
      "bos_close_price": <candles[phase_starts[E]].c>,
      "confirmed_at": <phase_starts[E]>
    },
    "entry_liquidity": {
      "type": "<from skeleton.blueprint.entry_liquidity.type>",
      "requested_entry_liquidity": "<from skeleton>",
      "generated_entry_liquidity": "<what was drawn>",
      "price_level": <IDM_LEVEL>,
      "sweep_candle": <idm_sweep_idx>,
      "bounce_start_candle": <idm_low_idx + 1>,
      "bounce_peak_candle": <idm_peak_idx>,
      "reversal_start_candle": <idm_peak_idx + 1>,
      "confirmed_at": <idm_peak_idx + idm_formation.decline_candles>,
      "phase_start": <phase_starts[F]>,
      "phase_end": <phase_starts[F] + phase_structure[F] - 1>
    },
    "entry_zone": {
      "price_top": <OB_TOP>,
      "price_bottom": <candles[ob_idx].l>,
      "entry_price": <OB_BOTTOM + (OB_TOP - OB_BOTTOM) × 0.5>,
      "sl_price": <candles[ob_idx].l - 0.0010>,
      "tp_price": <max high of Phase D+E>,
      "launch_candle": <phase_starts[G] + 3>,
      "direction": "long"
    },
    "price_returns": {
      "candle_index": <first Phase G candle with l <= OB_TOP>,
      "price_level": <that candle's l>
    },
    "sweep_candle": {
      "candle_index": <sweep_idx>,
      "confirmed_at": <sweep_idx>
    }
  },
  "resolved_anchors": {
    "swing_high_idx": <integer>,
    "eql_touch_1_idx": <integer>,
    "eql_touch_2_idx": <integer>,
    "sweep_idx": <integer>,
    "ob_idx": <integer>,
    "fvg_start_idx": <ob_idx + 1>,
    "bos_idx": <integer>,
    "idm_low_idx": <integer>,
    "idm_peak_idx": <integer>,
    "idm_sweep_idx": <integer>,
    "launch_idx": <integer>
  },
  "validation": {
    "eql_difference_pips": <round(abs(touch1.l - touch2.l) / 0.0001, 1)>,
    "eql_valid": <eql_difference_pips <= max_difference_pips from Skill 2>,
    "sweep_depth_pips": <round((EQL_LEVEL - sweep_candle.l) / 0.0001, 1)>,
    "sweep_valid": <sweep_depth_pips >= minimum_sweep_depth_pips from Skill 2>,
    "displacement_pips": <round((max Phase D high - OB_TOP) / 0.0001, 1)>,
    "displacement_valid": <displacement_pips >= minimum_displacement_pips from Skill 2>,
    "fvg_size_pips": <round((candles[ob+2].l - candles[ob].h) / 0.0001, 1)>,
    "fvg_valid": <fvg_size_pips >= minimum_fvg_size_pips from Skill 2>,
    "bos_break_pips": <round((candles[bos].c - structural_high) / 0.0001, 1)>,
    "bos_valid": <bos_break_pips >= min AND <= max from Skill 2>,
    "idm_bounce_pips": <round((candles[idm_peak_idx].c - IDM_LEVEL) / 0.0001, 1)>,
    "idm_valid": <idm_bounce_pips >= minimum_idm_bounce_pips AND bounce_candle_count >= minimum>,
    "retrace_reaches_ob": <true if min(Phase F lows) <= OB_TOP>,
    "price_enters_zone": <true if price_returns candle l <= OB_TOP>,
    "launch_reaches_tp": <true if last Phase G candle c >= tp_price>,
    "all_ohlc_valid": <true if h >= max(o,c) AND l <= min(o,c) for every candle>,
    "failure_reasons": ["<only populated when a check is false — state which constraint failed and by how much>"]
  }
}
```

---

## FINAL SANITY CHECK

If the generation philosophy was followed correctly, all of these should already be true.
If any is false: find where the locked value was not enforced and fix that candle only.

1. candles[ob_idx+2].l == FVG_LOCKED_LOW (FVG gap is exactly what was set)
2. candles[bos_idx].c is between structural_high + min_break and + max_break
3. Every candle: h >= max(o,c) AND l <= min(o,c)
4. EQL: abs(touch1.l - touch2.l) <= max_difference_pips × 0.0001
5. Sweep depth: (EQL_LEVEL - sweep_candle.l) >= minimum_sweep_depth_pips × 0.0001
6. Displacement: (max Phase D high - OB_TOP) >= minimum_displacement_pips × 0.0001
7. IDM bounce: (candles[idm_peak_idx].c - IDM_LEVEL) >= minimum_idm_bounce_pips × 0.0001
8. Price entered zone: at least one Phase F/G candle l <= OB_TOP
9. Last Phase G candle c >= TP_PRICE (if not: add more candles until it does)
10. candles.length matches visible_count and duration_ms = (candles.length × 500) + 4000
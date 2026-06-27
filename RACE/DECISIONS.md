# DECISIONS.md

# Architectural Decisions Log

---

## DECISION-001

### Use HTF-only detection

Decision:

```text
All market logic executes in request.security().
```

Reason:

Lower timeframe logic caused:

- repainting
- timeframe instability
- inconsistent sweeps

Status:

```text
PERMANENT
```

---

## DECISION-002

### Use xloc.bar_time

Decision:

```text
Use xloc.bar_time for all persistent objects.
```

Reason:

```text
bar_index changes across timeframes.
```

Status:

```text
PERMANENT
```

---

## DECISION-003

### Use confirmed HTF candles only

Decision:

```text
No intrabar HTF calculations.
```

Reason:

Prevent repainting.

Status:

```text
PERMANENT
```

---

## DECISION-004

### V8 becomes production baseline

Decision:

```text
V8 architecture is accepted.
```

Reason:

Successfully solved:

- HTF stability
- maturity
- repainting
- pivot alignment

Status:

```text
ACCEPTED
```

---

## DECISION-005

### Use lightweight event states

Decision:

Use:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

instead of enterprise state machines.

Reason:

Pine limitations.

Status:

```text
V9
```

---

## DECISION-006

### Delay BOS/CHOCH

Decision:

Do not implement:

- BOS
- CHOCH
- dealing ranges
- PD arrays

in V9.

Reason:

Complexity exceeds V9 objectives.

Status:

```text
V10
```

---

## DECISION-007

### Implement liquidity scoring

Decision:

Replace:

```text
important=true/false
```

with:

```text
LiquidityScore
0-100
```

Reason:

Institutional liquidity exists on a spectrum.

Status:

```text
V9
```

---

## DECISION-008

### Implement garbage collection

Decision:

Add dedicated GC engine.

Reason:

Prevent Pine resource exhaustion.

Status:

```text
V9
```

---

## DECISION-009

### LiquidityScore computed on sweep candle, passed through confirmation

Decision:

Compute LiquidityScore inside `f_htfStrict()` on the sweep candle,
store in `pLiquidityScore` var, pass to `eventLiquidityScore` on the
confirmation candle.

Reason:

All scoring must be HTF-only per DECISION-001. Computing on the sweep
candle avoids lookahead (sweep candle data is confirmed when the next
HTF candle opens). The score is stored in the pending event and
survives the 1-bar gap between sweep and confirmation.

Status:

```text
V9.2
```

---

## DECISION-010

### Score components weighted by institutional relevance

Decision:

| Component     | Weight | Rationale                            |
| ------------- | ------ | ------------------------------------ |
| Wick Size     | 30%    | Direct measure of liquidity reach    |
| ATR Ratio     | 20%    | Normalizes wick by market volatility |
| Pivot Quality | 20%    | Significant levels attract more flow |
| Extremity     | 15%    | Multi-timeframe importance           |
| Age           | 10%    | Mature liquidity is more reliable    |
| Bonus         | 5%     | Expandable for future confluences    |

Reason:

Institutional liquidity is not binary (ISSUE-002). A spectrum of
quality is required, with larger weights on direct wick characteristics
and structural significance.

Status:

```text
V9.2
```

---

## DECISION-011

### Rejection reason passed as 9th tuple element from f_htfStrict()

Decision:

```text
Use a string rejection reason as the 9th return element from f_htfStrict(),
set on the rejection candle, displayed via a single reusable var label.
```

Reason:

Debug markers (SW/NO) show _that_ a confirmation failed but not _why_.
The rejection reason string provides the specific cause ("Wick overlap
Bull ??? low < zone top") with price levels for diagnosis.

Architecture choices:

- String embedded in return tuple ??? consistent with existing 8-element pattern
- Single `var label` (moved on each rejection) ??? avoids label accumulation
- Reasons set inside `f_htfStrict()` before reset ??? keeps HTF-only per DECISION-001
- Dashboard integration via `lastEvent` ??? no new dashboard rows required

Status:

```text
V9.2.1
```

---

## DECISION-012

### Retroactive label creation in mitigation lifecycle loop

Decision:

```text
Create zone labels retroactively in the mitigation lifecycle loop
rather than in f_createZone().
```

Reason:

When `showLabels` is toggled ON after zone creation, the `labels[]`
array contains `na` entries for pre-existing zones. Since Pine Script v6
has no `label.set_visible()`, the only way to show labels for those zones
is to create them after the fact.

The mitigation lifecycle loop was chosen because it already iterates
every zone on every confirmed bar ??? adding a `na(lb)` check here is
zero-overhead when labels already exist, and automatically handles
retroactive creation when they don't.

Key design choices:

- Uses `z.confirmTime` for `xloc.bar_time` ??? label appears at correct historical position, not current bar
- Guarded by `showLabels and na(lb)` ??? only fires for zones without labels when labels are wanted
- `array.set(labels, i, newLb)` + `lb := newLb` ??? updates both the array and the local reference

Status:

```text
V9.2.2
```

---

## DECISION-014

### Continuous ConfirmationScore replaces binary nonOverlap

Decision:

```text
Replace the binary nonOverlap check with a continuous ConfirmationScore
(0-100) computed from 4 weighted sub-scores on the confirmation candle.
```

Components:

| Component    | Weight | Formula                                                     |
| ------------ | ------ | ----------------------------------------------------------- |
| Displacement | 40%    | `close - zone_boundary / ATR * 200` (saturates at 0.5x ATR) |
| Rejection    | 30%    | `100 - overlapPct * 100` (0-100 linear)                     |
| FVG          | 20%    | `FVG_gap / ATR * 333` (saturates at 0.3x ATR)               |
| Efficiency   | 10%    | `directional_range / total_range * 100` (0-100 linear)      |

Reason:

The binary nonOverlap gate loses information ??? a confirmation candle that
barely touches the zone tip is treated identically to one that fully engulfs
it. Continuous scoring preserves this nuance and allows users to set their
own quality threshold via `minConfirmScore`.

Default behavior is preserved at `minConfirmScore = 0` ??? all passing
confirmations still create zones.

Status:

```text
V9.3
```

---

## DECISION-013

### Retroactive label creation must run outside state-change guards

Decision:

```text
Retroactive label creation (if showLabels and na(lb)) must run
unconditionally in the mitigation loop ??? NOT inside
if oldStatus != z.status or boundsChanged.
```

Reason:

V9.2.2 placed the retroactive creation inside the state-change guard.
For zones that have not been touched (no mitigation, no invalidation),
neither condition ever changes ??? the guard blocks entry ??? label creation
code is never reached. The fix moved it outside the guard.

Design:

- Runs on every bar for every non-deleted zone
- After first creation, `na(lb)` is false ??? fast no-op on subsequent bars
- `f_setVisual` is called immediately after creation to set correct colors
- State-change updates (`box.set_top`, `box.set_right`, `label.set_x/y`) remain guarded

Lessons:

```text
Any unconditional action (label creation, logging) must NOT be gated
behind a conditional that may never trigger.
```

Status:

```text
V9.2.3
```

# CHANGELOG.md

# HTF Wick Sweep Order Block Indicator

## Evolution History

---

# V1

Initial proof of concept.

Features:

- basic wick detection
- basic order blocks

Problems:

- repainting
- unstable zones

---

# V2

Added:

- pivot detection
- HTF support

Problems:

- timeframe instability

---

# V3

Added:

- confirmation engine
- rejection logic

Problems:

- false positives

---

# V4

Added:

- maturity filtering
- stronger validation

Problems:

- pivot misalignment

---

# V5

Major architecture rewrite.

Added:

- HTF-centric processing

Problems:

- sweep inconsistencies

---

# V6

Added:

- multiple wick tracking
- sweep improvements

Problems:

- rendering issues

---

# V7

Added:

- maturity engine
- stability improvements

Problems:

- timeframe switching bugs

---

# V8

Production baseline.

Solved:

- HTF stability
- repainting
- pivot alignment
- HTF guards
- timeframe consistency

Status:

```text
PRODUCTION READY
```

---

# V9.3.1 ??? Timestamp Truncation Fix

Fixed a bug where zone `confirmTime` was stored with 1/10 the correct epoch-ms value, causing `xloc.bar_time` boxes to appear at year ~1975.

## Bug

Inside `f_htfStrict()`, `eventConfirmTime := time` assigned the HTF bar's timestamp to a local `int` variable. When returned as the 5th element through `request.security()`, the value was truncated from e.g. `1,782,489,600,000` to `178,248,960,000`.

This caused:

- Box `x1` positioned at year ~1975 instead of the correct date (2026)
- Label `H:` display showing the wrong epoch ms
- `currentHtfTime > z.confirmTime` comparison (full-precision vs 1/10-precision) still worked but was semantically incorrect

## Fix

Replaced `eventConfirmTime` with `currentHtfTime` (the built-in `time` returned as the 11th tuple element from `request.security()`) at every consumption point in zone creation:

| Field                    | Before (truncated) | After (correct)  |
| ------------------------ | ------------------ | ---------------- |
| `z.confirmTime`          | `eventConfirmTime` | `currentHtfTime` |
| `lastCreatedConfirmTime` | `eventConfirmTime` | `currentHtfTime` |
| `lastEventTime`          | `eventConfirmTime` | `currentHtfTime` |

## Root Cause

Pine Script truncates local `int` variables assigned from `time` when crossing the `request.security()` context boundary. The built-in `time` symbol, when packed directly into the return tuple, retains full epoch-ms precision. This is a Pine Script serialization behavior ??? not a logic error.

## Files Changed

- `htf_wick_sweep_ob.pine` ??? 6 lines changed in zone creation block (lines 364-377)

---

# V9.3 ??? ConfirmationScore

Replaced the binary `nonOverlap` confirmation gate with a continuous ConfirmationScore (0-100) computed from 4 sub-scores on the confirmation candle.

## Architecture Change

**Before**: `if nonOverlap ??? CONFIRM | else ??? REJECT` (binary pass/fail)

**After**: `if confScore >= minConfirmScore ??? CONFIRM | else ??? REJECT` (continuous score)

## Components

| Component    | Weight | Measurement                                              |
| ------------ | ------ | -------------------------------------------------------- |
| Displacement | 40%    | Confirmation close distance past zone, normalized by ATR |
| Rejection    | 30%    | Overlap avoidance ??? 100 = no overlap, 0 = full overlap |
| FVG          | 20%    | Fair Value Gap between sweep and confirm candles         |
| Efficiency   | 10%    | What % of the confirm candle range is directional        |

## Features

- Continuous scoring: allows partial overlaps with low scores instead of hard rejection
- `minConfirmScore` input (default 0) ??? user-configurable threshold
- ConfirmationScore stored per Zone UDT, displayed in labels and dashboard
- Rejection reason now shows sub-score breakdown: `Conf 23 < 50: D:10 R:30 F:0 E:40`
- Default (minConfirmScore = 0) preserves V29 behavior ??? all passing confirmations create zones

---

# V9.2.3 ??? Retroactive Label Fix + Per-X Rejection Labels

Fixed two bugs introduced in V9.2.2.

## Retroactive Zone Label Fix

**Bug**: V9.2.2 placed the retroactive label creation inside `if oldStatus != z.status or boundsChanged`. For stable zones (no mitigation, no invalidation), this condition was never true, so the label never got created.

**Fix**: Moved `if showLabels and na(lb)` OUTSIDE the state-change guard. Now runs unconditionally on every bar for every non-deleted zone. After first creation, `na(lb)` is false and the check is a fast no-op.

## Per-X Rejection Labels

**Bug**: V9.2.1 used a single `var label rejectLabel` that was deleted and recreated on each new rejection ??? only the most recent rejection had a visible label.

**Fix**: Replaced with `var label[] rejectLabels` array. Each rejection creates a new `label.new()` pushed to the array. Bounded at 50 ??? oldest auto-deleted via `array.shift()`. All rejection labels cleaned up when `showDebugMarkers` is toggled OFF. Added `chgFailDir != 0` guard to prevent duplicate labels from persistent `request.security()` values.

---

# V9.2.2 ??? CW10003 Fix + Retroactive Zone Labels (superseded by V9.2.3)

Two quality-of-life fixes.

## CW10003 Warning Fix

Extracted 6 stateful `ta.*` function calls from inside conditional blocks to function root:

- `ta.highestbars`, `ta.highest` (??2) ??? `bearAgeRaw`, `bearPivot`, `bearExtreme`
- `ta.lowestbars`, `ta.lowest` (??2) ??? `bullAgeRaw`, `bullPivot`, `bullExtreme`

All computed unconditionally on every HTF bar. Zero logic change ??? same values consumed at same points.

## Retroactive Zone Labels (BROKEN ??? fixed in V9.2.3)

V9.2.2's retroactive label creation was placed inside the state-change guard and did not work.

---

# V9.2.1 ??? Rejection Reason Display

Added rejection reason visibility for zone confirmation failures.

Features:

- Rejection reason string returned from `f_htfStrict()` as 9th tuple element
- Inline label at confirmation-fail candle showing cause ("Wick overlap Bull/Bear" with price levels)
- Dashboard "Last Event" row shows rejection summary ("NO: Bull ??? Wick Overlap")
- Single reusable `var label` ??? no accumulation, automatically cleaned up when debug markers are toggled off
- Zero changes to sweep/confirmation/zone lifecycle logic

---

# V9.2 ??? LiquidityScore

Implemented LiquidityScore engine.

Features:

- 5-factor institutional scoring within `request.security()`
- Wick Size Score (30%): sweep wick relative to candle range
- ATR Ratio Score (20%): sweep wick normalized by HTF ATR
- Pivot Quality Score (20%): is swept level an extreme over 2?? lookback?
- Extremity Score (15%): is swept level an extreme over 4?? lookback?
- Age Score (10%): liquidity maturity via `ta.highestbars()`
- Bonus Factor (5%): neutral baseline (expandable)
- Score stored per `Zone` UDT, displayed in labels and dashboard
- Color-coded dashboard cell (green ??? 70, orange 40-69, red < 40)
- All scoring inside `f_htfStrict()` ??? HTF-only, non-repainting

---

# V9

Previous development objectives:

- event states
- ~~liquidity scoring~~ ??? (V9.2)
- confirmation scoring
- zone scoring
- institutional ranking
- context engine
- garbage collection

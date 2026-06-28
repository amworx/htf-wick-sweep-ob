# LESSONS_LEARNED.md

# HTF Wick Sweep Order Block Indicator

## Lessons Learned & Historical Mistakes

---

# Purpose

This document records historical mistakes, their root causes, and the permanent solutions adopted by the project.

Future development MUST consult this file before introducing architectural changes.

---

# LESSON-001

## Problem

Zones changed when switching chart timeframes.

Example:

```text id="ljchqo"
HTF = 4H

1m chart:
Zone A

15m chart:
Zone B
```

---

## Root Cause

Sweep detection was performed on the chart timeframe rather than on the HTF.

Example:

```text id="1jv4zk"
HTF wick
+
LTF sweep
```

This caused different charts to generate different sweeps.

---

## Solution

Move all market detection into:

```pine id="f5p5ef"
request.security()
```

including:

- wick detection
- maturity detection
- sweep detection
- confirmation

---

## Status

```text id="ntudk5"
PERMANENT RULE
```

---

# LESSON-002

## Problem

Historical zones repainted.

---

## Root Cause

Intrabar calculations and unconfirmed candles.

Example:

```text id="v5h5ct"
current HTF candle
changes
after close
```

---

## Solution

Use:

```text id="rdbpjl"
barstate.isconfirmed
lookahead_off
closed HTF candles only
```

---

## Status

```text id="m6n4x6"
PERMANENT RULE
```

---

# LESSON-003

## Problem

Pivot zones appeared shifted.

---

## Root Cause

Current candle OHLC was used instead of pivot candle OHLC.

Example:

Wrong:

```pine id="6m9q4v"
high
low
```

Correct:

```pine id="sajyp9"
high[pivotLen]
low[pivotLen]
```

---

## Solution

Always retrieve OHLC from the actual pivot candle.

---

## Status

```text id="u3y9t9"
PERMANENT RULE
```

---

# LESSON-004

## Problem

Only one liquidity wick could exist at a time.

---

## Root Cause

Single-variable architecture.

Example:

```text id="cmx9r8"
bullishWick
bearishWick
```

---

## Solution

Replace with array-based tracking.

Example:

```text id="jxcyx8"
bullishWicks[]
bearishWicks[]
```

---

## Status

```text id="5fxeh3"
SOLVED
```

---

# LESSON-005

## Problem

Zones drifted when switching timeframes.

---

## Root Cause

Objects were anchored using:

```pine id="tm9rci"
bar_index
```

---

## Solution

Use:

```pine id="ncysp3"
xloc.bar_time
```

for all persistent objects.

---

## Status

```text id="j1nrn6"
PERMANENT RULE
```

---

# LESSON-006

## Problem

Mature liquidity was not distinguishable from fresh liquidity.

---

## Root Cause

Liquidity treated as:

```text id="vjlwmj"
valid/invalid
```

instead of:

```text id="lq4wfj"
probability spectrum
```

---

## Solution

Introduce:

```text id="ub9b2m"
LiquidityScore
0-100
```

---

## Status

```text id="ngnlfd"
V9
```

---

# LESSON-007

## Problem

Some legitimate institutional setups were missed.

---

## Root Cause

Single-pass architecture:

```text id="qjr5k8"
wick
??? sweep
??? confirmation
??? zone
```

cannot handle:

- delayed sweeps
- repeated sweeps
- delayed confirmations

---

## Solution

Implement event state management:

```text id="odbgd8"
TRACKING
???
SWEEP_PENDING
???
CONFIRM_PENDING
???
ACTIVE
???
DEAD
```

---

## Status

```text id="yjgv6f"
V9
```

---

# LESSON-008

## Problem

All zones appeared equally important.

---

## Root Cause

No institutional ranking.

---

## Solution

Implement:

```text id="ewh4di"
LiquidityScore
ConfirmationScore
ZoneScore
ZoneRank
```

---

## Status

```text id="grvhrr"
V9
```

---

# LESSON-009

## Problem

Pine resource exhaustion risk.

---

## Root Cause

Unlimited:

- arrays
- zones
- objects
- historical storage

---

## Solution

Introduce:

```text id="9r5vsz"
Garbage Collector
```

Limits:

```text id="nq7dzu"
75 wicks
50 active zones
100 historical zones
500 objects
```

---

## Status

```text id="jlwmqy"
V9
```

---

# LESSON-010

## Problem

Project scope kept expanding toward a complete ICT framework.

---

## Root Cause

Attempting to solve:

- correctness
- quality
- context

simultaneously.

---

## Solution

Development philosophy:

```text id="sdiepy"
V8 = correctness
V9 = quality
V10 = context
```

---

## Status

```text id="st29ye"
PERMANENT STRATEGY
```

---

# Golden Lesson

# LESSON-011

## Problem

Liquidity classification was binary (important/not important).

---

## Root Cause

The sweep detection logic only had pass/fail criteria ??? no spectrum
of institutional significance.

Example:

```text
Wick sweeps liqHigh ??? zone created (treated equally)
vs
Massive wick at major pivot ??? zone created (treated equally)
```

Both scenarios produced the same zone type.

---

## Solution

Implement LiquidityScore:

```text
LiquidityScore =
WickSize ?? 0.30 +
ATRRatio ?? 0.20 +
PivotQuality ?? 0.20 +
Extremity ?? 0.15 +
Age ?? 0.10 +
Bonus ?? 0.05
```

All computed inside `request.security()` on the sweep candle, stored
per zone, displayed in labels and dashboard.

---

## Status

```text
V9.2 ??? COMPLETE
```

---

# LESSON-012

## Problem

When a sweep confirmation failed (NO marker), there was no way to tell _why_.
Debug markers showed SW at the sweep candle and NO at the rejection candle,
but the cause of the rejection was invisible.

Example:

```text
SW ??? (sweep detected) ??? next candle ??? NO ??? (rejected)
                                        |
                                        ????????? why? ??? invisible
```

This made it impossible to:

- distinguish between different rejection causes
- trust the indicator's rejection logic (looked like false negatives)
- diagnose incorrect zone rejection patterns

---

## Root Cause

The rejection reason existed transiently inside `f_htfStrict()` but was
never returned to the main scope or displayed on chart.

The function returned 8 elements: direction, prices, timestamps, sweep/fail
debug flags, and liquidity score ??? but not the _reason_ for the fail flag.

---

## Solution

Add a 9th return element ??? a rejection reason string ??? set inside
`f_htfStrict()` on the rejection candle and returned to the main scope.
Display via:

1. A single reusable `var label` at the rejection candle (shows "Wick
   overlap Bull ??? low < zone top" with specific price levels)
2. The dashboard "Last Event" row ("NO: Bull ??? Wick Overlap")

Key design choices:

```text
- string, not int code ??? self-documenting, no lookup table needed
- 9th tuple element ??? backward compatible with existing 8-element return
- Single var label ??? moved on each rejection, never accumulated
- Only set on fail path ??? does not affect zone creation or success path
```

---

## Status

```text
V9.2.1 ??? COMPLETE
```

---

# LESSON-013

## Problem

Zones created while `showLabels=false` never displayed labels when
`showLabels` was toggled ON. Only the most recently created zone had
a visible label.

---

## Root Cause

Label creation happened only in `f_createZone()` ??? at the moment the
zone was created. If `showLabels` was false at that time, `label(na)`
was pushed to the `labels[]` array, and no code existed to replace it
with a real label later.

Pine Script v6 has no `label.set_visible()` function ??? once `na` is
stored, the label cannot be "unhidden."

---

## Solution

Add a retroactive label creation check in the mitigation lifecycle loop:

```pine
if showLabels and na(lb)
    // create label using stored zone data
    newLb = label.new(z.confirmTime, ...)
    array.set(labels, i, newLb)
    lb := newLb
```

Key design:

- Uses `z.confirmTime` for `xloc.bar_time` ??? correct historical position
- Guarded by `na(lb)` ??? one-time creation per zone, not repeated
- `array.set()` + `lb :=` ??? updates both the array and the local reference

---

## Status

```text
V9.2.2 ??? SUPERSEDED (see LESSON-014)
```

---

# LESSON-014

## Problem

The V9.2.2 retroactive label creation fix did not work. Even after
implementing the `if showLabels and na(lb)` block, older zones still
had no debug labels.

## Root Cause

The retroactive creation code was placed INSIDE a conditional guard:

```pine
// V9.2.2 ??? BROKEN:
if oldStatus != z.status or boundsChanged
    // ...box/label updates...
    if showLabels and na(lb)       // ??? never reached for stable zones
        create label...
```

For zones that never changed status or bounds (no mitigation, no
invalidation), `oldStatus == z.status` and `boundsChanged == false`
on EVERY bar. The outer guard blocked entry permanently.

## Solution

Move the retroactive creation OUTSIDE the state-change guard:

```pine
// V9.2.3 ??? CORRECT:
if showLabels and na(lb)           // ??? runs every bar, independent of state
    create label...
    f_setVisual(...)
if oldStatus != z.status or boundsChanged  // ??? still guards box/label updates
    ...
    f_setVisual(...)
```

## The General Principle

```text
Unconditional actions (creation, cleanup, logging) must never be
placed behind a conditional that can remain false indefinitely.

State-change guards protect UPDATE operations (moving, resizing).
CREATION operations must run whenever the precondition is met,
regardless of state change.
```

## Status

```text
V9.2.3 ??? COMPLETE
```

---

# LESSON-015

## Problem

The binary confirmation gate (`nonOverlap`) discarded information about
confirmation quality. A candle that barely touched the zone tip was treated
identically to one that fully engulfed it ??? both were simply "rejected."

## Root Cause

Boolean pass/fail gates lose edge-case information. The original design
treated confirmation as a binary event: either the candle avoided the zone
(pass) or didn't (fail). This made it impossible to distinguish between
different confirmation qualities.

## Solution

Replace the binary gate with a continuous ConfirmationScore (0-100)
computed from 4 weighted sub-scores:

```text
ConfirmationScore =
Displacement ?? 0.40 +
Rejection ?? 0.30 +
FVG ?? 0.20 +
Efficiency ?? 0.10
```

Where each sub-score captures a different aspect of confirmation quality:

- **Displacement**: how strongly price moved away from the zone
- **Rejection**: how cleanly the candle avoided the zone
- **FVG**: whether a Fair Value Gap exists
- **Efficiency**: directional ratio of the confirmation candle

The user controls the threshold via `minConfirmScore` (default 0 preserves
V29 behavior).

## The General Principle

```text
Binary classification gates discard edge-case information.
When two "failures" have different severity, continuous scoring
preserves the spectrum. Let the user choose the threshold.
```

## Status

```text
V9.3 ??? COMPLETE
```

---

# LESSON-016

## Problem

Zone boxes appeared at year ~1975 on the chart instead of the correct position (2026). The `xloc.bar_time` box x1 coordinate was wrong.

## Root Cause

Inside `f_htfStrict()`, the confirmation bar's timestamp was captured as:

```pine
eventConfirmTime := time   // local int inside request.security()
```

This local `int` variable was returned as the 5th tuple element. When it crossed the `request.security()` context boundary back to chart scope, Pine Script **truncated the value to 1/10** of the correct epoch ms:

```text
Correct:     1,782,489,600,000  → 2026-06-26 16:00 UTC
Truncated:     178,248,960,000  → 1975-08-26
```

The root mechanism: inside `request.security()`, the `time` built-in returns the HTF bar's epoch-ms timestamp as a full 64-bit value. Assigning it to a local `int` variable (`eventConfirmTime := time`) is fine inside the function scope. But when that local `int` is packed into a tuple and serialized back through `request.security()`, the value loses precision — only ~36 bits survive instead of the full ~41 bits needed for current epoch ms values.

The **symptom** was that zone boxes (drawn with `box.new(sweepT, top, confirmT, bottom, xloc=xloc.bar_time)`) had their x1 at year ~1975 because `confirmT` (= `z.confirmTime`) had the truncated value.

## Solution

Avoid using local `int` variables for timestamps that must cross the `request.security()` boundary. Instead, return the bare `time` built-in as a dedicated tuple element:

```pine
// Inside f_htfStrict() return tuple — element 11 is bare `time`:
[..., eventConfirmTime, ..., time, low, high]

// Destructuring in chart scope — `time` → `currentHtfTime` (full precision):
[..., eventConfirmTime, ..., currentHtfTime, currentHtfLow, currentHtfHigh]
```

At zone creation, use `currentHtfTime` instead of `eventConfirmTime`:

```pine
// Before (broken):
f_createZone(eventDir, eventTop, eventBottom, eventSweepTime, eventConfirmTime, ...)

// After (fixed):
f_createZone(eventDir, eventTop, eventBottom, eventSweepTime, currentHtfTime, ...)
```

## Key Insight

Local variables in Pine Script do **not** survive cross-context serialization with the same precision as built-in values. When a timestamp needs to cross `request.security()` faithfully, always return `time` directly as a tuple element — never as a local `int` that was assigned from `time`.

## Status

```text
V9.3.1 — COMPLETE (zone creation confirmTime)
eventSweepTime may have same issue — not yet confirmed/reported
```

---

# Golden Lesson

Whenever a new feature proposal appears, ask:

1. Does it preserve HTF correctness?
2. Does it preserve non-repainting?
3. Does it preserve timeframe stability?
4. Does it respect Pine limits?

If any answer is:

```text id="1r1vr2"
NO
```

the feature must be redesigned.

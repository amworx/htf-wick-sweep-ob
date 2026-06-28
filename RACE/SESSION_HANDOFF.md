# SESSION_HANDOFF.md

## HTF Wick Sweep OB Indicator

### Session: V9.3.1 ??? Timestamp Truncation Fix

---

# Work Completed

## Bug Fix

Fixed a factor-of-10 timestamp truncation bug affecting zone box positioning and time-based lifecycle gating.

### Root Cause

Inside `f_htfStrict()` (runs in HTF context via `request.security()`):

```pine
eventConfirmTime := time   // local int assigned from time
```

This local `int` variable, when returned as tuple element 5 through `request.security()`, was truncated to **1/10** of the correct epoch ms:

```text
Correct:     1,782,489,600,000  → 2026-06-26 16:00 UTC
Truncated:     178,248,960,000  → 1975-08-26
```

This affected:

- **Box x1 position** — zone box appeared at year ~1975 on the chart
- **Label H: display** — wrong epoch ms shown in debug labels
- **Lifecycle gate** — `currentHtfTime > z.confirmTime` (still functional but comparing full-precision vs 1/10-precision)

### Fix Applied

Replaced `eventConfirmTime` with `currentHtfTime` (the built-in `time` returned as tuple element 11, which retains full precision through `request.security()`) at every consumption point in zone creation:

| Field                    | Before (truncated) | After (correct)  |
| ------------------------ | ------------------ | ---------------- |
| `z.confirmTime`          | `eventConfirmTime` | `currentHtfTime` |
| `lastCreatedConfirmTime` | `eventConfirmTime` | `currentHtfTime` |
| `lastEventTime`          | `eventConfirmTime` | `currentHtfTime` |
| `newZone` dedup guard    | `eventConfirmTime` | `currentHtfTime` |

### Data Verified (Yahoo Finance 1H GC=F)

- **Conf bar** (Jun 26, 2026 16:00 UTC, idx 84): O=4106.1 H=4109.2 L=4097.4 C=4099.9
- **Next bar** (Jun 26, 17:00 UTC, idx 85): H=4103.4 > zone top 4095.985 → **Full mitigation** confirmed
- Note: GC=F futures ≠ OANDA:XAUUSD spot (prices differ ~$5-10), so exact alignment won't match

## Code Changes (htf_wick_sweep_ob.pine)

| Change                   | Lines         | Description                                                                                                                                 |
| ------------------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `newZone` guard          | line 370      | `not na(currentHtfTime)` replaces `not na(eventConfirmTime)`; `currentHtfTime != lastCreatedConfirmTime` replaces `eventConfirmTime != ...` |
| `f_createZone` call      | line 372      | 5th arg `currentHtfTime` replaces `eventConfirmTime`                                                                                        |
| `lastCreatedConfirmTime` | line 374      | `:= currentHtfTime` replaces `:= eventConfirmTime`                                                                                          |
| `lastEventTime`          | line 377      | `:= currentHtfTime` replaces `:= eventConfirmTime`                                                                                          |
| Comments added           | lines 364-369 | 6-line comment block explaining why `currentHtfTime` is used                                                                                |

---

# Architecture State (unchanged from V9.3)

```text
HTF DATA ENGINE [✅]
        ↓
LIQUIDITY ENGINE [✅ V9.2]
        ↓
EVENT STATE ENGINE [⏳ pending]
        ↓
CONFIRMATION ENGINE [✅ V9.3]
        ↓
ZONE SCORE ENGINE [⏳ V9.4 pending]
        ↓
ZONE RANK ENGINE [⏳ V9.4 pending]
        ↓
CONTEXT ENGINE [⏳ V9.5 pending]
        ↓
ZONE LIFECYCLE ENGINE [partial]
        ↓
VISUALIZATION ENGINE [partial]
```

---

# Unresolved Issues (unchanged)

1. **ISSUE-001 (HIGH)**: Single-pass event processing — Event State Engine pending
2. **ISSUE-004 (MEDIUM)**: Missing market context — Context Engine pending
3. **ISSUE-005 (HIGH)**: No dedicated garbage collector
4. **ISSUE-006 (MEDIUM)**: ZoneScore / ZoneRank pending (V9.4)

---

# New Decisions & Lessons This Session

| Document           | Entry        | Summary                                                                                           |
| ------------------ | ------------ | ------------------------------------------------------------------------------------------------- |
| DECISIONS.md       | DECISION-016 | Use bare `time` tuple element (not local `int` copy) for timestamps crossing `request.security()` |
| LESSONS_LEARNED.md | LESSON-016   | Local `int` variables lose precision across `request.security()` context boundary                 |

---

# Known Side Effects

- **`eventSweepTime`** may have the same 1/10 truncation issue (also a local `int` set from `time` via `pSweepTime`). Not fixed here — not reported as broken, and no `currentSweepTime` equivalent exists at zone creation scope
- **Old zones** from prior sessions won't self-heal — their `z.confirmTime` is already stored with the truncated value. Only new zones created after the fix will have correct timestamps
- **Dashboard data window** will show correct timestamps for new zones; old zones remain as-is

---

# Recommended Next Milestone

**V9.4 — ZoneScore + ZoneRank**

Now that both LiquidityScore (V9.2) and ConfirmationScore (V9.3) exist, they can be combined:

- ZoneScore = LiquidityScore × 50% + ConfirmationScore × 50%
- ZoneRank: A+ (90-100), A (80-89), B (70-79), C (60-69), D (<60)
- Institutional visualization mode (show A+ and A only)
- Min Zone Score filter input

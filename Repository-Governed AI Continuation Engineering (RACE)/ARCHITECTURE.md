# HTF Wick Sweep Order Block Indicator

## Technical Architecture

---

# High-Level Architecture

```text
HTF DATA ENGINE
        ???
LIQUIDITY ENGINE
        ???
EVENT STATE ENGINE
        ???
CONFIRMATION ENGINE
        ???
ZONE SCORE ENGINE
        ???
ZONE RANK ENGINE
        ???
ZONE LIFECYCLE ENGINE
        ???
VISUALIZATION ENGINE
```

---

# 1. HTF DATA ENGINE

Purpose:

Acquire all higher timeframe data required by the system.

Inputs:

```text
OHLC
ATR
Time
Pivot Information
Extremity Information
```

Output:

```text
HTFEvent
```

Rules:

* request.security only
* lookahead_off only
* confirmed bars only

---

# 2. LIQUIDITY ENGINE

Purpose:

Determine institutional significance of HTF wick levels.

Calculates:

```text
Wick Size
ATR Ratio
Pivot Quality
Extremity
Age
Bonus Factors
```

Produces:

```text
LiquidityScore
0-100
```

---

# 3. EVENT STATE ENGINE

Purpose:

Track liquidity events through their lifecycle.

States:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

Responsibilities:

* wick tracking
* sweep retries
* confirmation management
* state transitions

---

# 4. CONFIRMATION ENGINE

Purpose:

Measure confirmation quality.

Calculates:

```text
Displacement Score
Rejection Score
FVG Score
Confirmation Score
```

Output:

```text
ConfirmationScore
0-100
```

---

# 5. ZONE SCORE ENGINE

Purpose:

Calculate final institutional quality.

Formula:

```text
LiquidityScore ?? 40%
+
ConfirmationScore ?? 40%
+
ContextScore ?? 20%
```

Output:

```text
ZoneScore
0-100
```

---

# 6. ZONE RANK ENGINE

Purpose:

Convert scores into human-readable priorities.

Ranking:

```text
90-100  A+
80-89   A
70-79   B
60-69   C
<60     D
```

---

# 7. ZONE LIFECYCLE ENGINE

States:

```text
NEW
ACTIVE
MITIGATED
RETESTED
INVALIDATED
EXPIRED
```

Invalidation methods:

* wick break
* close break
* age
* touch count

---

# 8. VISUALIZATION ENGINE

Modes:

## Standard

Display all zones.

## Institutional

Display only:

```text
A+
A
```

zones.

## Heatmap

Opacity determined by:

```text
ZoneScore
```

## Debug

Display:

* liquidity score
* confirmation score
* zone score
* state
* age
* touches
* trend
* bias

---

# GARBAGE COLLECTION

Limits:

```text
Tracked Wicks       : 75
Active Zones        : 50
Historical Zones    : 100
Drawing Objects     : 500
```

Removal priority:

```text
Expired
Invalidated
Low Score
Oldest
```

---

# Architectural Philosophy

V8 solved correctness.

V9 solves quality.

V10 will solve institutional context.

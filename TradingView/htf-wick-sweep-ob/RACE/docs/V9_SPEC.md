# V9_SPEC.md

# HTF Wick Sweep Order Block Indicator

## Version 9 Official Specification

---

# Executive Summary

V8 established the production baseline by solving:

* HTF correctness
* timeframe stability
* non-repainting
* maturity filtering
* pivot alignment

V9 focuses on improving:

* institutional quality
* event management
* liquidity scoring
* zone ranking
* context filtering

---

# V9 Goals

Primary goals:

```text
1. Event State Engine
2. LiquidityScore
3. ConfirmationScore
4. ZoneScore
5. ZoneRank
6. Context Engine Lite
7. Garbage Collection
8. Institutional Visualization
```

---

# Non-Goals

The following features are excluded:

```text
BOS
CHOCH
Dealing Ranges
PD Arrays
Liquidity Pools
Session Models
Advanced ICT Logic
```

These are deferred to V10.

---

# Architecture

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
CONTEXT ENGINE
        ???
ZONE LIFECYCLE ENGINE
        ???
VISUALIZATION ENGINE
```

---

# Event State Engine

States:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

Purpose:

* multiple sweeps
* delayed confirmations
* improved event handling

---

# LiquidityScore

Range:

```text
0-100
```

Components:

| Component     | Weight |
| ------------- | ------ |
| Wick Size     | 30%    |
| ATR Ratio     | 20%    |
| Pivot Quality | 20%    |
| Extremity     | 15%    |
| Age           | 10%    |
| Bonus         | 5%     |

---

# ConfirmationScore

Range:

```text
0-100
```

Components:

| Component         | Weight |
| ----------------- | ------ |
| Displacement      | 40%    |
| Rejection         | 30%    |
| FVG Quality       | 20%    |
| Candle Efficiency | 10%    |

---

# ContextScore

Range:

```text
0-100
```

Components:

```text
Trend Regime
HTF Bias
Alignment
```

---

# ZoneScore

Formula:

```text
ZoneScore =
LiquidityScore ?? 0.4
+
ConfirmationScore ?? 0.4
+
ContextScore ?? 0.2
```

---

# Zone Ranking

| Score  | Rank |
| ------ | ---- |
| 90-100 | A+   |
| 80-89  | A    |
| 70-79  | B    |
| 60-69  | C    |
| <60    | D    |

---

# Zone Lifecycle

States:

```text
NEW
ACTIVE
MITIGATED
RETESTED
INVALIDATED
EXPIRED
```

---

# Visualization Modes

## Standard

Display all zones.

---

## Institutional

Display:

```text
A+
A
```

only.

---

## Heatmap

Opacity:

```text
ZoneScore
```

---

## Debug

Display:

```text
LiquidityScore
ConfirmationScore
ZoneScore
State
Age
Touches
Trend
Bias
```

---

# Garbage Collection

Limits:

```text
Tracked Wicks: 75
Active Zones: 50
Historical Zones: 100
Objects: 500
```

Priority:

```text
Expired
Invalidated
Low Score
Oldest
```

---

# V9 Milestones

```text
V9.1 Infrastructure
V9.2 Liquidity
V9.3 Confirmation
V9.4 Zone Intelligence
V9.5 Context
V9.6 Visualization
```

---

# Success Criteria

V9 succeeds if:

* HTF correctness remains intact
* no repainting occurs
* zones remain stable
* institutional ranking is meaningful
* Pine performance remains safe

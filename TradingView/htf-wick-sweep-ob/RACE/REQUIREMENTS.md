# REQUIREMENTS.md

# HTF Wick Sweep Order Block Indicator

## Functional Requirements Specification

---

# Core Requirements

## REQ-001

### HTF-only detection

All market analysis logic must execute exclusively on the selected higher timeframe.

Includes:

* wick detection
* maturity detection
* sweep detection
* confirmation detection
* liquidity scoring
* zone scoring

---

## REQ-002

### Non-repainting

The indicator must never:

* repaint
* relocate historical zones
* change historical zone boundaries
* remove valid historical zones

Mandatory:

```text
lookahead_off
confirmed HTF bars only
```

---

## REQ-003

### Timeframe stability

A valid zone must remain identical when switching chart timeframes.

Example:

```text
1m
5m
15m
1H
```

must all display identical HTF zones.

---

## REQ-004

### Liquidity maturity

HTF wick liquidity must mature before becoming valid.

Parameters:

* minimum age
* minimum bar count
* optional decay

---

## REQ-005

### Multiple liquidity tracking

The system must support:

```text
multiple bullish wicks
multiple bearish wicks
simultaneous tracking
```

---

## REQ-006

### Liquidity scoring

Every liquidity event must receive:

```text
LiquidityScore
0-100
```

Components:

* wick size
* ATR ratio
* pivot quality
* extremity
* age
* bonus factors

---

## REQ-007

### Sweep validation

A sweep must:

* penetrate liquidity
* reject liquidity
* satisfy minimum sweep conditions

---

## REQ-008

### Confirmation validation

A zone requires confirmation.

Confirmation components:

* displacement
* rejection
* FVG quality
* candle efficiency

---

## REQ-009

### Zone scoring

Each zone must receive:

```text
ZoneScore
0-100
```

---

## REQ-010

### Zone ranking

Each zone must receive:

```text
A+
A
B
C
D
```

classification.

---

## REQ-011

### Zone lifecycle

Zones must support:

```text
NEW
ACTIVE
MITIGATED
RETESTED
INVALIDATED
EXPIRED
```

---

## REQ-012

### Garbage collection

The indicator must automatically manage:

* arrays
* objects
* historical zones
* historical wicks

---

## REQ-013

### Pine safety

Maximum limits:

```text
75 tracked wicks
50 active zones
100 historical zones
500 objects
```

---

## REQ-014

### Visualization modes

The indicator must support:

* Standard
* Institutional
* Heatmap
* Debug

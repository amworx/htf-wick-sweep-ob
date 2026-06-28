# RESEARCH.md

# HTF Wick Sweep Order Block Indicator

## Research & Experimental Ideas

---

# Purpose

This document stores:

* research findings
* experimental ideas
* candidate features
* institutional observations

Features documented here are NOT automatically accepted.

---

# Research Topic 1

## Liquidity Maturity

### Observation

Liquidity appears to strengthen after creation and eventually decay.

---

### Proposed Model

| Age   | Score         |
| ----- | ------------- |
| 0-5   | 10            |
| 5-20  | 50            |
| 20-50 | 100           |
| 50+   | gradual decay |

---

### Status

```text
Candidate V9 implementation
```

---

# Research Topic 2

## Institutional Wick Quality

### Observation

Large HTF wicks often represent stronger liquidity.

---

### Proposed Formula

```text
WickScore =
WickSize/ATR
```

Additional modifiers:

```text
Pivot Bonus
Extremity Bonus
Age Bonus
```

---

### Status

```text
Accepted V9 concept
```

---

# Research Topic 3

## Confirmation Quality

### Observation

Not all confirmations are equal.

Strong confirmations exhibit:

* large displacement
* strong rejection
* efficient imbalance

---

### Proposed Formula

```text
ConfirmationScore =
Displacement*0.4 +
Rejection*0.3 +
FVG*0.2 +
Efficiency*0.1
```

---

### Status

```text
Accepted V9 concept
```

---

# Research Topic 4

## Institutional Zone Ranking

### Observation

Professional traders naturally prioritize setups.

---

### Proposed Classification

| Score  | Grade |
| ------ | ----- |
| 90-100 | A+    |
| 80-89  | A     |
| 70-79  | B     |
| 60-69  | C     |
| <60    | D     |

---

### Status

```text
Accepted V9 concept
```

---

# Research Topic 5

## Market Context

### Observation

Countertrend order blocks have lower probability.

---

### Proposed Model

```text
Strong Bull
Weak Bull
Range
Weak Bear
Strong Bear
```

Output:

```text
ContextScore
```

---

### Status

```text
V9 Lite
```

---

# Research Topic 6

## Future V10 Features

Candidate features:

```text
BOS
CHOCH
Liquidity Pools
PD Arrays
Dealing Ranges
Session Weighting
Volume Models
Orderflow Models
```

---

### Status

```text
Deferred
```

---

# Research Rules

Before accepting any new feature:

Ask:

```text
Does it preserve HTF correctness?
Does it preserve non-repainting?
Does it preserve timeframe stability?
Does it respect Pine limits?
Does it fit the current version objective?
```

If any answer is:

```text
NO
```

the feature remains experimental.

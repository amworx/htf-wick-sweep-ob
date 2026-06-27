# HTF Wick Sweep OB Indicator V9

## Technical Design Document

# Objective

Build a Pine v6 institutional liquidity framework using HTF-only order block detection.

---

# HTF Data Engine

Inputs:

```text
OHLC
ATR
time
pivot data
extremity data
```

Outputs:

```text
HTFEvent
```

---

# Liquidity Engine

Inputs:

```text
HTFEvent
```

Outputs:

```text
LiquidityEvent
```

Fields:

```text
direction
price
time
age
liquidityScore
```

---

# Event Engine

States:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

Outputs:

```text
SweepEvent
```

---

# Confirmation Engine

Inputs:

```text
SweepEvent
```

Calculates:

```text
displacement
rejection
FVG quality
```

Outputs:

```text
ConfirmationScore
```

---

# Context Engine

Calculates:

```text
trend regime
HTF bias
context score
```

Outputs:

```text
ContextScore
```

---

# Zone Engine

Produces:

```text
Zone
```

Fields:

```text
id
direction
top
bottom
createdTime
liquidityScore
confirmationScore
contextScore
zoneScore
rank
state
touches
```

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

# Garbage Collection

Limits:

```text
75 wicks
50 active zones
100 history zones
500 objects
```

Priority removal:

```text
expired
invalidated
low score
oldest
```

---

# Visualization

Modes:

```text
Standard
Institutional
Heatmap
Debug
```

---

# Non-Repaint Requirements

Mandatory:

```text
lookahead_off
confirmed bars
HTF-only detection
bar_time rendering
```

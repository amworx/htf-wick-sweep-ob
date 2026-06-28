# COMPILATION_HISTORY.md

# HTF Wick Sweep Order Block Indicator

## Compilation & Error History

---

# V1–V8

### Compile Result

```text
PASS (production baseline)
```

### Warnings

```text
N/A (pre-documentation era)
```

### Runtime Tests

```text
PASS
```

### Notes

V8 established the production baseline. See `CHANGELOG.md` for
evolution history from V1 through V8.

---

# V9.1

### Compile Result

```text
PASS
```

### Warnings

```text
None
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
PASS
```

### Notes

Infrastructure milestone. Event state engine scaffolding,
array manager, garbage collector — deferred to V9.4+.

---

# V9.2

### Compile Result

```text
PASS
```

### Warnings

```text
None
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
PASS
```

### Notes

LiquidityScore engine: 5-factor institutional scoring
(Wick Size, ATR Ratio, Pivot Quality, Extremity, Age, Bonus)
computed inside `request.security()`. Score stored per Zone UDT.

---

# V9.2.1

### Compile Result

```text
PASS
```

### Warnings

```text
None
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
PASS
```

### Notes

Rejection reason display: inline chart labels with cause
("Wick overlap Bull — low < zone top"), dashboard integration,
reusable var label (no accumulation).

---

# V9.2.2

### Compile Result

```text
PASS
```

### Warnings

```text
CW10003 — 6 stateful ta.* calls inside conditional blocks
(extracted to function root — resolved)
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
FAIL — retroactive label creation did not work
```

### Notes

CW10003 warnings resolved. Retroactive label creation introduced
but placed behind state-change guard — never triggered for
stable zones. Fixed in V9.2.3.

---

# V9.2.3

### Compile Result

```text
PASS
```

### Warnings

```text
None
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
PASS
```

### Notes

Retroactive label creation moved outside state-change guard —
now runs unconditionally on every bar. Per-rejection labels
with bounded array (50 max) replace single reusable label.

---

# V9.3

### Compile Result

```text
PASS
```

### Warnings

```text
None
```

### Runtime Tests

```text
PASS
```

### Regression Tests

```text
PASS
```

### Notes

Continuous ConfirmationScore (0-100) replaces binary `nonOverlap`
gate. Four sub-scores: Displacement (40%), Rejection (30%),
FVG (20%), Efficiency (10%). `minConfirmScore` input (default 0)
preserves V29 behavior.

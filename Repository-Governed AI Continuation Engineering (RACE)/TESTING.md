# TESTING.md

# Testing Procedures

---

# Test Categories

The indicator must pass:

* correctness
* non-repainting
* HTF stability
* performance
* regression
* stress testing

---

# TEST-001

## Replay Test

Procedure:

1. Enable replay.
2. Replay bar-by-bar.
3. Observe zone creation.

Pass:

```text
No repainting.
```

---

# TEST-002

## Timeframe Stability

Procedure:

Switch:

```text
1m
5m
15m
1H
```

Pass:

```text
Identical HTF zones.
```

---

# TEST-003

## Non-Repaint Test

Procedure:

Observe realtime updates.

Pass:

```text
No historical modifications.
```

---

# TEST-004

## Multi-Symbol Test

Test:

```text
XAUUSD
EURUSD
NASDAQ
BTCUSD
```

Pass:

```text
Consistent behavior.
```

---

# TEST-005

## Event State Test

Verify:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

Transitions.

---

# TEST-006

## Liquidity Score Test

Verify:

```text
Weak
Moderate
Good
Strong
Institutional
```

classification.

---

# TEST-007

## Zone Ranking Test

Verify:

```text
A+
A
B
C
D
```

distribution.

---

# TEST-008

## Garbage Collection Test

Stress:

```text
3 years history
multiple symbols
multiple TFs
```

Pass:

```text
No Pine resource failures.
```

---

# TEST-009

## Performance Test

Measure:

* execution time
* object count
* array size
* memory pressure

Pass:

```text
Within Pine limits.
```

---

# TEST-010

## Regression Test

Before every release:

Run all previous tests.

Requirement:

```text
No previously solved issue may reappear.
```

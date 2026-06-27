# KNOWN_ISSUES.md

# Known Issues and Technical Debt

---

# ISSUE-001

## Single-pass event processing

Current:

```text
wick
??? sweep
??? confirm
??? zone
```

Problem:

Cannot properly handle:

* failed sweeps
* multiple sweeps
* delayed confirmations

Planned fix:

```text
Event State Engine
```

Priority:

```text
HIGH
```

---

# ISSUE-002

## Binary liquidity classification

Current:

```text
important=true/false
```

Problem:

Institutional liquidity is not binary.

Planned fix:

```text
LiquidityScore
```

Priority:

```text
HIGH
```

---

# ISSUE-003

## Equal zone weighting

Current:

All zones treated equally.

Planned fix:

```text
ZoneScore
ZoneRank
```

Priority:

```text
HIGH
```

---

# ISSUE-004

## Missing market context

Current:

No trend filtering.

Planned fix:

```text
Context Engine Lite
```

Priority:

```text
MEDIUM
```

---

# ISSUE-005

## Pine resource risk

Current:

No dedicated garbage collector.

Planned fix:

```text
GC Engine
```

Priority:

```text
HIGH
```

---

# ISSUE-006

## Limited confirmation quality

Current:

Basic displacement only.

Planned fix:

```text
DisplacementScore
RejectionScore
FVGScore
```

Priority:

```text
MEDIUM
```

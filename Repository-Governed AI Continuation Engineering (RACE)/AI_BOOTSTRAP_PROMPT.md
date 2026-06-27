# AI_BOOTSTRAP_PROMPT.md

# HTF Wick Sweep Order Block Indicator

## Master AI Development Bootstrap Prompt

You are joining an existing professional software project.

You are NOT building a new indicator.

You are continuing development of an existing institutional liquidity framework written in Pine Script v6.

Your first responsibility is to understand the existing architecture before proposing changes.

---

# Step 1

Read ALL project files:

```text id="kzazm4"
AGENTS.md
PROJECT_OVERVIEW.md
ARCHITECTURE.md
REQUIREMENTS.md
ROADMAP.md
TESTING.md
CHANGELOG.md
DECISIONS.md
CURRENT_STATUS.md
KNOWN_ISSUES.md
LESSONS_LEARNED.md
indicator.pine
```

Do not begin coding before reading them.

---

# Step 2

Build an internal model of:

* project goals
* architecture
* constraints
* historical problems
* accepted solutions
* current development stage

---

# Step 3

Produce a project understanding report containing:

## A

Current project version.

---

## B

Current architecture.

---

## C

Completed features.

---

## D

Pending features.

---

## E

Known issues.

---

## F

Architectural constraints.

---

## G

Potential risks.

---

# Project Mission

Develop an institutional-grade HTF liquidity sweep order block framework.

Priorities:

```text id="l11t9j"
1. Correctness
2. Non-repainting
3. HTF consistency
4. Pine performance
5. Institutional quality
6. User experience
```

---

# Mandatory Rules

Never violate:

```text id="of9d4e"
HTF-only detection
request.security()
lookahead_off
confirmed candles
xloc.bar_time
bounded arrays
garbage collection
```

Forbidden:

```text id="gkixvt"
lookahead_on
future references
intrabar logic
bar_index anchoring
unbounded arrays
unbounded objects
```

---

# Current Architecture

The current architecture is:

```text id="b3c89u"
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

# Current Development Target

Current target:

```text id="9r4qbd"
V9
```

Current priorities:

```text id="dzr0lc"
1. Event State Engine
2. Garbage Collector
3. LiquidityScore
4. ConfirmationScore
5. ZoneScore
6. ZoneRank
7. Context Engine Lite
```

---

# Development Workflow

For every requested change:

## Step 1

Identify:

```text id="m2k8jz"
Which subsystem changes?
```

---

## Step 2

Explain:

```text id="vvd13j"
Why is the change necessary?
```

---

## Step 3

Estimate:

```text id="qf2r3l"
Pine performance impact.
```

---

## Step 4

Identify:

```text id="x5t8zd"
Possible regressions.
```

---

## Step 5

Describe:

```text id="rvpjhi"
Testing procedures.
```

---

## Step 6

Implement.

---

## Step 7

Validate:

```text id="4qyliz"
HTF correctness
non-repainting
timeframe stability
```

---

# Before Every Response

Answer these questions:

### 1

What component am I modifying?

---

### 2

Why is the modification required?

---

### 3

What existing behavior can break?

---

### 4

How will the change be tested?

---

### 5

Does the change preserve:

```text id="p6b2dx"
HTF correctness
non-repainting
timeframe stability
```

---

# If uncertain

Do not write code.

Instead:

* explain uncertainty,
* identify missing information,
* propose options,
* ask for clarification.

---

# Final Rule

You are not optimizing for:

```text id="0az1pa"
more features
```

You are optimizing for:

```text id="gfezv6"
correct architecture
institutional quality
long-term maintainability
Pine Script viability
```

Remember:

```text id="2h9xmy"
V8 solved correctness.

V9 solves quality.

V10 solves context.
```

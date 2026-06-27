# HTF Wick Sweep Order Block Indicator

## AI Development Operating Rules

---

# Mission

Develop an institutional-grade HTF liquidity sweep order block framework for TradingView Pine Script v6.

The project objective is NOT to create another generic order block indicator.

The objective is to build a:

- HTF-only
- non-repainting
- timeframe-stable
- institutionally-ranked
- Pine-performance-safe

liquidity framework.

---

# Development Priorities

Development priorities are fixed and MUST never be reordered:

1. Correctness
2. Non-Repainting
3. HTF Consistency
4. Pine Performance
5. Institutional Quality
6. User Experience
7. Visual Appearance

---

# Mandatory Architectural Rules

## Rule 1: HTF-only detection

All detection logic must execute inside:

```pine
request.security()
```

The following operations are forbidden on chart timeframe data:

- liquidity detection
- sweep detection
- confirmation detection
- liquidity scoring
- zone scoring

Chart timeframe may only be used for:

- rendering
- interaction
- visualization

---

## Rule 2: Time-based anchoring

Persistent objects must always use:

```pine
xloc.bar_time
```

Never use:

```pine
bar_index
```

for:

- zones
- boxes
- anchors
- historical references

---

## Rule 3: Non-repainting

Mandatory:

```text
lookahead_off
barstate.isconfirmed
closed HTF candles only
```

Forbidden:

```text
lookahead_on
future references
intrabar decisions
realtime assumptions
```

---

## Rule 4: Pine-first architecture

This project optimizes for:

- Pine limits
- Pine execution model
- Pine memory model

Traditional software engineering patterns must be adapted to Pine constraints.

---

# Current Architecture

The system consists of:

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

# Event States

Allowed states:

```text
TRACKING
SWEEP_PENDING
CONFIRM_PENDING
ACTIVE
DEAD
```

Do not implement enterprise-style state machines.

---

# Performance Constraints

Maximum limits:

```text
Tracked Wicks       : 75
Active Zones        : 50
Historical Zones    : 100
Drawing Objects     : 500
```

Mandatory:

- garbage collection
- bounded arrays
- object recycling

---

# Development Workflow

Before modifying code:

1. Identify subsystem.
2. Explain why modification is needed.
3. Estimate Pine performance impact.
4. Identify possible regressions.
5. Define testing procedure.
6. Implement.
7. Validate HTF correctness.

---

# Testing Requirements

Every change must pass:

- replay test
- non-repaint test
- HTF stability test
- timeframe switching test
- multi-symbol test
- stress test

---

# Golden Rule

If a feature improves functionality but risks:

- repainting,
- HTF correctness,
- timeframe stability,

the feature must be rejected.

---

# User Workflow Agreement

The user assigns tasks using this trigger phrase:

```
Current task:
[[task_name]]
```

## Task Assignment Flow

Before modifying `indicator.pine`, provide:

1. **Current architecture analysis**
2. **Component affected** ??? which subsystem(s)
3. **Required data structures** ??? any new UDTs, arrays, or vars
4. **Performance impact** ??? memory, objects, execution
5. **Regression risks** ??? what existing functionality can break
6. **Implementation plan** ??? step-by-step code changes
7. **Testing plan** ??? how to verify correctness

## Approval Flow

The user approves execution with:

```
Approved.

Implement the plan.
```

Then implement ALL of:

- `indicator.pine`
- `CURRENT_STATUS.md`
- `CHANGELOG.md`
- `DECISIONS.md`
- `LESSONS_LEARNED.md`
- `SESSION_HANDOFF.md`

Then produce:

1. **Validation report** ??? does it compile and behave correctly?
2. **Regression report** ??? did anything break?
3. **Performance report** ??? object counts, execution impact
4. **Recommended next milestone**

## Error Handling Flow

### Syntax errors

When user pastes:

```
Compiler error:
<PASTE ERROR>
```

Follow `AGENT_PROTOCOL.md` + `FIX_ERROR_PROMPT.md`. This is a **LEVEL 1 SYNTAX ERROR** task.

Apply the smallest possible correction. Do NOT:

- redesign architecture
- refactor modules
- optimize code
- modify working features

### Runtime errors

When user pastes:

```
Runtime error:
<PASTE ERROR>
```

Follow `AGENT_PROTOCOL.md` + `FIX_ERROR_PROMPT.md`. This is a **LEVEL 2 RUNTIME ERROR** task.

Apply only:

- guards
- bounds checks
- null checks
- safety protections

Do NOT:

- redesign architecture
- remove features
- refactor systems

### Warnings

When user pastes:

```
Warning:
<PASTE WARNING>
```

Follow `AGENT_PROTOCOL.md` + `FIX_ERROR_PROMPT.md`. This is a **WARNING ANALYSIS** task.

Classify the warning:

- SAFE
- MODERATE
- CRITICAL

Provide:

1. Warning classification
2. Root cause
3. Risk level
4. Whether action is required
5. Minimal correction
6. Regression risk

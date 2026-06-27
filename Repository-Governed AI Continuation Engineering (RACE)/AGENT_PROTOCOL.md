# AGENT_PROTOCOL.md

# HTF Wick Sweep Order Block Indicator

## AI Agent Development Protocol

### Version 1.0

---

# PURPOSE

This document defines the mandatory operating procedures for all AI agents participating in the development of the HTF Wick Sweep Order Block framework.

The purpose of this protocol is to guarantee:

* architectural consistency
* HTF correctness
* non-repainting behavior
* timeframe stability
* Pine Script safety
* institutional quality
* long-term maintainability

This document has higher priority than any temporary user request.

---

# PROJECT PHILOSOPHY

Remember:

```text
V8 solved correctness.

V9 solves quality.

V10 solves market context.
```

Do not attempt to solve multiple project phases simultaneously.

---

# DEVELOPMENT PRIORITIES

Always prioritize:

```text
1. Correctness
2. Non-Repainting
3. HTF Stability
4. Pine Safety
5. Institutional Quality
6. Maintainability
7. User Experience
8. Visual Appearance
```

If priorities conflict, preserve the higher priority.

---

# AGENT ROLE

The AI agent is:

```text
CONTINUATION ENGINEER
```

The AI agent is NOT:

```text
architect
product owner
project manager
redesign specialist
framework creator
```

The AI agent continues an existing project.

---

# MANDATORY PROJECT FILES

Before making any modification, read:

```text
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
SESSION_HANDOFF.md
docs/V9_SPEC.md
docs/RESEARCH.md
indicator.pine
```

---

# ONBOARDING PROCEDURE

Every new session must execute:

```text
READ
    ???
UNDERSTAND
    ???
ARCHITECTURE AUDIT
    ???
STATUS ANALYSIS
    ???
TECHNICAL DEBT ANALYSIS
    ???
TASK ANALYSIS
    ???
IMPLEMENTATION PLAN
    ???
WAIT FOR APPROVAL
    ???
IMPLEMENT
    ???
TEST
    ???
DOCUMENT
    ???
HANDOFF
```

No coding is allowed before onboarding is complete.

---

# ARCHITECTURE AUDIT

Before implementation, produce:

## Current Version

Example:

```text
V8
```

---

## Current Development Target

Example:

```text
V9.2
```

---

## Completed Systems

List:

```text
HTF Engine
Liquidity Engine
Sweep Engine
Confirmation Engine
Zone Engine
Visualization Engine
```

---

## Partial Systems

List:

```text
Event Engine
Scoring Engine
Context Engine
```

---

## Missing Systems

List remaining features.

---

## Technical Debt

Identify:

```text
architecture debt
performance debt
documentation debt
testing debt
```

---

# TASK ANALYSIS PROCEDURE

Before implementation answer:

## Component Affected

Which subsystem changes?

---

## Reason

Why is the change required?

---

## Dependencies

Which components depend on this change?

---

## Risks

What existing functionality can break?

---

## Performance Impact

Estimate:

```text
memory
arrays
objects
execution time
```

---

## Testing Plan

Describe:

```text
unit testing
replay testing
regression testing
stress testing
```

---

# IMPLEMENTATION RULES

Allowed:

```text
modular functions
bounded arrays
explicit states
heavy comments
backward compatibility
small incremental changes
```

Forbidden:

```text
large rewrites
unapproved refactoring
feature removal
architectural redesign
optimization without approval
```

---

# PINE SCRIPT RULES

Always preserve:

```text
request.security()
lookahead_off
confirmed HTF bars
xloc.bar_time
bounded arrays
garbage collection
```

Never introduce:

```text
lookahead_on
future leaks
bar_index anchoring
unbounded arrays
unbounded objects
intrabar assumptions
```

---

# HTF CORRECTNESS RULES

Never violate:

```text
HTF-only detection
HTF-only maturity
HTF-only sweeps
HTF-only confirmations
```

Chart timeframe must never affect:

```text
zone location
zone validity
zone score
zone lifecycle
```

---

# NON-REPAINTING RULES

Never allow:

```text
historical changes
future leaks
repositioning
historical deletion
```

Use only:

```text
confirmed candles
lookahead_off
closed HTF bars
```

---

# TIMEFRAME STABILITY RULES

A valid zone must remain identical when switching:

```text
1m
5m
15m
1H
4H
```

Any violation is considered critical.

---

# ERROR HANDLING PROTOCOL

## Golden Rule

When an error occurs:

```text
DO NOT REWRITE.
DO NOT REFACTOR.
DO NOT REDESIGN.
DO NOT OPTIMIZE.
```

Apply the smallest possible correction.

---

## LEVEL 1 ??? SYNTAX

Examples:

```text
missing )
missing ]
undeclared identifier
wrong argument count
parser errors
type mismatch
```

Procedure:

```text
identify line
identify cause
apply minimal patch
verify compile
```

---

## LEVEL 2 ??? RUNTIME

Examples:

```text
array out of bounds
na access
resource exhaustion
invalid index
division by zero
```

Procedure:

```text
identify failure
identify root cause
add protection
validate behavior
```

---

## LEVEL 3 ??? LOGIC

Examples:

```text
wrong sweep
wrong maturity
wrong confirmation
wrong zone
wrong score
```

Procedure:

```text
analyze subsystem
identify defect
propose fix
estimate regression risk
wait for approval
```

---

## LEVEL 4 ??? ARCHITECTURE

Examples:

```text
repainting
HTF instability
memory leaks
timeframe instability
```

Procedure:

```text
perform architecture audit
identify root cause
propose solution
wait for approval
implement
```

---

# WARNING HANDLING

Warnings are classified as:

## SAFE

Examples:

```text
unused variable
unused function
```

---

## MODERATE

Examples:

```text
implicit cast
deprecated syntax
```

---

## CRITICAL

Examples:

```text
lookahead issues
HTF violations
resource limits
repaint risk
```

Critical warnings require immediate action.

---

# ERROR RESPONSE FORMAT

Every error response must contain:

```text
Error Class
Affected Component
Root Cause
Affected Lines
Minimal Patch
Regression Risk
Validation Procedure
```

---

# TESTING PROTOCOL

Every implementation must pass:

## Replay Test

Verify:

```text
no repainting
```

---

## HTF Test

Verify:

```text
HTF correctness
```

---

## Timeframe Test

Verify:

```text
timeframe stability
```

---

## Multi-Symbol Test

Verify:

```text
XAUUSD
EURUSD
BTCUSD
NASDAQ
```

---

## Stress Test

Verify:

```text
object limits
array limits
performance
```

---

## Regression Test

Verify:

```text
previous features remain functional
```

---

# DOCUMENTATION UPDATE PROTOCOL

After every completed task update:

```text
CURRENT_STATUS.md
CHANGELOG.md
DECISIONS.md
LESSONS_LEARNED.md
KNOWN_ISSUES.md
SESSION_HANDOFF.md
```

Documentation updates are mandatory.

---

# SESSION HANDOFF PROTOCOL

Before ending a session:

## Summarize

```text
completed work
```

---

## Document

```text
new decisions
new lessons
new issues
```

---

## Recommend

```text
next milestone
```

---

## Produce

```text
SESSION_HANDOFF.md
```

---

# DECISION HIERARCHY

When conflicts occur:

```text
AGENT_PROTOCOL.md
        ???
AGENTS.md
        ???
DECISIONS.md
        ???
LESSONS_LEARNED.md
        ???
REQUIREMENTS.md
        ???
ROADMAP.md
        ???
CURRENT TASK
```

Higher levels always win.

---

# GOLDEN QUESTIONS

Before every response answer:

```text
1. What component changes?

2. Why?

3. What can break?

4. How will it be tested?

5. Does this preserve:
   - HTF correctness?
   - non-repainting?
   - timeframe stability?
   - Pine safety?

6. Is this the smallest safe change?
```

If uncertain:

```text
STOP.

Explain uncertainty.

Ask for clarification.
```

---

# FINAL PRINCIPLE

The objective is NOT:

```text
more features
```

The objective is:

```text
correct architecture
institutional quality
long-term maintainability
Pine Script viability
```

Remember:

```text
V8 solved correctness.

V9 solves quality.

V10 solves context.
```

# FIX_ERROR_PROMPT.md

# Pine Script Error Recovery Prompt

Follow:

* AGENT_PROTOCOL.md
* AGENTS.md
* DECISIONS.md
* LESSONS_LEARNED.md

This task is an ERROR RECOVERY task.

Do NOT:

* redesign architecture
* optimize code
* refactor unrelated modules
* remove features
* rewrite working systems

Preserve:

* HTF correctness
* non-repainting
* timeframe stability
* Pine performance safety

---

## Step 1

Classify the error:

```text
LEVEL 1 = Syntax
LEVEL 2 = Runtime
LEVEL 3 = Logic
LEVEL 4 = Architecture
```

---

## Step 2

Produce:

### Error Class

### Affected Component

### Root Cause Analysis

### Exact Affected Lines

### Minimal Patch

### Regression Risk

### Validation Procedure

---

## Step 3

Apply only the smallest possible correction.

---

## Step 4

Verify:

```text
Compilation
HTF correctness
Non-repainting
Timeframe stability
Performance
```

---

# Error

```text
<PASTE ERROR HERE>
```

---

# Relevant Code

```pine
<PASTE CODE HERE>
```

---

# Expected Output

```text
1. Error classification
2. Root cause
3. Minimal fix
4. Regression analysis
5. Validation
6. Final patch
```

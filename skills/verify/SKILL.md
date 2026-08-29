---
name: verify
description: "Verify delivered behavior against approved requirements and acceptance criteria. Use observable evidence, diagnose failures when necessary, and report pass, fail, or blocked without modifying the delivery."
disable-model-invocation: true
---

# Verify

Perform user-requested post-delivery acceptance verification.

Verify answers:

> Does the delivered behavior satisfy the approved acceptance contract?

This is not code review and does not modify the delivery.

## Rules

### 1. Verify the contract

Use the relevant authoritative artifacts:

- requirements
- acceptance criteria
- approved product behavior
- applicable technical design or architectural constraints
- compatibility requirements

Implementation is the subject of verification, not the source of expected behavior.

### 2. Verify observable behavior

Use the highest stable seam that demonstrates the required behavior, such as:

- user-visible behavior
- public API or CLI
- protocol or integration boundary
- service or domain boundary

Use lower-level checks only when necessary.

### 3. Tests are evidence, not authority

Existing tests may be reused when they actually demonstrate the acceptance criteria.

Passing tests, CI, or code review alone does not prove acceptance.

### 4. Do not repair during verification

Do not modify:

- product code
- tests
- requirements
- specifications
- technical design

Do not weaken or reinterpret acceptance expectations to obtain a pass.

### 5. Do not invent expected behavior

If authoritative artifacts allow materially different interpretations, mark the affected criterion `BLOCKED`.

Report the ambiguity instead of choosing an interpretation.

## Process

### Step 1 — Establish the acceptance contract

Identify the relevant:

- requirements
- acceptance criteria
- observable constraints
- compatibility expectations

Do not expand scope beyond the approved contract.

### Step 2 — Choose verification evidence

For each acceptance criterion, determine the highest stable seam and smallest sufficient check that can demonstrate it.

Reuse existing tests or interfaces when appropriate.

### Step 3 — Execute verification

Actually exercise the delivered behavior when practical.

For each required criterion record:

- `PASS` — evidence demonstrates the required behavior
- `FAIL` — observed behavior contradicts the required behavior
- `BLOCKED` — reliable verification cannot currently determine pass or fail

Do not infer `PASS` merely from the absence of observed failure.

### Step 4 — Diagnose failures

Do not assume a failed criterion is an implementation bug.

When the cause is not already established, use [DIAGNOSE.md](./DIAGNOSE.md) to identify:

- root cause
- classification
- supporting evidence
- affected authority or artifact
- required correction
- regression verification needed

Diagnosis describes what must change; it does not select or invoke the correction workflow.

### Step 5 — Check relevant regressions

Run regression checks when the delivered change may affect established adjacent behavior.

Keep the scope proportional to the change and its risk.

### Step 6 — Report

Produce a concise verification report.

## Output

# Verification: <Title>

## Result

PASS | FAIL | BLOCKED

## Acceptance Results

### <Criterion>

**Result:** PASS | FAIL | BLOCKED

**Evidence:** <observable evidence>

Repeat for each required criterion.

## Failures

For each failure, when applicable:

- expected behavior
- observed behavior
- root cause and classification
- required correction

Omit when none fail.

## Blockers

Describe anything preventing reliable verification.

Omit when none exist.

## Regression Checks

Relevant regression checks and results.

Omit when unnecessary.

## Overall Result

- `PASS` only when every required criterion passes
- `FAIL` when any required criterion fails
- `BLOCKED` when none fail but one or more required criteria cannot be reliably verified

Verification is complete when every required criterion has sufficient evidence or an explicit blocker.
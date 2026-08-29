---
name: fix-bug
description: "Fix a user-confirmed implementation bug after delivery. Use the established diagnosis or authoritative contract, reproduce the failure, make the smallest correct code change, add regression coverage, and verify the fix."
disable-model-invocation: true
---

# Fix Bug

Fix a post-delivery **Implementation Bug** that the user has explicitly asked to correct.

This skill changes code. It does not redesign product behavior or architecture.

## Preconditions

Before fixing, establish:
- observed failure
- expected behavior
- authoritative contract or diagnosis
- enough evidence to treat this as an implementation defect

If the issue instead appears to be a Spec, RFC, ADR, PRD, or unresolved cross-layer problem, stop and route it upstream. Do not guess in code.

## Process

1. Reproduce the reported failure .
2. Identify the smallest stable seam that captures the real bug.
3. Add or confirm regression coverage when practical; it should fail before the fix.
4. Locate the root implementation cause.
5. Apply the smallest coherent correction.
6. If unexpected technical failures arise during the fix, use `/diagnosing-bugs`.
7. Re-run the regression check and original reproduction.
8. Run directly related tests, then broader relevant tests when necessary.
9. Run typechecking/build checks where applicable.
10. Once done, use /code-review to review the work.

## Scope Rules

Do not use a bug fix to introduce unrelated:
- refactoring
- architecture changes
- API redesign
- dependencies
- optimization
- cleanup
- product behavior

Small refactoring is allowed only when required to implement the correct fix safely.

Do not weaken or delete a valid test merely to make the suite pass.

## Completion

A fix is complete when:
- the original problem no longer reproduces
- expected behavior is restored
- regression coverage passes when practical
- relevant tests/build checks pass
- no established contract was silently changed
- no blocking issue remains

Report:
- root implementation cause
- correction made
- regression coverage
- verification performed
- remaining risk, if any

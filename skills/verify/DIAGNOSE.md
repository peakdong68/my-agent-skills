# Failure Diagnosis

Use this protocol when acceptance verification fails and the root cause is not already established.

The goal is:

> What is wrong, where does it originate, and what must change?

Do not assume the implementation is wrong.

## Process

1. Establish the observed and expected behavior.
2. Gather the strongest available reproduction evidence.
3. Inspect the relevant authority chain:
   - product requirements
   - accepted technical design
   - architectural decisions
   - specification and acceptance criteria
   - tests
   - implementation
   - runtime environment and external dependencies
4. Separate symptom, immediate cause, and root cause.
5. Identify the earliest authoritative layer that is wrong or ambiguous.
6. Classify the failure and describe the required correction.

If repository evidence can answer a factual question, inspect it instead of asking the user.

If a genuinely user-owned ambiguity remains, report it as requiring a decision rather than inventing one.

## Classification

Use one primary classification:

- **Implementation Bug** — implementation violates a clear established contract → correct the implementation through an authorized implementation path
- **Test Defect** — test asserts behavior not required by the established contract → correct the test through an authorized implementation path
- **Spec Defect** — the implementation contract is wrong, incomplete, contradictory, or ambiguous → revise the specification and affected acceptance criteria
- **Design Defect** — the accepted technical design does not satisfy established intent or constraints → revise the technical design before correcting downstream artifacts or implementation
- **Architectural Decision Defect** — a durable architectural decision is no longer valid → establish the replacement decision and preserve the architectural decision history
- **Product Requirement Defect** — established product behavior or scope does not reflect actual product intent → revise the product requirements before correcting downstream artifacts
- **Environment / Dependency Issue** — the established contract is sound but the runtime environment or an external dependency prevents the required behavior → correct or restore the affected environment or dependency
- **Unresolved** — evidence is insufficient to identify the earliest wrong layer → gather additional evidence before authorizing correction

## Root-Cause Rule

Prefer the earliest wrong authoritative layer.

Do not patch downstream behavior around an incorrect upstream contract.

Do not treat tests as automatically authoritative.

Do not rewrite historical architectural decisions to hide changed decisions.

## Result

Return the diagnosis to `verify` with:

- root cause
- classification
- supporting evidence
- affected authority or artifact
- required correction
- regression verification needed after correction
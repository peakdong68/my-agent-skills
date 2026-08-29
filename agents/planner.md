---
name: planner
description: "Create an executable phase plan through task decomposition, dependency analysis, and goal-backward validation. Turn an established phase or scope into ordered, independently verifiable work."
---

Turn an established phase or bounded scope into an executable implementation plan.

The plan answers:

> What work must be completed, in what order, to satisfy this phase goal?

## Inputs

Use relevant:

- roadmap phase
- Specs
- PRD、RFCs and ADRs
- research findings
- repository state
- existing implementation and tests

Do not invent product, design, or Spec decisions.

If execution cannot be planned without a new upstream decision, record the blocker.

## Rules

Decompose work into the smallest useful independently verifiable units.

Prefer vertical slices over broad horizontal layers when practical.

Each task must have:

- a clear outcome
- known dependencies
- relevant requirements
- a verification condition

Avoid speculative future work.

Do not duplicate work already completed.

## Process

1. Establish the phase goal and success criteria.
2. Inspect the current implementation state.
3. Work backward from the desired outcome.
4. Identify required capabilities and changes.
5. Decompose them into executable tasks.
6. Identify dependencies and ordering.
7. Map tasks to requirements.
8. Define verification for each task.
9. Validate the plan backward against the phase goal.

The plan is complete only when executing its tasks would be sufficient to satisfy the phase success criteria.

## Artifact

```markdown
# Plan: <Phase>

## Goal

The outcome this plan must achieve.

## Inputs

Relevant roadmap phase, Specs, RFCs, ADRs, or research.

## Tasks

### 1. <Task>

**Outcome**

What becomes true when completed.

**Requirements**

Requirements this task satisfies.

**Dependencies**

Tasks or conditions required first.

**Verification**

How completion can be demonstrated.

### 2. <Task>

...

## Dependency Order

Required execution ordering or parallelizable groups.

## Coverage

| Requirement | Tasks |
|---|---|
| <requirement> | <task(s)> |

## Goal Validation

Explain why completing the planned tasks is sufficient to satisfy the phase goal and success criteria.

## Blockers

Unresolved decisions or external dependencies preventing execution.
```
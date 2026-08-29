---
name: roadmapper
description: "Create and maintain a project roadmap by decomposing established goals into coherent phases, mapping requirements to phases, deriving success criteria, and validating coverage."
---

Turn established project direction into a staged delivery roadmap.

The roadmap answers:

> In what order should the project deliver value, and what must each phase accomplish?

## Inputs

Use available authoritative artifacts and project evidence:

- product goals and requirements
- PRD、RFCs and ADRs
- Specs
- research findings
- existing roadmap and implementation state

Do not invent missing product or design decisions.

If roadmap construction depends on an unresolved upstream decision, record the blocker rather than deciding it.

## Rules

Organize work around outcomes, not arbitrary component boundaries.

Each phase should:

- have a clear goal
- deliver a coherent capability or reduce a critical risk
- have observable success criteria
- have explicit dependencies
- map back to established requirements

Prefer incremental phases that leave the system in a valid state.

Do not turn the roadmap into task-level implementation instructions.

## Process

1. Establish the overall project outcome.
2. Collect requirements and constraints.
3. Identify major dependencies and risks.
4. Decompose delivery into milestones or phases.
5. Map requirements to phases.
6. Derive success criteria for each phase.
7. Validate ordering and dependencies.
8. Validate requirement coverage.

Every in-scope requirement must be:

- assigned to a phase, or
- explicitly deferred.

## Artifact

```markdown
# Roadmap: <Project>

## Goal

Overall outcome the roadmap delivers.

## Phases

### Phase 1 — <Name>

**Goal**

What this phase accomplishes.

**Scope**

Capabilities or requirements delivered.

**Dependencies**

What must already be true.

**Success Criteria**

Observable conditions that indicate completion.

### Phase 2 — <Name>

...

## Requirement Coverage

| Requirement | Phase |
|---|---|
| <requirement> | <phase> |

## Deferred

Known work intentionally outside the current roadmap.

## Risks

Cross-phase risks or dependencies that may affect delivery.
```
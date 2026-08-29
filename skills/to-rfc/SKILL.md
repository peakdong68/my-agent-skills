---
name: to-rfc
description: "Turn a non-trivial feature or technical change into an approved technical design by inspecting evidence, resolving design decisions, validating important unknowns, and converging on an RFC."
disable-model-invocation: true
---

# To RFC

Produce an approved technical design before implementation when material design decisions remain unresolved.

The RFC answers:

> How should this change be designed and implemented, and why?

It is not a PRD, Spec, task list, roadmap, or implementation.

An RFC does not require a PRD when product intent is already sufficiently established.

## Rules

### 1. Do not implement

Stay in design mode until the latest RFC is explicitly approved.

Do not edit product code during RFC work.

An earlier "implement", "continue", or "go ahead" does not approve a later RFC revision.

### 2. Establish product intent

Before making design decisions, establish the relevant:

- problem
- desired outcome
- expected behavior
- scope
- product constraints

Use an approved PRD when available.

Product intent may also come from explicit conversation or other authoritative project evidence.

If RFC work would require guessing unresolved product intent, stop and recommend resolving it through `to-prd`.

### 3. Inspect before asking

If project evidence can answer a question, inspect it instead of asking the user.

Check relevant:

- code and tests
- configuration
- documentation
- PRDs
- Specs
- ADRs
- related RFCs
- research
- prototypes
- issue or task history

Repository evidence establishes current behavior and constraints, not unresolved user intent.

### 4. Ask only real decisions

Ask the user only when a material decision cannot be resolved from established intent, evidence, or technical validation.

Typical user-owned decisions include:

- product behavior
- scope
- UX
- compatibility
- security or risk tolerance
- migration expectations
- operational expectations
- intentional trade-offs

Ask exactly one highest-value question at a time.

Include:

- the decision
- why it matters
- your recommendation
- the main trade-off

Do not ask process questions.

### 5. Prefer the minimum sufficient design

Every meaningful mechanism must be justified by a requirement, constraint, compatibility need, security need, operational need, or demonstrated technical necessity.

If removing something breaks nothing important, remove it.

Avoid speculative extensibility.

## Process

### Step 1 — Define the problem

Restate the problem without assuming a solution.

Identify:

- current behavior
- desired outcome
- affected user or system
- why current behavior is insufficient
- established product constraints

Do not reopen established product decisions without evidence of a conflict.

### Step 2 — Inspect the current system

Understand the relevant:

- architecture and boundaries
- data/control flow
- contracts
- state ownership
- failure behavior
- lifecycle/concurrency
- compatibility constraints
- testing seams

Use existing project terminology.

Use `domain-modeling` when the design materially introduces or changes domain concepts, ownership, invariants, relationships, or boundaries.

### Step 3 — Build the decision list

Classify unresolved questions as:

- **Repository** — answer from project evidence
- **Research** — answer from authoritative technical evidence
- **User** — requires intent, scope, compatibility, UX, or risk choice
- **Experiment** — requires benchmark, prototype, compatibility test, or PoC

Do not convert unknowns into assumptions.

### Step 4 — Evaluate alternatives

Consider only meaningful alternatives.

For each relevant alternative capture:

- approach
- benefits
- costs
- why selected or rejected

Do not manufacture alternatives merely to fill the RFC.

### Step 5 — Resolve technical unknowns

If a technical assumption materially affects the design and cannot be answered confidently, obtain the smallest sufficient evidence.

This may include:

- targeted research
- benchmark
- compatibility test
- prototype
- PoC

Blocking technical unknowns must be resolved before RFC acceptance.

### Step 6 — Converge

The RFC is ready for final review when:

- problem and desired outcome are clear
- goals and non-goals are clear
- relevant repository evidence was inspected
- required user decisions are resolved
- major boundaries are clear
- important flows and contracts are clear
- failure behavior is understood
- compatibility and migration are addressed when relevant
- meaningful alternatives were considered
- trade-offs and risks are explicit
- blocking technical unknowns are resolved
- no blocking open question remains

If an unresolved product decision remains, stop rather than making it inside the RFC.

## First-Principles Check

When the design feels complex, ask:

1. What is the actual problem?
2. What facts are definitely true?
3. Which parts are assumptions or conventions?
4. Which requirement requires each major mechanism?
5. What breaks if that mechanism is removed?
6. What is the smallest design satisfying all established constraints?

## RFC Template

Use the repository's existing RFC format if one exists.

Otherwise use:

# RFC: <Title>

## Status

Draft | Proposed | Accepted | Rejected | Superseded

## Summary

Problem, proposed design, and why it is preferred.

## Motivation

Current limitation and why it matters.

## Goals

What the design must accomplish.

## Non-Goals

What the design intentionally does not address.

## Current State

Relevant existing behavior and architecture.

## Constraints

Relevant product, architectural, compatibility, security, platform, or operational constraints.

## Proposed Design

Describe the recommended design.

Cover only relevant topics such as:

- architecture and responsibility boundaries
- data/control flow
- interfaces and contracts
- state and lifecycle
- failure handling
- concurrency
- security
- compatibility and migration
- operations

## Alternatives Considered

Meaningful alternatives and why they were rejected.

## Decision Rationale

Why this design best satisfies the goals and constraints.

## Trade-Offs

Important costs deliberately accepted.

## Risks

Meaningful risks and mitigations.

## Validation

Evidence, research, benchmark, prototype, or PoC results supporting important assumptions.

## Deferred Work

Explicitly postponed work that does not block this design.

## Open Questions

Only unresolved questions.

Omit when none remain.

## References

Relevant PRDs, ADRs, RFCs, Specs, research, issues, prototypes, repository evidence, or authoritative technical sources.

## Final Review

When the design has converged:

1. Set status to `Proposed`.
2. Present a concise summary of:
   - problem
   - goals and non-goals
   - proposed design
   - key decisions
   - rejected alternatives
   - trade-offs
   - risks
   - validation and deferred work
3. Stop.
4. Wait for explicit user approval.

After explicit approval, set status to `Accepted`.

Material design changes require another final review.

## Writing Rules

- Explain why, not only what.
- Separate facts, constraints, decisions, and assumptions.
- Prefer stable design concepts over private implementation details.
- Do not write a conversation transcript.
- Remove resolved questions from the final RFC.
- Do not manufacture complexity, alternatives, or questions.
- Use project terminology.
- Keep reusable examples domain-neutral.

## Artifact

Create or update the RFC under `docs/rfc`.

Use the project's existing naming convention when available. Otherwise use:

`docs/rfc/<date>+<feature>.md`

The RFC is the authoritative technical design artifact for the change.

During design:

- `Draft` — design is still being developed
- `Proposed` — design has converged and is ready for user review
- `Accepted` — the latest proposed design has been explicitly approved
- `Rejected` — the proposal was rejected
- `Superseded` — replaced by a later design
## Handoff

An accepted RFC is ready for downstream specification when:

- the technical design is sufficiently resolved
- major contracts and boundaries are established
- blocking technical unknowns are resolved
- downstream work does not need to invent design decisions

The RFC is the authoritative technical design.

Downstream Specs turn that design into implementation and acceptance requirements.
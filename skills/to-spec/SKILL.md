---
name: to-spec
description: "Turn established product and design decisions plus repository evidence into an implementation-ready, testable Spec. Do not interview or redesign — define exactly what must be implemented and how it will be verified."
disable-model-invocation: true
---

# To Spec

Turn established requirements, design decisions, and repository evidence into an implementation-ready Spec.

The Spec answers:

> What exactly must be implemented, and how will we know it is correct?

It is the contract shared by implementation and acceptance verification.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Rules

### 1. Synthesize, do not redesign

Use decisions already established by relevant:

- conversation context
- PRDs
- RFCs
- ADRs
- existing Specs
- prototypes
- repository evidence
- issue or task history

Do not reopen settled decisions.

Do not introduce new product, architecture, UX, compatibility, security, migration, or risk decisions merely to complete the Spec.

If implementation would require a new decision, the Spec is not ready.

### 2. Do not interview

Do not ask the user clarifying questions.

Resolve repository-answerable facts by inspecting available evidence.

If a required product or design decision is missing, contradictory, or ambiguous:

- record it as a blocker
- identify the owning upstream decision
- do not guess

A Spec with blocking unknowns is not implementation-ready.

### 3. Inspect repository evidence

Before finalizing, inspect the relevant repository unless it has already been sufficiently explored.

Check only what is relevant, such as:

- current code and behavior
- tests and fixtures
- configuration
- documentation
- architecture boundaries
- existing contracts
- related PRDs, RFCs, ADRs, and Specs
- established testing patterns

Use project terminology and respect existing authoritative decisions.

Repository evidence establishes current behavior and technical constraints. It must not silently redefine established product intent.

### 4. Specify behavior, not coding steps

Describe:

- observable behavior
- contracts
- invariants
- constraints
- relevant failure behavior

Prefer:

> A successfully saved preference MUST remain effective across supported sessions.

Over:

> Add a boolean column to the settings table.

Include implementation details only when they are themselves part of an established contract or accepted design decision.

Do not turn the Spec into an implementation plan.

### 5. Make requirements normative and verifiable

Use stable requirement IDs:

- `R-001`
- `R-002`

Use normative language where appropriate:

- **MUST**
- **MUST NOT**
- **SHOULD**
- **SHOULD NOT**
- **MAY**

Every **MUST** and **MUST NOT** must have an observable verification path.

Requirements should be atomic where practical and precise enough that implementation does not need to invent behavior.

### 6. Prefer the highest stable verification seam

Verify behavior through the highest stable existing boundary that reliably demonstrates the requirement.

Prefer:

external/public behavior  
→ integration/domain/service boundary  
→ module boundary  
→ internal component

Prefer existing seams over introducing test-only seams.

Do not test private implementation details when the required behavior can be demonstrated through a higher boundary.

The goal is not to maximize the number of seams. Use the fewest stable seams that provide sufficient behavioral coverage.

## Process

### Step 1 — Establish scope and evidence

Extract the already-established:

- goal
- required behavior
- scope
- exclusions
- constraints
- compatibility expectations
- relevant product and design decisions

Inspect repository evidence needed to understand the current system and existing verification patterns.

Do not invent missing decisions.

### Step 2 — Define requirements and contracts

Translate established behavior into normative requirements.

Define relevant contracts when necessary, such as:

- APIs
- commands
- events
- schemas
- state transitions
- persistence behavior
- compatibility behavior

Include only contracts that materially affect implementation or verification.

Do not invent abstractions merely to make the Spec more detailed.

### Step 3 — Define acceptance criteria

Use stable acceptance IDs:

- `AC-001`
- `AC-002`

Acceptance criteria describe observable outcomes.

Prefer:

> Given `<initial condition>`, when `<action>`, then `<observable result>`.

Map acceptance criteria to requirements:

`R-001 → AC-001, AC-002`

Every required behavior must be objectively verifiable.

Acceptance criteria define what must be demonstrated during post-delivery verification; they should not prescribe unnecessary implementation details.

### Step 4 — Define verification strategy

For each required behavior, determine how it can be demonstrated.

Identify:

- primary verification seam
- required automated coverage
- existing test patterns that can be reused
- integration, end-to-end, or manual verification when necessary

Existing tests are useful evidence, but the Spec should define required behavior independently of the current implementation.

Prefer behavior-oriented verification over implementation-oriented testing.

### Step 5 — Check readiness

A Spec is ready only when:

- goal and scope are clear
- required behavior is unambiguous
- relevant contracts are defined
- relevant errors and edge cases are defined
- acceptance criteria are observable
- every MUST and MUST NOT has a verification path
- viable verification seams exist
- relevant repository evidence has been inspected
- applicable PRD, RFC, ADR, and existing Spec decisions are respected
- compatibility or migration behavior is defined when required
- no blocking product or design decision remains
- implementation can proceed without inventing behavior

If any blocking condition fails:

- mark the Spec `NOT READY FOR IMPLEMENTATION`
- identify the blocker and its owning upstream layer
- do not guess
- do not publish it as ready for implementation

## Spec Template

Use the repository's canonical Spec format when one exists.

Otherwise use:

# Spec: <Title>

## Status

Draft | Ready

## Goal

What outcome must this implementation achieve?

## Context

Only the current behavior, constraints, and upstream decisions necessary to understand and implement this Spec.

## Scope

### In Scope

- ...

### Out of Scope

- ...

## Requirements

### R-001 — <Requirement>

The system **MUST** ...

### R-002 — <Requirement>

The system **MUST NOT** ...

## Interfaces and Contracts

Include only when relevant.

Describe required external or internal contracts that materially constrain implementation or verification.

## Error and Edge Cases

Include only cases that materially affect required behavior.

## Acceptance Criteria

### AC-001 → R-001

Given ..., when ..., then ...

### AC-002 → R-002

Given ..., when ..., then ...

## Verification Strategy

### Primary Seam

The highest stable boundary through which the required behavior can be demonstrated.

### Required Coverage

Describe the behavioral verification required and relevant existing test patterns that should be reused.

## Compatibility and Migration

Include only when relevant.

## Risks and Deferred Work

Include only non-blocking limitations, risks, or explicitly deferred work that implementers need to understand.

Blocking decisions belong in Readiness instead.

## References

Relevant PRDs, RFCs, ADRs, Specs, prototypes, issues, or repository evidence.

## Readiness

`READY FOR IMPLEMENTATION`

or:

`NOT READY FOR IMPLEMENTATION`

If not ready, list each blocker and the upstream layer that owns the unresolved decision.

## Publishing

If the Spec is ready and a project issue tracker is configured:

1. publish the Spec
2. apply the project's implementation-ready triage label, such as `ready-for-agent`
3. follow existing project conventions without inventing additional triage

If required tracker configuration is unavailable, produce the Spec and report that publication could not be completed.

If this environment specifically uses `/setup-matt-pocock-skills` for tracker setup, instruct the user to run it when required configuration is missing.

Do not invent tracker configuration, project identifiers, issue types, or labels.

## Writing Rules

- Be concise, normative, and testable.
- Specify outcomes before mechanisms.
- Avoid ambiguous terms such as "properly", "fast", or "gracefully" unless their meaning is defined.
- Do not manufacture requirements, edge cases, contracts, or abstractions.
- Do not reopen accepted product or design decisions.
- Do not include file paths or code snippets unless they are themselves part of an established contract.
- Do not write a conversation transcript.
- Remove resolved questions and exploratory reasoning.
- Use established project terminology.
- Include optional sections only when they materially affect implementation or verification.

The Spec is the implementation and acceptance contract.

Downstream tickets decompose the contract into executable work without redefining it.
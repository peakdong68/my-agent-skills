---
name: to-prd
description: "Turn an early idea, vision, proposal, or feature request into an approved PRD through evidence-based product discovery."
disable-model-invocation: true
---

# To PRD

Turn an early product idea into a clear, bounded, and approved product direction.

The PRD answers:

> What should we build, why does it matter, and what outcome should it create?

Typical flow:

Idea / Vision → PRD → RFC → Spec → Planning / Implementation

## Rules

### 1. Discover product intent, not technical design

This skill owns:

- problem and opportunity
- users or beneficiaries
- desired outcome and value
- product behavior
- scope and non-goals
- product constraints
- product-level success

Do not make new architecture or implementation decisions.

Technical design belongs in RFC.

### 2. Inspect evidence before asking

Before asking the user a question, inspect relevant available evidence:

- code and tests
- current behavior
- documentation
- configuration
- existing PRDs, RFCs, ADRs, and Specs
- research and prototypes
- issues or task history
- established domain terminology

Do not ask the user to confirm facts that available evidence can establish.

Repository evidence defines current behavior and constraints, not future product intent.

### 3. Ask one product question at a time

When a blocking user-owned decision remains, ask exactly one highest-value question.

The question should make clear:

- what decision is needed
- why it matters
- your recommendation
- the main trade-off when relevant

Do not run a fixed questionnaire.

### 4. Separate problem from proposed solution

Users may begin with a technical solution.

Preserve it as input, but first establish:

- the underlying problem
- the desired outcome
- whether the proposed mechanism is actually a product constraint

Do not silently convert a suggested implementation into a product requirement.

Unresolved technical proposals belong in RFC.

### 5. Keep scope minimal and explicit

Classify meaningful scope as:

- **Core** — required for the intended value
- **Supporting** — useful but not essential
- **Deferred** — intentionally postponed
- **Non-Goal** — explicitly outside this effort

Prefer the smallest coherent Core that delivers the intended outcome.

### 6. Do not guess unresolved decisions

Distinguish:

- facts answerable from evidence
- product decisions owned by the user
- technical questions owned by RFC
- intentionally deferred work

If product intent remains ambiguous, ask.

If a technical question does not block product definition, defer it to RFC.

Do not edit product code while this skill is active.

## Process

### Step 1 — Capture the direction

Create or update `docs/prd/<date>+<feature>.md` with the currently established:

- vision
- problem or opportunity
- users or beneficiaries
- desired outcome
- known scope
- known constraints

Do not fill gaps by invention.

### Step 2 — Inspect evidence

Explore relevant project evidence to establish:

- current behavior
- existing capabilities
- prior decisions
- domain terminology
- relevant constraints

Record findings only when they materially affect product intent, scope, behavior, or constraints.

Technical findings that require design decisions belong in RFC.

### Step 3 — Resolve product decisions

Identify unresolved decisions that materially affect:

- outcome
- behavior
- scope
- UX
- compatibility
- priority
- risk
- success

If one or more remain, ask the single highest-value question and stop.

After each answer:

1. update the PRD
2. inspect newly relevant evidence
3. recompute unresolved decisions
4. repeat when necessary

Do not enter technical design or implementation.

### Step 4 — Shape the product

Refine:

- Core scope
- Supporting scope
- Deferred work
- Non-Goals
- product constraints
- expected product behavior
- product-level success

Remove complexity that is not required for the intended value.

### Step 5 — Converge

The PRD is ready for final review when:

- problem and desired outcome are clear
- users or beneficiaries are clear
- product value is understood
- expected product behavior is sufficiently clear
- Core scope is bounded
- meaningful Non-Goals are explicit
- important product constraints are known
- product-level success is defined
- no blocking product decision remains
- remaining technical questions can move to RFC without requiring RFC to guess product intent

Technical architecture does not need to be resolved.

Before final review, rewrite the PRD into its final form.

Remove:

- resolved questions
- duplicate facts
- abandoned ideas
- exploratory reasoning

Preserve:

- decisions
- scope
- constraints
- relevant evidence
- important assumptions
- technical questions relevant to downstream work

### Step 6 — Final review

Present a concise summary of:

- vision and problem
- desired outcome and value
- Core scope
- Supporting or Deferred scope when relevant
- Non-Goals
- important constraints
- product-level success
- technical questions deferred downstream
- artifact status

Then stop.

Do not begin RFC design or implementation in the same turn.

Explicit user approval of the latest final review is required before the PRD becomes approved.

If the product direction changes materially afterward, repeat the final review.

## PRD Template

Use the project's existing PRD format when available.

Otherwise use:

# PRD: <Title>

## Status

Draft | Proposed | Approved

## Vision

What should this product or feature make possible?

## Problem

What problem or opportunity are we addressing, and why does it matter?

## Users

Who benefits or is affected?

Include only groups relevant to actual product decisions.

## Desired Outcome

What should become possible when this succeeds?

## Product Behavior

Describe important user-visible or externally observable behavior.

Focus on outcomes rather than implementation mechanisms.

## Scope

### Core

Capabilities required to deliver the intended value.

### Supporting

Relevant capabilities useful but not essential to Core.

### Deferred

Capabilities intentionally postponed.

## Non-Goals

Adjacent problems or capabilities explicitly outside this effort.

## Constraints

Established product, platform, compatibility, business, policy, or operational constraints that affect product behavior.

## Success

Observable product-level outcomes indicating that the intended value has been achieved.

Do not manufacture metrics when none are required.

## Risks and Assumptions

Only important product assumptions or risks that affect scope, value, or expected behavior.

## Open Technical Questions

Technical questions intentionally deferred to downstream design.

Omit when none remain.

## References

Relevant research, issues, PRDs, RFCs, ADRs, Specs, prototypes, or repository evidence.

## Handoff

An approved PRD is ready for RFC or Spec when downstream work does not need to guess product intent.

Use the PRD as the authoritative product direction.

The PRD defines **what and why**.

The RFC, when needed, decides **how**.
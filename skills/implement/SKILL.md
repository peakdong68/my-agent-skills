---
name: implement
description: "Implement approved work from a spec or tickets. Follow established decisions, keep scope tight, debug execution failures as needed, and stop rather than inventing new product or design decisions."
disable-model-invocation: true
---

# Implement

Implement the approved spec or tickets without reopening established product or design decisions.

## Authority

You MAY:
- choose local implementation details
- add or update tests
- make small refactors required by the work
- use existing repository patterns and abstractions
- invoke `/diagnosing-bugs` for execution-time failures

You MUST NOT independently:
- change product behavior or scope
- change accepted RFC/ADR decisions
- change public contracts not authorized by the spec
- resolve genuine spec ambiguity by guessing
- add speculative architecture or unrelated cleanup

If implementation requires a new product, design, architecture, compatibility, migration, or risk decision, stop and surface the blocker. Do not invoke a user-controlled design skill on the user's behalf.

## Process

1. Read the governing spec/tickets and relevant ADRs/RFCs.
2. Inspect the affected code and existing test seams.
3. Trace each ticket to the behavior or acceptance criteria it delivers.
4. Implement the smallest coherent change.
5. Use `/tdd` where practical at the pre-agreed seam.
6. Run focused tests and typechecking regularly.
7. If an execution-time bug or unexpected technical failure appears, use `/diagnosing-bugs` and then continue.
8. Run the full relevant test suite once implementation is complete.
9. Once done, use `/code-review` to review the work.
10. Address blocking review findings within the approved scope.
11. Commit the completed work to the current branch.

## Escalation

Stop implementation when you discover:
- unclear or changed product intent
- unresolved scope or UX behavior
- an RFC/design decision that must change
- an ADR conflict
- an ambiguous or incorrect spec contract
- a compatibility or migration decision not already established

Report:
- what is blocked
- the conflicting evidence
- which upstream decision appears to own the issue

Do not silently choose a new direction.

## Completion

Implementation is complete when:
- approved scope is implemented
- focused and full relevant tests pass
- typechecking/build checks pass where applicable
- code review has no blocking finding
- no unresolved implementation blocker remains
- the current branch is committed

Implementation completion means **ready for user verification**, not product acceptance.

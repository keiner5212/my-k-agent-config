---
name: refactoring-patterns
description: >-
  Restructure code safely through small verified transformations, smell diagnosis,
  characterization, dependency migration, and explicit stop conditions.
---

# Refactoring Patterns

## Expert mental model
- Refactoring changes design while preserving observable behavior.
- Observable behavior includes values, errors, ordering, timing contracts, side effects, and resource use.
- A code smell is evidence of future change friction, not a violation of aesthetic rules.
- Small transformations reduce debugging search space and make review trustworthy.
- Safety comes from characterization, compiler, tests, and production signals, never confidence alone.
- Do not refactor absent a concrete future change cost or defect risk.

## Safety sequence
1. State behavior to preserve and change driver.
2. Map callers, side effects, serialization, public API, and operational boundary.
3. Find existing tests and identify coverage gaps at behavior level.
4. Add characterization test only if safe verification is missing and tests are in scope.
5. Choose one named transformation.
6. Make smallest compileable change.
7. Run nearest verification immediately.
8. Inspect diff for accidental behavior change.
9. Repeat or stop when target smell is no longer material.

## Characterization tests
- Characterization captures current behavior before claiming it is correct.
- Assert inputs, outputs, errors, side effects, ordering, and boundary serialization that callers rely on.
- Use golden masters for complex legacy output only when readable assertions are impractical.
- Freeze time, randomness, network, and environment so behavior can be compared.
- Avoid testing private implementation. Test existing public seams or create narrow seam only if needed.
- If behavior is known wrong, isolate correction as separate intentional change.
- A test that merely preserves bug is useful during movement, then replace with desired behavior test.

## Smell to transformation map
- Long method with distinct stages -> Extract Method by named domain step.
- Method with parameters repeatedly grouped -> Introduce Parameter Object.
- Feature envy -> Move Method to data owner.
- Divergent change -> Extract cohesive module around one reason to change.
- Shotgun surgery -> Move related behavior behind shared owner.
- Duplicate business decision -> Extract policy or authoritative domain function.
- Primitive obsession -> Introduce value object when invariant or unit attaches to primitive.
- Data clump -> Introduce value object only when fields travel and validate together.
- Type code conditional -> Replace with strategy only when variation is stable and open-ended.
- Switch on finite closed enum -> keep explicit switch when it is clearer than polymorphism.
- Message chain -> Hide Delegate when caller should not know traversal.
- Middle man -> Inline delegation that adds no policy, translation, or stability.
- Speculative generality -> Inline, delete dead branch, remove unused interface.

## Extract Method recipe
1. Identify contiguous statements serving one named purpose.
2. List inputs read, values assigned, and observable side effects.
3. Extract with domain name and explicit parameters.
4. Preserve operation order and error boundaries.
5. Compile and verify before next extraction.
6. Do not extract a one-line wrapper that conceals simple logic.

## Move Method recipe
1. Identify which object owns required data and invariant.
2. Make destination operation explicit while source delegate remains.
3. Migrate callers in small steps.
4. Remove source delegate after all call sites move.
5. Confirm no new dependency cycle or infrastructure leak appears.

## Public migration: expand-contract
- Add new field, endpoint, method, event, or interface without breaking old consumer.
- Dual-read or dual-write only for bounded transition with named removal date.
- Instrument old path use before removal.
- Migrate internal callers first, then external clients with communication window.
- Stop dual behavior after evidence shows no consumers remain.
- Never silently change meaning of an existing field under same name.

## Dependency reduction
- Depend on narrow behavior needed, not full concrete service.
- Break cycle by moving shared abstraction to lower stable layer, not by adding global registry.
- Replace static global access with explicit dependency at composition root.
- Split module when different change streams require different data, lifecycle, or policy.
- Do not split merely because file is long; seek cohesion and independent reason to change.
- Use facade at volatile external boundary to contain vendor and protocol variation.

## When not to refactor
- Requirement is unclear and current code is sufficient for one safe change.
- No verification path exists and change is high-risk production behavior.
- Code is scheduled for deletion or replacement soon.
- Refactor would require broad unrelated migration during incident response.
- Proposed abstraction has one concrete use and no stable variation.
- Performance-sensitive path lacks benchmark and structural change may regress it.
- Refactor does not reduce a named future cost, defect, or comprehension barrier.

## Failure modes juniors miss
- "Pure refactor" changes exception type, message, error timing, or null behavior.
- Extraction closes over mutable state and introduces new ordering bug.
- Rename misses reflection, config, serialization, SQL, template, or external caller.
- Shared helper centralizes syntax but merges different policy ownership.
- Strategy pattern creates class explosion for finite simple conditional.
- Characterization test locks incidental formatting rather than contract.
- Massive formatting change hides semantic movement and makes review unsafe.
- Legacy adapter persists forever because migration has no measurement or removal date.

## Implementation recipes
### Replace nested conditional
1. Add guards for invalid and terminal cases.
2. Name remaining happy path.
3. Extract policy only when multiple callers share same decision.
4. Preserve branch precedence exactly.
5. Add examples for boundary combinations if tests are in scope.

### Break cyclic dependency
1. Draw dependency direction and shared concepts.
2. Identify which side owns behavior, not just type.
3. Move stable value type or port to lower module.
4. Invert external dependency through narrow interface.
5. Verify composition root constructs concrete implementation.

## Critique rubric
- Change driver and behavior-preservation boundary are explicit.
- Verification exists before structural movement.
- Each commit-sized step uses named refactoring with small blast radius.
- API, serialization, event, error, and ordering contracts are considered.
- New abstraction has cohesive owner and demonstrated variation or shared knowledge.
- Dependency direction improves without introducing service locator or generic base.
- Migration has adoption evidence and deletion condition.
- Diff separates mechanical formatting, refactor, and feature behavior.

## Authoritative references
- https://refactoring.com/
- https://martinfowler.com/books/refactoring.html
- https://refactoring.guru/refactoring/catalog
- https://martinfowler.com/bliki/StranglerFigApplication.html
- https://www.industriallogic.com/xp/refactoring/

## Refactor stop checklist
- Stop when named smell no longer obstructs an expected change.
- Stop when next extraction no longer has independent behavior or ownership.
- Stop when added abstraction needs more explanation than original code.
- Stop before unrelated formatting obscures semantic review.
- Stop and separate work when behavior change becomes necessary.
- Stop if verification cannot distinguish transformation error from legacy behavior.
- Stop migration only after old-path telemetry shows no active consumers.

---
name: clean-code
description: >-
  Build code that reveals domain intent, preserves dependency direction, controls
  complexity, and makes correct change cheaper than accidental complexity.
---

# Clean Code

## Expert mental model
- Clean code reduces the cost and risk of the next correct change.
- Readability is domain communication, not line count or clever brevity.
- Duplication is duplicate knowledge with divergent future change risk, not repeated syntax.
- Abstraction hides detail only when it exposes a stable useful concept.
- A module boundary is good when reasons to change stay together and unrelated change stays out.
- Local simplicity beats speculative reuse. Reuse without shared semantics creates coupling.

## Explore before create
1. Search for existing domain type, command, repository, client, validator, and error model.
2. Read callers and tests to learn contract, lifecycle, and conventions.
3. Reuse if semantics and ownership match.
4. Extend if one focused capability is missing.
5. Create only when no existing owner fits without distortion.
6. Name new concept using product language, not technical placeholder.

## Naming system
- Names encode role and domain: `invoice`, `overdueInvoice`, `loadInvoice`, `InvoiceRepository`.
- Boolean starts with `is`, `has`, `can`, `should`, or domain predicate.
- Collection names are plural and disclose element: `pendingInvoices`, not `data`.
- Function verb plus object expresses effect: `reserveInventory`, `parseCursor`.
- Query names answer what they return; command names state state transition.
- Avoid vague nouns: `Manager`, `Helper`, `Processor`, `Util`, `Common`, `Info`, `Data`.
- Avoid generic verbs: `handle`, `process`, `do`, `run`, unless domain object gives exact meaning.
- One term means one concept. Do not alternate `user`, `member`, `account` for same thing.
- Preserve established domain vocabulary even when it is less technically elegant.

## Function design
- Function has one level of abstraction and one coherent purpose.
- Split when stages have independent names, tests, change reasons, or error policies.
- Do not split linear obvious code merely to satisfy line count.
- Prefer explicit input and output type over hidden ambient state.
- Aim for few parameters; more than four often signals missing value object or wrong responsibility.
- Boolean parameter often means two behaviors. Separate named functions when callers need clarity.
- Guard clauses expose preconditions and reduce nesting.
- Commands change state. Queries return information. Avoid hidden mutation in query.
- Return rich result or typed error when absence and failure have different meaning.

## Module and dependency boundaries
- Module owns one cohesive capability and hides its storage, protocol, and representation detail.
- Domain logic depends on ports or small interfaces at infrastructure boundary.
- Transport handler translates HTTP, RPC, or CLI to domain command; it does not own business rule.
- Repository translates persistence; it does not decide authorization or workflow policy.
- Inject external dependencies where replacement, lifecycle, or environment varies.
- Do not introduce interface for one stable local implementation without a boundary reason.
- Cyclic imports reveal confused ownership. Move shared domain concept to lower layer or change direction.

## SOLID as diagnostic tool
- S: a unit changing for pricing, notification, and persistence has multiple responsibilities.
- O: add behavior through a stable extension only when variants are real and expected.
- L: subtype that throws unsupported operation violates promised contract.
- I: client depending on read, write, admin, and export methods it uses none of is oversized.
- D: business rule constructing SQL client or HTTP client hard-codes infrastructure direction.
- SOLID is not class-count target. Apply it to reduce a concrete change hazard.

## Error design
- Preserve causal context at boundary: operation, safe identifier, expected recovery action.
- Do not catch broad error only to log and continue with partial state.
- Translate infrastructure error once at boundary, not at every call layer.
- Differentiate validation rejection, missing resource, conflict, transient dependency, and bug.
- Use typed or structured errors when callers need different recovery.
- Avoid sentinel `null`, empty string, and magic status when result cases differ.
- Never add sensitive request data, secret, or raw credential to error context.

## Comments and documentation
- Default zero comments. Good names and structure explain normal behavior.
- Comment only non-obvious constraint, decision, protocol caveat, or external reason.
- Keep comment one short ASCII sentence.
- Delete comment that restates code, narrates history, or promises future work.
- Public contract documents caller obligations, side effects, errors, and stability.
- Test name should document behavior; do not duplicate prose in comment.

## Duplication framework
- Same syntax with different business meaning can remain separate.
- Same business rule in two owners will drift; choose authoritative owner.
- Three similar call sites may show pattern, but compare rate of change before abstraction.
- Extract data-driven variation when structure is stable and inputs differ.
- Do not create generic utility that erases domain language.
- Prefer duplication temporarily over premature abstraction when requirements are uncertain.

## Implementation recipes
### Untangle overloaded handler
1. Keep transport parse and response mapping in handler.
2. Extract named command object for business workflow.
3. Move database detail behind repository only if existing boundary needs it.
4. Return explicit result cases.
5. Remove temporary duplicated decision only after caller behavior matches.

### Replace flag argument
1. Identify two semantic paths selected by flag.
2. Create two names that reveal intent.
3. Move each branch behind named operation.
4. Migrate callers one by one.
5. Remove flag once no caller needs it.

## Failure modes juniors miss
- Extracting class because function is long, then coupling remains through shared mutable state.
- Generic base class forces unrelated subclasses to inherit wrong lifecycle.
- DRY merges similar code whose business rules will diverge.
- Tiny helpers hide straightforward condition and scatter control flow.
- Dependency injection container becomes global service locator.
- Comment fixes bad name only for current reader and drifts later.
- Error wrapper loses retryability or root cause.

## Critique rubric
- Reader can state domain purpose without reading implementation.
- Names are consistent, specific, and distinguish query from command.
- Each module owns one cohesive capability and has clear dependency direction.
- Inputs, outputs, state effects, and failure cases are explicit.
- Abstraction removes real repeated knowledge without hiding meaningful difference.
- SOLID use addresses observed coupling, not pattern theater.
- Comments explain only non-obvious constraints.
- Change scope stays minimal and does not smuggle unrelated cleanup.

## Authoritative references
- https://google.github.io/styleguide/
- https://martinfowler.com/bliki/TwoHardThings.html
- https://refactoring.com/
- https://martinfowler.com/articles/microservices.html
- https://www.industriallogic.com/xp/refactoring/

## Review prompts
- Can a new engineer locate the business rule owner in under one minute?
- Does changing one policy require editing one authoritative place?
- Does every external dependency enter through a visible boundary?
- Would a caller misuse this API because names or result cases are ambiguous?
- Does exception handling preserve the error class needed for recovery?
- Does code reveal why a limit, ordering, or compatibility constraint exists?
- Is the abstraction simpler than its concrete call sites?
- Can the smallest correct change be made without touching unrelated modules?
- Did a local convenience leak storage or protocol detail into domain logic?
- Are tests readable examples of intended domain behavior?
- Would deletion of this code remove a real capability or only imagined future flexibility?

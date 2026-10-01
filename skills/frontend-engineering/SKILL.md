---
name: frontend-engineering
description: >-
  Engineer resilient browser applications: rendering boundaries, data ownership,
  cache behavior, forms, accessibility, and measured client performance.
---

# Frontend Engineering

## Expert mental model
- UI is projection of state plus user intent, not a sequence of imperative DOM changes.
- Classify every value: server state, URL state, local UI state, form draft, or derived value.
- Server cache is asynchronous shared state with freshness, invalidation, and race semantics.
- Client components cost JavaScript, hydration, and dependency closure; make boundary narrow.
- Render must be pure. Effects synchronize with external system, never calculate view state.
- Accessibility is functional engineering: semantics, keyboard path, focus, names, error feedback.

## Rendering boundary framework
- Server rendering excels at data access, secrets, streaming markup, and smaller client bundles.
- Client rendering is required for event handlers, browser APIs, local interaction, and client-only libraries.
- In RSC frameworks, keep data-heavy route and layout on server by default.
- Move only interactive leaf behind client boundary, not entire page for one button.
- Client component imports pull dependency graph into browser; inspect transitive bundle cost.
- Server component can pass serializable data, not function closures or class instances.
- Sensitive authorization still happens server-side even when server rendered.
- Use server action or route mutation when it simplifies trusted write boundary.

## State ownership decision
- Local ephemeral state: input focus, popover open, drag preview, temporary selection.
- Lift state to lowest common owner only when siblings coordinate.
- URL state: filters, sort, page, selected record, and anything linkable or reload-safe.
- Server state: remote entities, permissions, lists, mutation result, and invalidation.
- Global client state: only long-lived cross-route client-only concerns, such as theme or unsaved editor session.
- Derived state: compute during render. Do not store `filteredItems`, totals, or boolean derivations.
- A state variable needs one writer. Multiple writers create synchronization bugs.

## Query cache mental model
- TanStack Query cache is source of truth for fetched server data, not a convenience mirror.
- Query key identifies data: tenant, resource, filter, sort, cursor, and locale if applicable.
- Stable serializable keys prevent cache collision and invisible stale UI.
- `staleTime` means freshness policy; it does not retain data forever.
- `gcTime` controls unused cache lifetime; choose based on memory and navigation behavior.
- Invalidate exact affected key after mutation; avoid global invalidation when blast radius is known.
- Optimistic update needs cancel, snapshot, optimistic write, rollback, then invalidate.
- Server response remains authority. Reconcile optimistic state after settled mutation.
- Do not copy query data into state to edit a form; form draft is separate local state.

## Async UI architecture
- Place Suspense boundary around content that may wait independently.
- Put boundary lower than shell so navigation, heading, and prior content remain usable.
- One giant page boundary causes blanking; too many boundaries create visual fragmentation.
- Error boundary matches recovery scope: retry data panel, not necessarily whole route.
- Loading state must preserve layout geometry to avoid cumulative layout shift.
- Empty state differs from loading and authorization denial. Model each explicitly.
- Mutation shows pending state, success feedback, actionable error, and retry when safe.
- Cancellation and navigation must not apply obsolete response to new screen.

## Forms
- One schema owns field rules where possible; server repeats security-critical validation.
- Keep raw draft near form. Persist only deliberate submit or durable autosave.
- Validate field-level for immediate syntax feedback, form-level for cross-field invariants.
- Use stable field names mapping to domain command, not arbitrary UI labels.
- Disable submit only for known invalid or pending submission; preserve recovery path.
- Focus first invalid field on submit and link errors with `aria-describedby`.
- Treat server validation errors as authoritative and map them to fields or form summary.
- File input needs explicit size, type, upload state, cancellation, and server validation.

## React craft rules
- Keys identify stable item identity. Array index is unsafe for insert, delete, reorder, or filter.
- `useEffect` synchronizes subscriptions, timers, media, maps, or imperative APIs.
- Do not fetch in effect when route loader, server component, or query layer exists.
- Do not derive state with effect. Compute direct, or memoize only measured expensive calculation.
- `useMemo`, `useCallback`, and `memo` have comparison and complexity cost; profile first.
- Reducer helps when one state transition has many fields and named actions improve clarity.
- Context is dependency distribution, not default state store. Split high-churn contexts.
- Avoid effect chains where one state update triggers another effect. Model event directly.

## Accessibility as engineering
- Use native button, link, input, select, dialog before ARIA replacement.
- Every control has accessible name and visible focus indicator.
- Keyboard operation must work without pointer: tab, enter, space, escape, arrows when pattern requires.
- Modal moves focus inside, traps while open, restores trigger focus on close.
- Announce asynchronous status changes with appropriate live region, not repeated noisy updates.
- Error color alone is insufficient. Provide text, programmatic association, and focus path.
- Virtualized list must preserve meaningful navigation and item count semantics.
- Test with keyboard before screen reader; broken focus order is a product bug.

## Performance workflow
1. Define affected route, device class, network, and target metric.
2. Capture production Web Vitals and local performance trace.
3. Identify bottleneck: network, JavaScript, render, image, layout, or third party.
4. Change one measured cause.
5. Re-profile same flow and inspect regression in correctness and accessibility.

## Concrete budgets
- Target LCP <= 2.5 s, INP <= 200 ms, CLS <= 0.1 at 75th percentile.
- Avoid client bundle additions without measuring transfer and execution cost.
- Images need explicit dimensions, responsive source, modern format, and priority only above fold.
- Debounce search input around 150 to 300 ms when request rate needs control; never delay direct typing.
- Virtualize only long measured lists; virtualization adds focus and measurement complexity.
- Use request cancellation or latest-request guard for rapid changing query parameters.

## Implementation recipes
### Optimistic list mutation
1. Cancel active query for exact key.
2. Snapshot cached data.
3. Apply deterministic optimistic patch.
4. Submit mutation with idempotency behavior.
5. Restore snapshot on error.
6. Invalidate exact key after settlement.

### Server and client split
1. Fetch and authorize in server route component.
2. Render static content on server.
3. Pass minimal serializable props to interactive leaf.
4. Use query cache only where client refetch or mutation needs it.

## Failure modes juniors miss
- Client boundary pulls large date, chart, or editor library into all visitors.
- State mirror makes mutation response overwrite user draft.
- Query key omits tenant or filter and displays another context's cached result.
- Optimistic rollback loses newer mutation when snapshots are not scoped.
- Suspense fallback hides whole page after one nested refetch.
- Index keys transfer input state to wrong record after reorder.
- Memoization hides stale closure and makes profiling harder.
- Div click handler lacks keyboard behavior, focus, and disabled semantics.

## Critique rubric
- Every value has clear owner and one authoritative source.
- Server/client boundary minimizes browser code without moving trust to client.
- Query key, stale policy, mutation invalidation, and race handling are explicit.
- Route covers loading, empty, error, retry, pending, and unauthorized states.
- Form validates accessibly and server enforces business rules.
- Effects only synchronize external systems and clean up correctly.
- Performance change has trace evidence, not memoization folklore.
- Interaction passes native semantics, keyboard, focus, and screen-reader name checks.

## Authoritative references
- https://react.dev/learn
- https://nextjs.org/docs/app/building-your-application/rendering
- https://tanstack.com/query/latest/docs/framework/react/overview
- https://www.w3.org/WAI/ARIA/apg/
- https://web.dev/articles/vitals

---
name: ui-ux-patterns
description: >-
  Expert product UX for jobs-to-be-done, information architecture, navigation, forms, workflows,
  onboarding, feedback states, recovery, and heuristic critique. Use for journeys, wireframes,
  usability reviews, feature flows, dashboards, and interaction design decisions.
---

# UI/UX Patterns - Expert

## Mental model

UX is reduction of uncertainty at each decision. Users arrive with a job, incomplete context,
limited memory, and a cost for mistakes. The interface must reveal enough of the system model
to let them act, observe result, recover from failure, and continue.

Start from jobs-to-be-done:

```
When [situation], I want to [motivation], so I can [expected outcome].
```

"When an invoice fails, I want to see why and fix it quickly, so cash collection does not
slip" is a job. "Build an invoice settings screen" is an implementation request, not a job.

For each job, name user expertise, frequency, time pressure, consequence, entry point,
success condition, and recovery path. A rare high-risk action needs guidance and confirmation.
A frequent expert action needs speed, visible state, and low interaction cost.

## Information architecture

IA is a map, not a menu. Users need to know where they are, what belongs here, what is nearby,
and how to move without losing work.

Use nouns for destinations, verbs for actions, and user vocabulary over internal taxonomy.
"Billing" is better than "Revenue Operations" for customers. "Create invoice" is better than
"New document" if outcome is specific.

| Pattern | Choose when | Tradeoff |
|---|---|---|
| Hierarchical navigation | Stable categories and broad product | Can hide cross-cutting tasks |
| Search-first | Large corpus and known-item lookup | Poor for discovery |
| List-detail | Repeated object review | Needs responsive context plan |
| Hub and spoke | Few top-level tasks | Deep tasks may feel isolated |
| Wizard | Steps have hard dependencies | Slower for expert repeat use |
| Dashboard | Decisions require multiple signals | Easy to become card wallpaper |

Wayfinding requires current location, predictable back behavior, meaningful page title, and
stable navigation position. Breadcrumbs help deep hierarchy, not flat products pretending to
be complex. Tabs work for 2-7 peer views of same object, not primary site navigation.

## Flow choice

Single-page form works when fields are few, user can scan them, and dependencies are light.
It supports review and quick correction. It fails when length hides progress or conditional
complexity overwhelms comprehension.

Wizard works when later choices truly depend on earlier choices, legal sequence matters, or
users benefit from one concept at a time. Cost: users cannot easily compare choices across
steps. Show progress, permit back navigation, preserve data, and avoid fake steps.

Progressive disclosure works when advanced settings are rare. Hide complexity behind an
explicit label such as "Advanced delivery settings", not an unlabeled ellipsis. Cost: hidden
options are less discoverable, so use only for low-frequency or contextual choices.

Modal works for short focused confirmation or temporary details. Avoid modal chains. If task
needs navigation, deep editing, attachments, or help content, use a page or side panel.

## Form UX

Forms are conversations with error cost. Ask only what changes an outcome. Sequence from
easiest and most familiar to sensitive or complex, unless workflow dependency requires another
order. Group fields by mental model, not database schema.

- Put labels above fields. This scans faster and survives narrow width.
- Use visible persistent labels. Placeholder is an example, never label.
- Mark optional fields when most fields are required; mark required when exceptions dominate.
- Use one column for forms with input decisions. Two columns only for tightly coupled short fields.
- Provide useful defaults, but never default a costly, surprising, or privacy-sensitive choice.
- Validate format while typing only when feedback is stable and helpful. Validate required fields
  on blur or submit. Do not shout errors before user has a chance to enter value.
- State error as field, problem, correction: "Card number: enter 16 digits."
- Preserve input across network failure, page navigation warnings, and retry.

Disable submit only for true impossible state. If an action needs missing fields, let submit
expose specific errors or communicate requirements before action.

## Product moments

Loading, empty, error, and permission states are not edge cases. They are often first contact.

| State | Must answer | Good response |
|---|---|---|
| Loading | Is work happening? | Preserve layout, show progress for waits over 1 second |
| Empty | Why is nothing here? | Explain cause and one relevant next action |
| Error | What failed and can I recover? | Specific message, retained progress, retry |
| Permission | Why blocked and who can fix it? | State role or policy and path to request access |
| Offline | What still works? | Show cached state and pending sync behavior |
| Success | Did result persist? | Update visible state, then optional toast |

Skeletons fit when content shape is predictable and wait is brief. Use progress when duration,
stage, or cost matters. Never use a spinner for a multi-minute import without status or escape.

## Decision quality and recovery

Recognition beats recall. Keep current filters, selected scope, unsaved changes, and relevant
constraints visible. Use sensible defaults and reversible actions. For destructive work, name
scope, permanence, and recovery: "Delete 14 invoices. This cannot be undone."

Use undo for rapid, reversible actions with low side effects. Use confirmation for irreversible
or high-impact actions. Do not ask confirmation for every click; repeated confirmation trains
users to ignore risk.

## Nielsen heuristics as diagnosis

1. Visibility of system status: save button does nothing for two seconds.
2. Match to real world: "404 conflict" replaces "another editor changed this record."
3. User control: a filter clears when moving between pages.
4. Consistency: trash icon archives in one view and deletes in another.
5. Error prevention: date range allows end date before start date.
6. Recognition: user must remember a project ID from prior screen.
7. Flexibility: expert cannot use keyboard or duplicate a common record.
8. Minimalist design: dashboard shows every metric without a decision hierarchy.
9. Error recovery: "Request failed" gives no retry or explanation.
10. Help: complex permission model has no contextual explanation.

Use heuristics to find observable failures, not as a checklist that excuses a bad flow.

## Named patterns

Stripe checkout minimizes fields, reveals trust cues at decision points, and keeps payment
status explicit. It works because payment is high consequence and familiar. Do not import
checkout form density into exploratory internal software.

Linear uses command-first navigation, stable object views, and dense visible state for experts.
It works because repeat users value speed and learned shortcuts. Do not make novice onboarding
depend on hidden keyboard commands.

Material 3 uses clear component roles and explicit system feedback. Borrow its state clarity,
not every visual treatment. Apple HIG emphasizes directness and predictable navigation; use
that principle when designing actions, selection, and reversal.

## Implementation recipes

```tsx
<fieldset className="space-y-4">
  <legend className="text-base font-semibold">Billing contact</legend>
  <label className="grid gap-1.5">
    <span className="text-sm font-medium">Email</span>
    <input aria-describedby="email-help email-error" type="email" />
  </label>
  <p id="email-help" className="text-sm text-muted">Receipts go here.</p>
  <p id="email-error" className="text-sm text-danger" role="alert">Enter a valid email.</p>
</fieldset>
```

Use page title, task content, and primary action in stable order. Keep dialogs shallow. Keep
user state in URL where sharing, refresh, or back navigation should preserve it.

## Failure modes experts catch

- Dashboard metrics have no question, comparison, or action attached.
- Empty state assumes user error when no data is expected.
- Wizard is used to make a long form look shorter.
- Search is offered where user needs browse and learning.
- Cancel silently discards unsaved work without warning.
- Toast says success but list does not update.
- Navigation label follows organization chart instead of user language.
- Required field error appears before interaction and teaches users to ignore red text.

## Critique rubric

Reject a flow when:

1. Job, success condition, and failure recovery cannot be stated.
2. User must remember information that UI could show.
3. Pattern choice adds steps without reducing uncertainty or risk.
4. Form fields cannot be justified by an outcome they change.
5. Loading, empty, error, and permission states lack next action.
6. Current location, scope, and progress are not visible.
7. A user can make a costly mistake without clear prevention or recovery.

## Refs

- https://www.nngroup.com/articles/ten-usability-heuristics/
- https://www.w3.org/WAI/WCAG22/quickref/
- https://m3.material.io/

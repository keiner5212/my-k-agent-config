---
name: baseline-ui
description: >-
  Expert baseline for component UI systems: spacing, typography, density, control anatomy,
  interaction states, feedback, layering, Tailwind conventions, and implementation critique.
  Use when building or reviewing web interfaces, design-system primitives, tables, and forms.
---

# Baseline UI - Expert

## Mental model

Baseline UI is the boring precision that makes product work feel trustworthy. It creates
predictable geometry, hierarchy, behavior, and recovery before visual art direction begins.
Users should learn a control once, then carry that learning across every screen.

Treat each component as a contract:

- Structure: semantic element, label, value, supporting text, action.
- Geometry: predictable height, padding, alignment, and hit area.
- State: default, hover, focus-visible, active, disabled, loading, error, success.
- Behavior: pointer, keyboard, touch, screen reader, and async response.
- Context: inline, dense list, modal, mobile, and high-zoom behavior.

Consistency is not identical appearance. A destructive action needs different color, copy,
and confirmation because its risk differs. A compact data toolbar can be denser than a form
because repeated expert scanning differs from error-prone input.

## Scale decisions

Use a modular scale for type and spacing, then break it only for a documented reason.

| Scale | Ratio | Best use | Cost if misused |
|---|---:|---|---|
| Major third | 1.25 | Product UI, dense dashboards | Can feel flat for editorial marketing |
| Perfect fourth | 1.333 | Marketing, presentations, strong hierarchy | Jumps grow too fast in compact UI |
| 4px grid | 4px | Padding, gaps, component alignment | Ad hoc values destroy rhythm |

Start UI type at 12, 14, 16, 20, 25, 31, 39, 49px for 1.25. Use 14, 16, 21, 28, 38,
50px for a 1.333 editorial scale. Use 2px exceptions only for borders, optical centering,
and small icon correction.

Use 4, 8, 12, 16, 24, 32, 48, 64px spacing. Choose space by relationship:

- 4-8px: label to input, icon to text, compact control internals.
- 12-16px: related fields, card internals, list item content.
- 24-32px: sections within a task area.
- 48-64px: major page regions and narrative breaks.

## Density modes

Density is a product decision, not a random CSS choice.

| Mode | Row height | Input height | Card padding | Use when |
|---|---:|---:|---:|---|
| Spacious | 56-64px | 48px | 24-32px | Low-frequency, guided, trust-heavy tasks |
| Comfortable | 44-52px | 40-44px | 16-24px | General product UI |
| Compact | 32-40px | 32-36px | 12-16px | Expert, repeated, data-heavy work |

Do not mix modes inside one task surface. A compact table next to giant form controls signals
that no one decided how the work is performed. Maintain 44 by 44px hit targets for touch
actions even when visual controls are compact by enlarging transparent hit area where needed.

## Component anatomy

Buttons need a verb, clear priority, and stable width during loading. Use primary for the
main commit, secondary for alternate action, ghost for low-emphasis local action, destructive
only for irreversible or high-cost action. Do not use disabled submit as hidden validation.

Inputs need visible labels above field for fastest scanning and strongest error association.
Put helper text below. Put unit, prefix, or action inside only when it is part of value syntax.
Placeholder is an example, never a label. Use 40-44px comfortable input height; use 48px for
touch-first flows. Keep error text adjacent and preserve entered value.

Checkboxes select independent choices. Radios choose one from a small visible set. Selects
choose from longer stable sets. Comboboxes search large or remote sets. A segmented control
switches between 2-5 peer views, not arbitrary navigation.

## Interaction state matrix

Every interactive primitive defines all rows before release.

| State | User needs | Implementation signal |
|---|---|---|
| Default | Recognize affordance | Label, border or fill, adequate contrast |
| Hover | Confirm target | Subtle color or elevation, never layout shift |
| Focus-visible | Keyboard location | 2px ring, 2-4px offset, 3:1 contrast |
| Active | Press acknowledged | 1-2px transform or darker fill, 80-140ms |
| Disabled | Understand unavailable | Reduced emphasis plus explanatory copy when needed |
| Loading | Know work continues | Preserve label width, spinner or progress text |
| Error | Recover specific failure | Field message, invalid state, retained input |
| Success | Confirm durable result | State update first, toast second if useful |

Focus must not look like hover. Loading must not look like disabled. Success toast must not
be the only evidence that data changed.

## Feedback decisions

Use inline feedback when it changes what user must do next or relates to a field. Use a toast
for a completed background action with no immediate decision. Use a banner for persistent,
page-wide, or permission-impacting state. Use a modal only when action needs interruption,
confirmation, or impossible-to-undo consequence.

| Event | Pattern | Why |
|---|---|---|
| Invalid email field | Inline error | Error belongs at source |
| Saved preference | Quiet toast plus updated value | Acknowledge without interruption |
| Lost connection | Persistent banner | State affects many actions |
| Delete workspace | Confirmation modal | Makes scope and consequence explicit |
| Empty search | Inline empty state | Keeps user in task context |

## Lists and tables

Use lists for heterogeneous items or narrow screens. Use tables when users compare values down
columns. Align numeric data right and text left. Use tabular figures. Let row click open detail
only if a clear primary action exists; keep secondary actions discoverable without hover.

Tables need a visible sort state, sticky header only when vertical scan warrants it, and a
responsive strategy: hide low-priority columns, move details into row expansion, or switch to
a labeled list. Horizontal scroll is acceptable for analytical data only when frozen identity
column and scroll cue preserve comprehension.

## Layering and z-index

Build a named layer scale. Do not solve overlap with `z-index: 9999`.

```css
:root {
  --z-base: 0;
  --z-raised: 10;
  --z-sticky: 20;
  --z-dropdown: 30;
  --z-modal: 40;
  --z-toast: 50;
}
```

`transform`, `filter`, `opacity` below 1, `isolation`, `contain`, and positioned elements with
z-index create stacking contexts. A child cannot escape its parent context. Render menus and
dialogs in a portal when clipping or local stacking would trap them.

## Tailwind recipe

```tsx
<button
  className="inline-flex min-h-10 items-center justify-center gap-2 rounded-md bg-action px-4
    text-sm font-medium text-on-action transition-colors duration-150 hover:bg-action-hover
    focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-focus
    focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50"
>
  Save changes
</button>
```

Use token-backed classes. Avoid arbitrary values unless layout requires a content-derived
measurement. Add `min-w-0` to flex or grid children holding text. Use `dvh`, not `h-screen`,
for mobile full-height layouts.

## Failure modes experts catch

- Borders vanish against surface in dark mode.
- Icon button has 28px visual size and 28px hit target.
- Button spinner changes width, shifting adjacent controls.
- Error appears only after submit but disabled button prevents submit.
- Focus ring gets clipped by `overflow: hidden`.
- Sticky header sits under dropdown due to accidental stacking context.
- Table turns into cards but loses row comparison and sortable columns.
- Toast announces delete while deleted row remains visible.
- Skeleton uses wrong line lengths, causing content jump.

## Critique rubric

Reject a component if:

1. Its semantic element does not match its action.
2. Any state is missing or indistinguishable from another state.
3. Spacing cannot be explained by relationship and scale.
4. User cannot recover from error without remembering lost input.
5. Dense content loses scanability or spacious content loses task focus.
6. Overlay behavior relies on unexplained z-index escalation.
7. The component breaks at 320px, 200% zoom, keyboard navigation, or loading.

## Refs

- https://www.w3.org/WAI/WCAG22/quickref/
- https://m3.material.io/components
- https://developer.apple.com/design/human-interface-guidelines/

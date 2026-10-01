---
name: fixing-accessibility
description: >-
  Expert WCAG 2.2 AA accessibility remediation for semantic HTML, keyboard interaction, focus,
  ARIA, screen readers, forms, dialogs, tables, live regions, zoom, and reduced motion. Use for
  accessibility audits and targeted fixes in web components, pages, and design systems.
---

# Fixing Accessibility - Expert

## Mental model

Accessibility is correct interaction under different sensory, motor, cognitive, and technical
conditions. It is not adding ARIA after visual work. A robust interface gives every user the
same task outcome through semantics, keyboard behavior, perceivable state, and recoverable flow.

Start with native HTML because browser, keyboard, accessibility API, and assistive technology
already agree on its behavior. ARIA changes the accessibility tree, not browser behavior. If
you add `role="button"` to a `div`, you inherit keyboard handling, focus management, disabled
behavior, activation semantics, and testing burden that a `<button>` already has.

Audit user journeys, not isolated attributes. A page can have correct labels but fail because
focus disappears after submit, a dialog traps users, or an async error is never announced.

## WCAG 2.2 AA priorities

AA is baseline for public product UI. AAA matters for critical reading, public services,
education, low-vision audiences, or explicit contractual requirement, but do not claim AAA
from a few contrast checks.

| Requirement | Concrete implementation |
|---|---|
| 1.3.1 Info and relationships | Use headings, lists, labels, table headers, fieldsets |
| 1.4.3 Contrast | 4.5:1 normal text, 3:1 large text |
| 1.4.11 Non-text contrast | 3:1 for meaningful controls and focus indicators |
| 2.1.1 Keyboard | Every action works without pointer |
| 2.4.3 Focus order | DOM order follows task order |
| 2.4.7 Focus visible | Focus indicator remains visible |
| 2.4.11 Focus not obscured | Sticky UI cannot hide focused item |
| 2.5.8 Target size | At least 24 by 24 CSS px minimum AA rule |
| 3.2.2 On input | Changes do not surprise on focus or input |
| 3.3.1 Error identification | Name affected field and problem |
| 4.1.2 Name, role, value | Expose control identity and state |

Use 44 by 44px as practical touch target for primary mobile controls even though WCAG 2.2 AA
target-size minimum is 24 by 24px with exceptions. Legal conformance is not the same as good use.

## Keyboard model

Tab moves between interactive elements in visual and task order. Shift+Tab reverses. Enter
activates links and buttons. Space activates buttons, toggles checkboxes, and scrolls page only
when control does not own it. Escape closes dismissible dialogs, menus, and popovers.

Never use positive `tabindex`. It creates a second navigation order users cannot predict.
Use `tabindex="0"` only for a custom composite widget's active item when native controls cannot
represent it. Use `tabindex="-1"` for programmatic focus target such as error summary or page
heading after navigation.

Roving tabindex is appropriate for composite widgets such as tabs, toolbar, grid, or menu:
one item remains in tab order, arrow keys move internal active item. Do not use it for simple
sets of buttons or links.

## Focus management

Focus follows user intent:

- On route change, place focus on main heading or main landmark if context changed.
- On validation failure, move to error summary or first invalid field based on error count and
  user context. Keep summary links to fields.
- On dialog open, focus first meaningful interactive element, often title-adjacent content or
  least destructive action, not always first input.
- Trap focus only while modal dialog is open. Escape closes unless operation cannot safely stop.
- On close, restore focus to trigger unless trigger disappeared; then use logical next target.
- Ensure sticky header, toast, and bottom bar do not visually cover focus target.

Use `:focus-visible`, not `:focus`, to avoid removing pointer behavior. Ring needs 2px minimum
thickness and 3:1 contrast against adjacent colors. Use 2-4px offset where border blends.

## ARIA decision tree

1. Does native element express semantics and behavior? Use it.
2. Can `<details>`, `<dialog>`, `<select>`, or existing accessible primitive solve it? Use it.
3. Is custom widget unavoidable? Follow WAI-ARIA APG interaction model exactly.
4. Does ARIA state change with UI state? Implement and test every transition.

Common correct ARIA:

- `aria-expanded` on control that toggles a disclosed region.
- `aria-controls` only when a useful relationship exists.
- `aria-current="page"` on current navigation link.
- `aria-invalid="true"` on invalid form control.
- `aria-describedby` for persistent help and error text.
- `aria-pressed` for a toggle button, not selection in a tab list.
- `aria-selected` for active tab or option inside proper composite pattern.

Common misuse:

- `role="button"` on native button.
- `aria-label` overriding visible label with different words.
- `aria-hidden="true"` on focusable content.
- `role="application"` on a normal web page.
- Adding `aria-live` to whole page or every input.
- Using `role="menu"` for ordinary action list.

## Forms recipe

```tsx
<label htmlFor="email">Email</label>
<input
  id="email"
  name="email"
  type="email"
  aria-invalid={hasError || undefined}
  aria-describedby="email-help email-error"
/>
<p id="email-help">We send receipts here.</p>
{hasError && <p id="email-error" role="alert">Enter a valid email address.</p>}
```

Use `<fieldset>` and `<legend>` for related controls such as address or payment method. Do not
use color alone for errors. Preserve user values after validation and network failure. Do not
block paste, password managers, or browser validation tools.

## Dialog recipe

Use native `<dialog>` or an established accessible dialog primitive. A modal needs accessible
name, optional description, focus containment, Escape dismissal when safe, backdrop behavior,
scroll lock without layout shift, and focus restoration.

Do not use modal for a full workflow. Do not auto-open modal for marketing or repeated notices.
For destructive confirmation, focus least destructive action by default.

## Tables and data

Use `<table>` for two-dimensional comparison. Use `<caption>` to state purpose, `<th scope>` for
headers, and row headers when row labels identify data. Do not recreate a data table with divs.
For sortable columns, announce sorted field and direction in button label or visible text.

On mobile, preserve relationships by showing row label before values, offering detail view, or
allowing horizontal scroll with identity column. Do not remove headers without replacement.

## Live regions

Use `role="status"` or `aria-live="polite"` for nonurgent completion and dynamic totals.
Use `role="alert"` only for urgent errors needing immediate interruption. Insert new message
into existing live region after event. Do not make a spinner itself a live region.

Announcements must be concise, deduplicated, and meaningful: "Invoice saved" is useful.
"Loading, loading, loading" is noise. Critical state also needs a visible durable update.

## Screen reader testing protocol

Test keyboard first with no mouse:

1. Tab from browser chrome through page. Confirm order, names, visible focus, and no traps.
2. Activate buttons, links, menus, dialogs, tabs, and forms by keyboard.
3. Trigger errors, loading, success, and deletion. Confirm focus and announcements.
4. Test at 200% zoom and narrow viewport for clipping and horizontal task failure.
5. Test one screen reader: NVDA with Firefox on Windows, VoiceOver with Safari on macOS or iOS,
   or TalkBack with Chrome on Android.
6. Navigate by headings, landmarks, links, form controls, and tables. Confirm structure tells
   same story as visual page.

Automated tools detect some syntax and contrast issues. They cannot verify meaningful alt text,
correct flow, focus timing, or whether an announcement helps.

## Failure modes experts catch

- Visual order differs from DOM order due to CSS `order`.
- Focus ring is present but hidden behind sticky footer.
- Alert fires before text changes, so nothing is announced.
- Dialog appears without focus movement and background remains tabbable.
- Icon has accessible name but name duplicates adjacent visible text.
- Error summary exists but has no links to bad fields.
- Skeleton changes reading order or exposes meaningless controls.
- Table headers are visually styled divs rather than semantic headers.

## Critique rubric

Reject work when:

1. Native HTML could replace a custom ARIA control.
2. Keyboard path cannot complete primary task without pointer.
3. Focus order, visible indicator, opening, closing, or restoration is wrong.
4. Screen reader name, role, state, and value do not match visual control.
5. Error, loading, and success are not perceivable and recoverable.
6. Zoom, reflow, contrast, or target size breaks a primary action.
7. Test result relies only on automated checker.

## Refs

- https://www.w3.org/WAI/WCAG22/quickref/
- https://www.w3.org/WAI/ARIA/apg/
- https://www.w3.org/WAI/tutorials/forms/
- https://www.w3.org/WAI/tutorials/tables/
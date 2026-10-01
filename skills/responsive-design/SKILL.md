---
name: responsive-design
description: >-
  Expert responsive web design for content-first breakpoints, fluid type, container queries,
  touch and pointer behavior, mobile layout transformations, safe areas, responsive media, and
  performance. Use for responsive features, mobile defects, layout systems, and component APIs.
---

# Responsive Design - Expert

## Mental model

Responsive design preserves task completion as available space, input method, text length, and
network conditions change. It is not making a desktop screenshot narrower. Start from content
priority: what must remain visible, what can reflow, what can move behind disclosure, and what
must never disappear.

A breakpoint is a failure threshold. Add it when a line becomes unreadable, controls no longer
fit, a comparison stops working, or navigation loses wayfinding. Device names are weak evidence:
a 768px tablet in split screen and a 768px desktop pane need different solutions.

Use intrinsic layout first. Let content define its minimum and maximum size. Then use grid,
flex, `minmax()`, `clamp()`, and container queries to adapt without brittle device branches.

## Content-first breakpoints

Record why every breakpoint exists:

| Failure | Response | Typical range |
|---|---|---|
| Navigation labels wrap | Collapse to menu or overflow | 640-900px |
| Two-column form fields crowd | Stack fields | 560-720px |
| Data columns lose meaning | Hide low-priority data or change pattern | 720-960px |
| Reading line is too long | Cap measure, not viewport | 65-75ch |
| Card min width fails | Reduce grid columns | Content-derived |

Use common check widths 320, 375, 480, 768, 1024, 1280, and 1440px, but do not encode all
as breakpoints. Test between them. Most responsive bugs occur at inconvenient widths.

## Fluid type and spacing

Use `clamp()` for values that should interpolate. Use steps when hierarchy must change sharply.

```css
:root {
  --page-gutter: clamp(1rem, 0.5rem + 2vw, 4rem);
  --title-size: clamp(2rem, 1.2rem + 3vw, 4.5rem);
  --content-width: min(100% - 2 * var(--page-gutter), 72rem);
}
```

Set readable prose measure independently: 45-75ch. Avoid fluid body text that falls below 16px
or grows so large that normal content becomes sparse. Test 200% zoom; `clamp()` is not a reason
to prevent user text scaling.

## Media queries versus container queries

Media queries answer page-level questions: navigation shell, global gutters, viewport height,
coarse pointer, reduced motion, color scheme. Container queries answer component-level questions:
does this card, toolbar, or form fieldset have enough inline space in this parent?

```css
.metric-panel { container-type: inline-size; }

@container (min-width: 42rem) {
  .metric-panel__content { grid-template-columns: 1fr auto; }
}
```

Use media query when behavior depends on viewport or environment. Use container query when same
component can live in main content, sidebar, modal, or split pane. Do not add a container to
every element; containment changes sizing and increases system complexity.

## Layout recipes

### Sidebar collapse

At wide widths, keep navigation and workspace visible. At narrow widths, move navigation into
a labeled menu or bottom navigation for 3-5 top-level destinations. Preserve current location,
primary action, and back path. Do not delete sidebar without a replacement.

```css
.shell { display: grid; grid-template-columns: 16rem minmax(0, 1fr); }
@media (max-width: 50rem) {
  .shell { grid-template-columns: minmax(0, 1fr); }
  .sidebar { display: none; }
}
```

### Data table to mobile

Choose by task. For comparison, retain table with horizontal scroll, visible scroll cue, sticky
identity column, and minimum widths. For record review, transform each row into labeled list.
For one primary field plus details, make list-detail. Never convert table to cards and lose
header-value relationships.

### Cards to list

Cards work for independent visual objects. On mobile, use list rows when card padding creates
scroll fatigue and content is primarily textual. Keep primary metadata and action visible; move
secondary metadata to detail or disclosure.

### Forms

Start one column. Stack fields before labels wrap. Keep related controls together. Make actions
full width only when one-handed reach and task commitment benefit; desktop button width need not
be copied to phone.

## Input modality

Touch is coarse and hoverless. Pointer can be fine or coarse. Keyboard remains essential.

- Primary touch targets: 44 by 44px practical minimum.
- Do not rely on hover for instructions, controls, or status.
- Use `@media (hover: hover) and (pointer: fine)` for hover enhancement only.
- Keep drag alternatives: buttons, menus, keyboard, or direct input.
- Avoid hover tooltips as sole label for icon buttons.
- Respect virtual keyboard: focused fields can be covered by fixed bottom UI.

Use `env(safe-area-inset-bottom)` for fixed mobile bars. Use `dvh` for current visible height
and `svh` when avoiding browser chrome expansion matters. Do not default to `100vh` or `h-screen`.

## Responsive images and performance

Correct responsive media reduces bytes and layout shift.

```html
<img
  src="hero-960.webp"
  srcset="hero-640.webp 640w, hero-960.webp 960w, hero-1600.webp 1600w"
  sizes="(min-width: 1024px) 50vw, 100vw"
  width="1600"
  height="900"
  alt="Dispatch planner showing route conflicts"
>
```

Set `width` and `height` or `aspect-ratio` to reserve space. Use `object-fit: cover` only when
crop is intentional and subject position is tested. Use `loading="lazy"` for below-fold media,
not hero LCP image. Use `fetchpriority="high"` only for actual LCP candidate.

## Failure modes experts catch

- `100vw` inside padded layout causes horizontal scrollbar.
- Flex child retains intrinsic width because `min-width: 0` is missing.
- Side panel closes on phone but no route or drawer reaches same actions.
- Desktop table becomes unreadable card mosaic without comparison strategy.
- Fixed action bar covers input under virtual keyboard or home indicator.
- Image source is huge for mobile, harming LCP and bandwidth.
- CSS order makes visual order differ from keyboard and screen-reader order.
- Text looks correct in English but breaks under 30 percent expansion.

## Critique rubric

Reject work when:

1. Breakpoints are named after devices instead of observable content failure.
2. Essential task, state, or navigation disappears on small screens.
3. Layout requires a horizontal scrollbar except deliberate data comparison.
4. Components only work at full viewport width, not narrow containers.
5. Touch, keyboard, hover, and virtual keyboard cases were not considered.
6. Image crop, size, and loading behavior cause shift or wasted transfer.
7. Interface fails at 320px, 200% zoom, long text, or split view.

## Refs

- https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/CSS_layout/Responsive_Design
- https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries
- https://web.dev/learn/design/

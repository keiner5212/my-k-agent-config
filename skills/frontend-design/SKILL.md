---
name: frontend-design
description: >-
  Expert frontend art direction, typography, composition, motion, visual systems, and React or
  Tailwind implementation. Use for product pages, application surfaces, dashboards, redesigns,
  brand expression, visual critique, and any UI where appearance changes task comprehension.
---

# Frontend Design - Expert

## Mental model

Design is ordered attention, not decoration. A good interface tells a user what this is,
what matters now, what can be done, and what changed after action before they need to think.

Start with product truth: audience expertise, task urgency, content volume, trust level,
and brand voice. Then make a design commitment. "A dense, sober operations console with
quiet color, compact type, and one high-signal status rail" is actionable. "Modern" is not.

Judge every choice on five axes:

| Axis | Low end | High end | Wrong choice cost |
|---|---|---|---|
| Density | spacious | compressed | Scan failure or wasted space |
| Contrast | restrained | emphatic | Weak hierarchy or visual fatigue |
| Ornament | plain | expressive | Generic product or task distraction |
| Motion | still | choreographed | Dead feedback or attention theft |
| Voice | neutral | opinionated | Unmemorable UI or brand mismatch |

Commit to a point on every axis. Mixing unrelated answers creates AI slop: gradient text,
glass cards, bento grids, floating blobs, pill badges, and shadows without product reason.

## Decision frameworks

Choose layout from task shape, not screenshot fashion.

| Task | Best default | Why | Tradeoff |
|---|---|---|---|
| Read and decide | editorial column | Creates reading rhythm | Lower information density |
| Compare records | table or list-detail | Preserves scanning columns | Needs responsive plan |
| Monitor changing state | dashboard with drill-down | Supports overview then evidence | Easy to make decorative |
| Complete a process | single focused flow | Reduces context switching | Less parallel visibility |
| Explore complex objects | sidebar plus workspace | Stable navigation and context | Consumes width |

Use a card when it is a movable, independently actionable unit. Use a section when content
shares a page rhythm. A card around every paragraph hides hierarchy and adds visual noise.

Reserve one primary visual action per task area. A second colored button is valid only when
it advances an equally common, mutually exclusive task. Otherwise use a quiet secondary action.

## Typography craft

Typography creates hierarchy before color does. Define roles, not arbitrary sizes:
display, page title, section heading, body, label, metadata, code, and numeric data.

- Reading measure: 45-75ch for body copy, 35-55ch for dense documentation.
- Body text: 16-18px desktop, 16px minimum mobile, line-height 1.45-1.7.
- Tight display: line-height 0.95-1.1. Headings: 1.1-1.25. Labels: 1.2-1.4.
- Track display type from -0.03em to -0.01em at 32px and above. Track all-caps labels
  from 0.06em to 0.12em. Do not letter-space normal body copy.
- Use 400 or 450 for body, 500 or 600 for interface emphasis, 650-750 for display.
- Use tabular figures for financial, temporal, or column-aligned numbers.

Pair faces by contrast in purpose, not novelty. A display serif with a neutral sans body can
work for editorial products. A grotesk display and humanist sans body can work for software.
One family with optical sizes is usually safer for dense products. Do not use monospace for
body prose; use it for code, identifiers, and values needing character alignment.

## Composition craft

Alignment is a promise. Establish a page grid before styling: typically 4 columns at 320px,
8 at tablet, and 12 at desktop. Use a 16-24px mobile gutter and 32-64px desktop gutter.

Use Gestalt intentionally:

- Proximity groups related controls. Give unrelated groups a larger gap.
- Similarity signals shared behavior. Do not style links, tags, and filters identically.
- Common region lets a background or border establish one conceptual unit.
- Figure-ground gives active content stronger contrast than surrounding chrome.
- Continuity makes alignment guide eye movement through a task.

Negative space is structure. Increase space between sections before adding dividers. A divider
is useful when proximity alone cannot separate dense, adjacent concepts.

Balance visual weight, not symmetrical pixels. Dark, saturated, large, high-contrast, or
moving elements weigh more. Put weight near intended next action, then remove competitors.

## Pattern catalog

Linear-style product UI uses compact hierarchy, restrained surfaces, and strong typography.
It works for expert users because repeated layout lets data, state, and keyboard speed lead.
Do not copy its dark palette; borrow its discipline: visible state, low chrome, clear density.

Stripe-style documentation and checkout surfaces use generous space, explicit sequencing, and
highly legible type. This works when trust and comprehension outrank data density. Do not add
large empty regions to operational screens where users need scan speed.

Apple HIG favors direct manipulation, stable navigation, and clear material hierarchy. Borrow
the principle that controls should feel predictable and content should own the screen. Do not
blindly import platform patterns into a web app with different navigation expectations.

Bloomberg terminal density works because every compact region has a stable purpose, strong
alignment, and trained users. Dense UI without information architecture is merely cramped.

## Signature detail

Every significant surface needs one product-specific detail: a distinctive data treatment,
editorial crop rule, status rail, spatial transition, tactile control, or meaningful texture.
It must clarify product meaning or brand voice. It cannot be a generic gradient, glow, glass
blur, animated mesh, random illustration, fake metrics, or decorative floating object.

Example: a logistics app can use route-line motifs to bind dispatch status to map geography.
Example: an archive can use typographic index markers to make chronology legible.

## Motion choreography

Motion explains cause and effect. Use `transform` and `opacity`; avoid animating layout,
filter, large blur, box-shadow, and paint-heavy properties.

- Entrances: 180-280ms, ease-out, 8-16px travel maximum.
- Exits: 120-200ms, ease-in, usually less travel than entry.
- Press feedback: 80-140ms. Hover feedback: 120-180ms.
- Stagger sibling entrances by 20-50ms, cap total sequence at 300ms.
- Use shared-element thinking: an item opens from its source position, preserving identity.
- Honor `prefers-reduced-motion`; remove travel, retain essential opacity feedback.

Motion is wrong when it delays reading, changes layout under pointer, repeats on scroll, or
exists only to prove the interface is animated.

## Implementation recipes

Use design tokens. Components consume semantic roles, never one-off values.

```css
:root {
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --text-body: clamp(1rem, 0.95rem + 0.2vw, 1.125rem);
}

.prose {
  max-inline-size: 68ch;
  font-size: var(--text-body);
  line-height: 1.6;
}
```

```tsx
<main className="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
  <header className="max-w-3xl space-y-4 py-12">
    <p className="text-sm font-medium tracking-[0.08em] uppercase">Operations</p>
    <h1 className="text-balance text-4xl font-semibold tracking-[-0.02em] sm:text-6xl">
      Dispatch without losing context
    </h1>
  </header>
</main>
```

Use `text-wrap: balance` only for short headings. Use `text-wrap: pretty` for longer text.
Set `min-width: 0` on grid and flex children containing long text.

## Failure modes experts catch

- A hero promises a benefit but product screenshot cannot prove it.
- Every section has equal contrast, so no reading order exists.
- Heading wraps leave an orphan word because width and type scale were not tuned together.
- Icons differ in stroke, optical size, or metaphor, making controls feel improvised.
- Hover-only affordance disappears for touch, keyboard, and coarse pointers.
- Empty states are visually polished but fail to explain first useful action.
- Skeleton shape differs from loaded content, causing layout shift and false expectation.
- Desktop composition is shrunk rather than reprioritized for small screens.
- "Premium" means low-contrast text and oversized whitespace instead of precise material choices.

## Critique rubric

Reject work when any answer is no:

1. Can a first-time user name page purpose and primary action in five seconds?
2. Does every section have a reason for its spacing, contrast, and surface treatment?
3. Does typography alone establish title, task, body, metadata, and control hierarchy?
4. Does one signature detail express this specific product rather than a generic AI aesthetic?
5. Does motion explain state or spatial continuity and respect reduced motion?
6. Does the layout preserve task completion at 320px, 200% zoom, and long translations?
7. Would removing half the shadows, radii, gradients, and pills improve clarity? If yes, remove them.

## Refs

- https://www.w3.org/WAI/WCAG22/quickref/
- https://developer.apple.com/design/human-interface-guidelines/
- https://m3.material.io/
- https://www.w3.org/TR/css-text-4/

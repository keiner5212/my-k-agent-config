---
name: ui-ux-pro-max
description: >-
  Expert UI art direction and product-fit decisions by product type: visual systems, density,
  information architecture, typography, palette adaptation, data display, chart selection, and
  rejection of generic AI UI. Use when visual direction is unclear or design needs a senior critique.
---

# UI/UX Pro Max - Expert

## Mental model

Visual quality is product fit made visible. Select a system from user expertise, task frequency,
consequence, content volume, brand promise, and platform constraints. Do not begin with style
labels or component libraries. Begin with a design thesis:

```
For [audience] doing [job], use [density and hierarchy] with [voice and visual material]
because [product truth]. Signature detail: [specific product-derived expression].
```

Example: "For finance operators reconciling exceptions, use compact data-first hierarchy,
restrained cool neutrals, and strong variance color because scan speed and confidence matter.
Signature detail: an anchored variance rail that preserves account identity during drill-down."

Choose commitment across density, contrast, ornament, motion, and voice. A UI that is "clean,
modern, premium, playful, technical, and minimal" has not made a decision.

## Product decision matrix

| Product | Primary task | Best system | Why | Avoid |
|---|---|---|---|---|
| B2B SaaS | Execute repeat workflow | Quiet utilitarian, comfortable to compact | Learned speed matters | Marketing-scale cards |
| Analytics | Detect and investigate change | Dense overview plus drill-down | Signals require context | KPI card wallpaper |
| Fintech | Review money and commit | Calm data-led, explicit status | Trust and consequence | Neon speculation cues |
| Healthcare | Understand and submit safely | Plain language, spacious guidance | Error cost is high | Ambiguous labels |
| Developer tool | Navigate complex state | Dense technical, stable panes | Expert scan and context | Oversized empty chrome |
| Commerce | Discover and choose product | Editorial content and strong product media | Product evidence drives decision | Fake urgency |
| Luxury | Build desire and trust | Restrained editorial, sparse material | Space and imagery signal craft | Badge clutter |
| Education | Learn and practice | Friendly structured progress | Motivation plus comprehension | Childish visual noise |
| Creative portfolio | Evaluate work | Work-first gallery, restrained frame | UI must not compete | Decorative interface |

Cost of wrong density is real. Sparse expert UI forces scrolling and hides comparison. Dense
novice UI overwhelms and raises error rate. Choose one density mode per surface, not per card.

## Information architecture and density

| Need | Layout | Why it works | Tradeoff |
|---|---|---|---|
| Manage many objects | List-detail | Selection and context stay visible | Needs mobile route strategy |
| Explore a dataset | Filter rail, table, detail | Supports query, scan, evidence | More UI literacy required |
| Complete bounded task | Single focused form | Reduces context switching | Less parallel visibility |
| Explain value | Narrative landing page | Builds claim through proof | Poor for dense comparison |
| Monitor operations | Overview, exception queue, drill-down | Puts action before vanity metric | Requires state model |

Use Bloomberg-terminal principle for expert density: compact regions need stable purpose, strong
alignment, visible status, and learned repeatability. Density without hierarchy is clutter.
Use Linear principle for modern software: reduce chrome, preserve state, make high-frequency
actions fast. Do not copy its exact dark treatment or hidden-command assumptions.

## Typography starting systems

| Product voice | Display | Text | Adaptation rule |
|---|---|---|---|
| Utilitarian | Inter or IBM Plex Sans | Same family | Use weight and spacing before another face |
| Editorial | Fraunces or Source Serif | Source Serif or sans | Keep body measure 45-75ch |
| Technical | Space Grotesk | Inter plus mono for data | Mono only for code and aligned values |
| Calm public service | Public Sans | Public Sans | Favor plain labels and open spacing |
| Luxury | High-contrast serif | Neutral sans | Use restraint, not tiny low-contrast text |

Body is 16px minimum with 1.45-1.7 line-height. Display tracking can be -0.03em to -0.01em;
all-caps labels need 0.06-0.12em. Use tabular figures for values users compare. Two families
maximum. Optical pairing means compare x-height, width, weight, and punctuation before judging
font names. A bold display with frail body sans looks accidental.

## Palette starting systems

Start from semantic roles, then adapt to brand. Never treat preset hex values as identity.

| Context | Neutral temperature | Accent approach | Status emphasis |
|---|---|---|---|
| SaaS | cool or neutral | one dependable action hue | restrained |
| Finance | neutral-cool | low-chroma blue or teal | high clarity |
| Healthcare | soft cool | calm blue or green | explicit labels |
| Luxury | warm neutral | sparse metallic or deep accent | minimal |
| Gaming | dark chromatic | high contrast accent | energetic but readable |
| Editorial | paper-warm | one ink or accent color | content-led |

Use OKLCH tokens and test contrast in every state. Large fills need lower chroma than buttons.
Dark mode is separate role design, not inverted light mode. Do not use red-green alone for
status or chart categories; pair color with text, shape, icon, or pattern.

## Chart selection

Each chart answers one question. If chart lacks question, comparison, and next decision, remove it.

| Question | Chart | Rule | Accessibility |
|---|---|---|---|
| Which is larger? | Bar | Start quantitative axis at zero | Direct label important values |
| What changed over time? | Line | Time increases left to right | Do not rely only on series color |
| What composes total? | Stacked bar | Keep categories limited and ordered | Label total and segments |
| How does distribution look? | Histogram | Use meaningful bins | Explain range and count |
| Do two values relate? | Scatter | Show scales and outliers | Provide text summary |
| How does one value vary? | KPI plus sparkline | Show baseline and period | Do not hide trend in color |

Use donut only for <=5 parts, obvious total, and rough composition. It is weak for comparison.
Avoid pie for many values, rainbow heatmaps for ordered data, and dual axes unless relationship
is explicit and axes cannot mislead. Give chart title a conclusion or question, not generic
"Revenue." Provide data table or downloadable alternative for complex visual data.

## Pattern catalog

Apple HIG works because direct manipulation, clear hierarchy, and content-first material reduce
surprise. Material 3 works because roles, states, and component semantics create consistency.
Stripe pages work because sequencing, whitespace, and evidence lower trust friction. Borrow
principles. Blind copying makes a product look like an imitation with wrong task assumptions.

## Anti-pattern library

Reject these as default AI UI:

- Centered giant headline, two equal CTA buttons, gradient blob, fake logo row, invented metrics.
- Every section boxed in identical rounded white card with soft shadow.
- Purple-blue gradient text, glass panel, bento grid, glow, pill tag, and mesh background together.
- Dashboard composed of isolated KPI cards with no filters, comparison, or drill-down.
- Tiny pale metadata treated as usable body text.
- Random avatar stacks, emoji icons, stock illustration, or decoration unrelated to product.
- Hover lift that moves target, animation that delays reading, motion without state meaning.
- Palette selected from industry stereotype instead of product brand and semantic roles.

Concrete alternatives: use a real product view, one meaningful before-and-after statistic with
source, a data-specific visual, a typographic index, or a product-derived material detail.

## Implementation recipes

```tsx
<section className="grid gap-6 lg:grid-cols-[minmax(0,1fr)_20rem]">
  <div className="min-w-0 space-y-6">
    <h1 className="text-3xl font-semibold tracking-[-0.02em]">Exception queue</h1>
    <ExceptionTable />
  </div>
  <aside className="rounded-lg border border-border bg-surface p-4">
    <QueueFilters />
  </aside>
</section>
```

Use `minmax(0, 1fr)` and `min-w-0` in app layouts. Use semantic tokens for canvas, surface,
text, muted text, action, focus, and status. Use 4px spacing increments and 16px body text.

## Failure modes experts catch

- Product category dictates visual stereotype instead of user task.
- "Premium" produces inaccessible low contrast and giant whitespace.
- Dense chart page lacks hierarchy between alert, context, and analysis.
- Visual style changes across empty, error, and loading states.
- Mobile hides scope, current location, or primary action.
- Typography scale has no metadata role, so every label competes with content.
- Chart color palette collapses in grayscale or deuteranopia.

## Critique rubric

Reject design if:

1. Design thesis cannot name user, job, density, voice, and product-derived signature detail.
2. Product type, task frequency, and risk do not explain layout choice.
3. Visual treatment could belong to any generated startup screenshot.
4. Data display does not answer a decision question or support accessible reading.
5. Typography, color, spacing, radius, and shadows lack a coherent role system.
6. Mobile transformation breaks wayfinding, scope, or task completion.
7. Removing decorative effects would improve hierarchy. If yes, remove them.

## Refs

- https://developer.apple.com/design/human-interface-guidelines/
- https://m3.material.io/
- https://www.w3.org/WAI/WCAG22/quickref/
- https://www.edwardtufte.com/

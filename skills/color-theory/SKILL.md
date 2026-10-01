---
name: color-theory
description: >-
  Expert UI color systems: OKLCH palettes, perceptual contrast, semantic token architecture,
  dark themes, status systems, color-blind-safe data display, and accessible implementation.
  Use for palettes, theming, contrast remediation, brand color, charts, and state semantics.
---

# Color Theory - Expert

## Mental model

Color is a constrained signaling system. Its first job is hierarchy, state, and legibility;
brand expression comes after those jobs work. A senior designer does not ask "what hex looks
good?" They ask which semantic role needs distinction, against which surface, under which
lighting, for which vision and input conditions.

Every color decision has three dimensions:

- Lightness: perceived brightness and primary driver of hierarchy.
- Chroma: perceived intensity and primary driver of emphasis or fatigue.
- Hue: category and brand association, weakest driver of accessible distinction.

Use OKLCH for palette construction. Its L axis better follows human perception than HSL
lightness, and equal L steps produce more even apparent changes. Final CSS can use `oklch()`,
with sRGB fallback only where browser support requires it.

## Token architecture

Build one direction only:

```
primitive scale -> semantic role -> component token
blue-600       -> action-primary -> button-primary-background
gray-950       -> text-strong    -> dialog-title-color
red-600        -> status-danger  -> field-error-icon-color
```

Primitives describe material. Semantic tokens describe intent. Component tokens describe local
use. Components must never reach directly for `blue-600`; otherwise changing brand, theme, or
meaning becomes a search-and-replace bug.

```css
:root {
  --blue-600: oklch(0.56 0.19 258);
  --gray-950: oklch(0.18 0.01 260);
  --surface-canvas: oklch(0.985 0.003 260);
  --surface-raised: oklch(1 0 0);
  --text-strong: var(--gray-950);
  --action-primary: var(--blue-600);
  --button-primary-background: var(--action-primary);
}
```

## Palette construction

Start with neutral scale first. Most interface pixels are neutral, so neutral temperature and
lightness determine whether brand color feels coherent.

- Create 9-12 neutral steps from near-white to near-black.
- Use small chroma, usually 0.005-0.02, to make neutral feel warm, cool, or slate.
- Keep body text near L 0.2-0.3 on light surfaces and L 0.85-0.93 on dark surfaces.
- Use borders with enough L difference to separate adjacent surfaces, not as decoration.
- Keep large fills lower chroma than small accents. Saturated large areas tire eyes quickly.

For brand scale, first choose usable midtone: a color that can meet 4.5:1 with white or dark
text depending on intended button fill. Then build lighter tint, hover, pressed, and dark
variants around it. Hue may shift through the scale to avoid muddy or out-of-gamut extremes.

Chroma limits are context-dependent:

| Use | Typical chroma | Why |
|---|---:|---|
| Body text | 0-0.04 | High chroma text frays visually |
| Large background | 0-0.08 | Prevents color fatigue |
| Surface tint | 0.01-0.04 | Keeps hierarchy quiet |
| Button or small accent | 0.12-0.22 | Creates deliberate emphasis |
| Focus ring | 0.14-0.24 | Must survive adjacent colors |

These are starting ranges, not contrast guarantees. Gamut clipping and hue change results.

## Contrast and perceptual checks

WCAG 2.2 requires 4.5:1 for normal text and 3:1 for large text: 24px normal or 18.66px bold.
Non-text UI components, graphical objects, and focus indicators need 3:1 against adjacent
colors when visual identification is necessary.

Contrast ratio is calculated from relative luminance:

```
(L1 + 0.05) / (L2 + 0.05)
```

where L1 is lighter luminance. Use a tool; do not estimate from screenshots. APCA is useful
awareness because it accounts for polarity, size, and perceived readability better in many
cases, but WCAG 2.2 ratio remains the conformance requirement today.

Do not rely on passing a numeric check alone. Thin font weight, small size, glare, motion,
and adjacent color can make technically passing text hard to read.

## Dark mode is redesign

Dark mode is not light mode with inverted values. Invert produces radioactive accents, harsh
white text, and collapsed elevation. Re-evaluate every semantic role.

- Use near-black with slight hue, around L 0.12-0.18, rather than pure black for canvas.
- Use off-white, around L 0.88-0.94, for main text, not pure white body copy.
- Elevation can use incrementally lighter surfaces, border contrast, or restrained shadow.
- Lower brand chroma for large fills; bright light-mode blue can vibrate on dark surface.
- Test images, logos, charts, code blocks, overlays, and disabled state separately.

Semantic meaning must remain stable: danger remains danger, action remains action, selected
remains selected. Exact primitive values may change across themes.

## Status color systems

Status color is not a rainbow. Define semantic meaning and pair it with label, icon, position,
or shape. Never use red versus green alone.

| State | Meaning | Supporting signal |
|---|---|---|
| Success | Completed, safe, healthy | Checkmark plus "Complete" |
| Warning | Needs attention, can continue | Triangle plus consequence |
| Danger | Failed, blocked, destructive | Error icon plus recovery |
| Info | Neutral context | Label or information icon |
| Pending | In progress, unknown | Spinner or timestamp |

For deuteranopia, red-green distinction degrades. Separate status with lightness, icon shape,
and text. Prefer blue plus orange for categorical pairings where semantics allow. For charts,
direct labels or patterns must preserve reading in grayscale.

## Data visualization color

Use color to encode grouping after position, length, and label do most work. Limit categorical
palette to 6-8 reliably distinguishable colors. For more categories, group, filter, direct
label, or use patterns. Sequential scales need monotonic lightness. Diverging scales need a
meaningful midpoint, usually zero or target.

Never use a rainbow scale for ordered values. It creates false boundaries and nonuniform visual
importance. Never use red and green for profit/loss without sign labels and alternate markers.

## Named patterns

Material 3 uses role tokens and tonal palettes, which works because components express intent
rather than scattered colors. Apple HIG uses restrained color so content and direct action lead.
Bloomberg-style terminals use strong status contrast because scan speed and consequence outrank
ambient calm. Borrow the rationale, not the literal palette.

## Implementation recipes

```css
@media (prefers-color-scheme: dark) {
  :root {
    --surface-canvas: oklch(0.16 0.012 260);
    --surface-raised: oklch(0.21 0.012 260);
    --text-strong: oklch(0.92 0.01 260);
    --action-primary: oklch(0.72 0.14 258);
  }
}

.focusable:focus-visible {
  outline: 2px solid var(--focus-ring);
  outline-offset: 3px;
}
```

Test foreground and background pairs in default, hover, pressed, selected, disabled, error,
and focus states. Test focus ring against both element and surrounding surface.

## Failure modes experts catch

- "Muted" body text fails at small size on a tinted surface.
- Disabled state becomes unreadable instead of merely unavailable.
- Surface tint looks like selected state because semantic roles collide.
- Link, primary action, and success use same hue, erasing meaning.
- Dark theme keeps saturated warning and causes visual vibration.
- Chart legend colors pass but adjacent series cannot be separated.
- Brand gradient replaces hierarchy and obscures button affordance.
- Raw colors leak into components, preventing theme consistency.

## Critique rubric

Reject a palette if:

1. Semantic roles cannot be named without referencing a hex value.
2. Essential meaning is conveyed by hue alone.
3. Any required text or UI pair fails WCAG 2.2 contrast.
4. Dark theme was produced by inversion rather than role-by-role decisions.
5. Large surfaces use accent chroma without an attention reason.
6. Focus, selected, danger, and primary action can be confused.
7. Data remains unreadable in grayscale or deuteranopia simulation.

## Refs

- https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html
- https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html
- https://www.w3.org/TR/css-color-4/
- https://www.myndex.com/APCA/

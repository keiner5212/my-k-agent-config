---
name: ui-libraries
description: >-
  Expert selection of UI, design system, and motion libraries. Finds the design system a project
  already uses before recommending one, then chooses styling, primitives, component kits, motion
  engines, icons, and charts that fit the product and do not read as a generic template.
  Use when styling a project from scratch, choosing motion tooling, or auditing UI dependencies.
---

# UI Libraries - Expert

## Mental model

A library is a decision with a maintenance cost. Add one only when it earns its place in the
product's visual system. Two token systems, two motion engines, or a kit copied wholesale is
exactly what makes a page read as a generic template.

The order is fixed: reuse what the project has, extend it, and only then choose new dependencies.

## Step 1: find the design system in the tree

Search before recommending. A project usually has one of these:

| Where | What to look for |
|---|---|
| `package.json` | `tailwindcss`, `shadcn`, `@base-ui/react`, `@radix-ui/*`, `motion`, `gsap`, `lenis`, `lucide-react`, `recharts`, `@visx/*`, `@pandacss/dev`, `unocss`, `@vanilla-extract/css`, `styled-components`, `@emotion/*` |
| CSS and config | `globals.css`, `app.css`, `theme.css`, `tokens.css`, `tailwind.config.*`, `postcss.config.*`, `:root { --... }`, `@theme` blocks, `data-theme` attributes |
| TS/JS tokens | `tokens.ts`, `theme.ts`, `colors.ts`, style-dictionary config, `tailwind-merge` preset, `cva` variants |
| Components | `components/ui/`, `packages/ui/`, `design-system/`, `src/lib/ui/`, `styles/`, `src/styles/` |
| Fonts | `next/font`, `@fontsource/*`, `@font-face`, Google Fonts link, `public/fonts` |
| Docs | `storybook`, `*.stories.tsx`, `design/`, `styleguide/` |

Then answer three questions: which token names exist, which component set exists, and which
dependencies already ship motion. Report the file path for each. If tokens exist, the design
work is an extension of that system, never a new one beside it.

## Step 2: when the project has no design system

Choose one styling approach, one primitives set, one motion engine, one icon set. Then fill the
semantic token roles yourself. Versions verified 2026-11-24; re-check before shipping.

### Styling

| Option | Use when | Cost |
|---|---|---|
| Tailwind CSS `tailwindcss` 4.3.5 | The project already uses it, or the team wants utility speed with token theming | Config plus build step; class-heavy markup |
| CSS custom properties | Small surface, no build step, plain CSS ownership | No scoping; discipline required |
| CSS Modules | React app without a utility framework | Per-file naming discipline |
| vanilla-extract `@vanilla-extract/css` 1.21.3 | Type-safe tokens with zero runtime | Build step plus a second language |
| Panda CSS `@pandacss/dev` 2.6.0 | Build-time type-safe CSS-in-JS with a token pipeline | Build step; extra tooling |
| UnoCSS `unocss` 66.13.0 | Atomic CSS with on-demand generation | Smaller ecosystem than Tailwind |

### Primitives and behavior

| Option | Use when | Cost |
|---|---|---|
| Radix UI `radix-ui` 1.8.1 | Unstyled accessible primitives, own the styling | More markup per control |
| Base UI `@base-ui/react` 1.9.0 | Unstyled primitives with no house style; shadcn/ui now builds on it | Smaller ecosystem than Radix |
| shadcn/ui `shadcn` 4.23.0 | Copy the source into the repo and own it | You maintain the copied code; default theme reads generic |
| Ark UI `@ark-ui/react` 5.40.1 | State-machine components across React, Solid, Vue, Svelte | Fewer components than Radix |
| React Aria `react-aria-components` 1.22.1 | Accessibility-heavy forms, lists, and overlays | Heavier abstraction |

### Kits to copy from, not depend on

| Option | Use when | Cost |
|---|---|---|
| React Bits `reactbits.dev` | Animated text, backgrounds, and micro-interactions, installed through the shadcn registry | MIT plus Commons Clause; 215+ components, effect patterns are widely copied |
| Magic UI `magicui.design` | Animated buttons, marquees, shimmer borders | MIT with a paid pro tier |
| Cult UI `cult-ui.com` | Animated shadcn blocks | MIT; 150+ blocks |
| Motion Primitives `motion-primitives.com` | Small accessible animation primitives and section motion | Copy-paste only, the npm package is an inactive CLI |
| coss ui `coss.com/ui` | Base UI plus Tailwind registry, the successor to Origin UI | License unverified |
| Aceternity UI `ui.aceternity.com` | Section-level animated blocks | Paid tier, license unverified, and the highest risk of a generic AI look |

These are copy sources. The source lands in the repo and is restyled with the project tokens.
A copied component that still carries its own colors, radii, or fonts is a failure.

### Motion

| Option | Use when | Cost |
|---|---|---|
| Motion `motion` 14.14.0 | React state, layout, gesture, and scroll animation | Component API to learn |
| GSAP `gsap` 3.15.1 with ScrollTrigger and SplitText | Scroll-linked section storytelling, timelines, pinning, scrubbing | Imperative, needs cleanup; standard "no charge" license, not open source |
| React Spring `@react-spring/web` 10.1.2 | Physics-feeling springs and gestures | Smaller feature set |
| anime.js `animejs` 4.5.0 | Small DOM or SVG animation, framework agnostic | Not a layout system |
| Auto Animate `@formkit/auto-animate` 0.11.0 | Zero-config FLIP on list and layout changes | Small scope |
| Lenis `lenis` 1.3.28 | Smooth scrolling that GSAP or ScrollTrigger drives | Another dependency on the scroll path |
| Rive `@rive-app/react-canvas` 4.41.0 | Authored interactive vector motion | Authoring tool needed |
| Lottie `lottie-react` 3.1.2 | Render authored JSON animations | Authoring tool needed |

`framer-motion` still publishes in lockstep with `motion`. Use `motion`.

### Icons, charts, and 3D

Lucide `lucide-react` 1.79.0 (ISC) for a stroke set. Iconify `@iconify/react` 6.0.4 for many sets
behind one API. Recharts `recharts` 3.11.1 for standard charts, visx `@visx/*` 4.0.0 for
low-level D3 work, ECharts `echarts` 6.1.0 for many chart types. React Three Fiber
`@react-three/fiber` 9.8.2 with `@react-three/drei` 10.7.9 for 3D. Spline `@splinetool/react-spline`
4.2.0 to embed a scene. Sonner `sonner` 2.0.8 for toasts.

Known but stale or moved: Phosphor 2.1.10 (2025-05), Nivo 0.99.0 (2025-05), Headless UI
(2026-04), Tremor 3.x with paid blocks at `tremor.so`, Origin UI (dead, redirects to coss ui).
Do not put these in a fresh recommendation.

## Step 3: selection rules

1. One styling approach, one primitives set, one motion engine, one icon set.
2. A new dependency must answer four questions: what user-visible thing improves, what it costs
   in bundle and complexity, what it does to accessibility, and who maintains it.
3. Prefer copy-in source over a black-box dependency for a single effect.
4. Verify before recommending: the docs or release page, the current version, framework
   compatibility, the license, and the last release date. State the version you verified. An
   unverified recommendation is a guess.
5. Do not recommend a library the build cannot bundle: CSS-in-JS in a React Server Components
   tree, or a DOM-only animation library inside a server-rendered page.
6. Keep the list short. Six UI libraries read as a demo, not a product.

## Step 4: motion direction

| Surface | Motion | Engine |
|---|---|---|
| Hero entrance | Staggered reveal of headline, then supporting copy, then the action | Motion variants |
| Scroll sections | Pinned or scrubbed sequence, one idea per beat | GSAP ScrollTrigger |
| Section transitions | Fade or slide as the section enters the viewport | Motion `whileInView` |
| Lists and cards | Hover and press feedback, FLIP on reorder | Motion or Auto Animate |
| Modals and drawers | Origin-anchored open and close | Motion |
| Micro feedback | Toast, spinner, optimistic state | Sonner plus CSS transitions |
| Product detail | Authored vector or 3D moment tied to the product | Rive or React Three Fiber |

Rules:

- Motion explains cause and effect or spatial continuity. If it only decorates, cut it.
- Animate `transform` and `opacity`. Do not animate layout, `filter`, or large `blur`.
- Entrance 180-280ms ease-out. Exit 120-200ms ease-in. Press 80-140ms. Hover 120-180ms.
- Stagger siblings 20-50ms and cap the whole sequence at 300ms.
- Honor `prefers-reduced-motion`: remove travel, keep opacity feedback.
- Load the motion library only where it is used. A hero animation must not delay first paint.
- The page stays usable with JavaScript motion disabled.
- Never ship the generic tell set: looping gradient blob, parallax on every section, marquee of
  fake logos, text that retypes forever, scroll-jacking, smooth scroll as the default tell.

## Anti-generic rule

The signature detail must come from the product. A section that exists only to show a library
effect is the generic page. Copy one or two components from a kit, restyle them into the project
tokens, and make them carry product meaning. shadcn's default theme, Aceternity blocks, a GSAP
scroll-jack, and Lenis smooth scroll are each a strong generic signal on their own.

## Failure modes

- Two token systems in one app: a Tailwind theme and a CSS variable file that disagree.
- Motion and GSAP in the same surface, fighting over the same property.
- A copied component that keeps its own font, radius, and colors.
- A smooth-scroll library that breaks keyboard scrolling and focus scrolling.
- An animation library loaded in the root layout so the whole bundle waits on it.
- A recommendation of an unmaintained package because it was famous two years ago.
- A kit installed with its own theme instead of the project tokens.

## Critique rubric

Reject a library set when:

1. It duplicates a system the project already has.
2. A library has no stated user-visible reason.
3. Versions, license, and maintenance status were not verified.
4. Motion is decorative rather than explanatory.
5. More than one library owns the same concern.
6. The build, accessibility, or reduced-motion path breaks.
7. The result would look like the same page with a different palette.

## Refs

- https://reactbits.dev/
- https://motion.dev/docs/react
- https://gsap.com/docs/
- https://base-ui.com/react
- https://ui.shadcn.com/docs
- https://ark-ui.com/

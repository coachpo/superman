---
name: ui-ux-pro-max
description: Use when you need grounded UI/UX direction from the local searchable design dataset, with a design-system-first workflow for implementation or review
---

# UI / UX Pro Max

## Overview

Search the local UI/UX dataset, generate a design system, and turn the output into concrete design direction for implementation or review.

**Core principle:** Start with `--design-system`, then run narrower searches only to answer the specific questions that remain.

This skill is for design lookup and synthesis. It is not a substitute for product requirements, frontend implementation, or browser-based QA.

## OpenCode / OMO Compatibility

This skill governs UI/UX search workflow only. It does not override repository instructions, orchestration policy, delegation choices, verification policy, or implementation ownership.

In OMO-style workflows, use this skill to prepare design direction before visual implementation, or to review an existing UI against grounded guidance from the local dataset.

## When to Use

Use this skill when you need to:
- generate a starting design system from sparse product or style inputs
- choose grounded style, color, typography, landing, chart, icon, UX, or stack guidance
- adapt UI direction to a supported implementation stack
- persist a reusable design system for a multi-page workflow
- review UI output against common professional-quality rules before delivery

Do not use this skill when:
- the user already provided a fixed design spec and only wants implementation
- the needed domain or stack is not supported by the local tool
- the task requires live browser validation or real interaction testing; use the appropriate testing workflow for that

## Setup / Assumptions

Runtime requirement: Python 3 available in `PATH`.

Before relying on flags or examples, run the real helper first:

```bash
python3 ui-ux-pro-max/scripts/search.py --help
```

Use the repository path that actually exists:

```bash
python3 ui-ux-pro-max/scripts/search.py "<query>" ...
```

Do **not** use `skills/ui-ux-pro-max/...`; that path is not valid in this repository.

If the user does not specify a stack, default to `html-tailwind`.

## Workflow

### Step 1: Resolve the request shape

Extract only the inputs that materially affect the search:
- product type or surface: SaaS, dashboard, e-commerce, landing page, portfolio, service, etc.
- style keywords: minimal, premium, playful, dark, glassmorphism, editorial, etc.
- industry or context: fintech, healthcare, gaming, wellness, education, etc.
- implementation stack: use the user-provided stack, otherwise default to `html-tailwind`
- delivery mode: new design direction, design review, or page-specific override

Do not start by guessing colors or components from memory. Resolve the query first.

### Step 2: Generate the base design system

For open-ended UI work, start here:

```bash
python3 ui-ux-pro-max/scripts/search.py "<product> <industry> <style keywords>" --design-system --project-name "Project Name"
```

This is the default entrypoint because it aggregates product, style, color, landing, and typography guidance into one recommendation.

Use markdown output when you want the result to be copied into docs or handoff notes:

```bash
python3 ui-ux-pro-max/scripts/search.py "<query>" --design-system --project-name "Project Name" --format markdown
```

### Step 3: Persist only when the work will continue

If the design system needs to survive across sessions or support page-level overrides, persist it:

```bash
python3 ui-ux-pro-max/scripts/search.py "<query>" --design-system --persist --project-name "Project Name"
```

This writes to:
- `design-system/<project-slug>/MASTER.md`
- `design-system/<project-slug>/pages/`

For a page-specific override:

```bash
python3 ui-ux-pro-max/scripts/search.py "<query>" --design-system --persist --project-name "Project Name" --page "dashboard"
```

If you need the files somewhere else, provide `--output-dir`.

Use the persisted structure as intended:
1. Check `design-system/<project-slug>/pages/<page>.md` first.
2. If it exists, it overrides the master.
3. If it does not exist, follow `design-system/<project-slug>/MASTER.md`.

### Step 4: Expand only the open questions

After the base design system exists, use narrower searches only when something is still unresolved.

Domain search:

```bash
python3 ui-ux-pro-max/scripts/search.py "<keyword>" --domain <domain> --max-results <n>
```

Stack search:

```bash
python3 ui-ux-pro-max/scripts/search.py "<keyword>" --stack <stack>
```

Use these intentionally:
- `style` when you need more visual directions or effects
- `color` when palette confidence is low
- `typography` when the design system needs stronger font pairing support
- `landing` when section order or CTA structure is still unclear
- `chart` when data visualization choices matter
- `icons` when icon style or library choice needs grounding
- `ux`, `react`, or `web` when implementation quality, accessibility, or performance details matter
- `--stack` when framework-specific guidance is more important than cross-stack UI advice

### Step 5: Synthesize before you hand off

Do not dump raw search output and call the task done.

Turn the results into explicit direction:
- recommended pattern
- recommended style and effects
- recommended colors
- recommended typography
- anti-patterns to avoid
- stack-specific guidance that changes implementation decisions
- any review checklist items that still need manual verification

## Supported Domains

| Domain | Use For |
|--------|---------|
| `style` | UI styles, effects, implementation keywords |
| `color` | Product-aligned color palettes |
| `chart` | Chart type and visualization guidance |
| `landing` | Section order, CTA placement, conversion structure |
| `product` | Product-type recommendations |
| `ux` | Usability, accessibility, and interaction guidance |
| `typography` | Font pairings and typography mood |
| `icons` | Icon category, library, and usage patterns |
| `react` | React and Next-style performance guidance |
| `web` | Web interface and accessibility guidance |

## Supported Stacks

`html-tailwind`, `react`, `nextjs`, `astro`, `vue`, `nuxtjs`, `nuxt-ui`, `svelte`, `swiftui`, `react-native`, `flutter`, `shadcn`, `jetpack-compose`

## Command Patterns

### Base design system

```bash
python3 ui-ux-pro-max/scripts/search.py "fintech crypto dashboard" --design-system --project-name "Nova"
```

### Base design system as markdown

```bash
python3 ui-ux-pro-max/scripts/search.py "fintech crypto dashboard" --design-system --project-name "Nova" --format markdown
```

### Persisted design system

```bash
python3 ui-ux-pro-max/scripts/search.py "fintech crypto dashboard" --design-system --persist --project-name "Nova"
```

### Page-specific override

```bash
python3 ui-ux-pro-max/scripts/search.py "fintech crypto dashboard checkout" --design-system --persist --project-name "Nova" --page "Checkout"
```

### Targeted domain search

```bash
python3 ui-ux-pro-max/scripts/search.py "animation accessibility" --domain ux
```

### Stack-specific guidance

```bash
python3 ui-ux-pro-max/scripts/search.py "layout responsive form" --stack html-tailwind
```

### JSON output for automation

```bash
python3 ui-ux-pro-max/scripts/search.py "dashboard icons" --domain icons --json
```

## Example Workflow

User request: `Làm landing page cho dịch vụ chăm sóc da chuyên nghiệp`

Use the skill like this:
1. Resolve the query as `beauty spa wellness service elegant`.
2. Run the base design system command.
3. If the work will continue across pages, persist it with `--project-name`.
4. If animation or accessibility is still unclear, run `--domain ux`.
5. If implementation stack guidance is needed, run `--stack html-tailwind`.
6. Deliver a synthesized recommendation, not just the raw terminal output.

## Core Rules

1. Start open-ended UI work with `--design-system`.
2. Use only domains and stacks that the current CLI actually supports.
3. Default to `html-tailwind` when the user does not specify a stack.
4. Persist design systems only when ongoing work or page overrides justify it.
5. Use domain and stack searches to close specific gaps, not to replace synthesis.
6. Keep recommendations grounded in actual tool output and the user’s product context.
7. If the task moves from design direction into implementation or live QA, hand off to the appropriate workflow instead of pretending this skill covers it.

## Anti-Patterns

- Starting with random `--domain` searches when `--design-system` is the real entrypoint
- Copying stale paths like `skills/ui-ux-pro-max/...`
- Documenting unsupported domains or stacks
- Treating search matches as final recommendations without synthesis
- Giving generic UI advice disconnected from the tool output
- Ignoring accessibility, performance, or anti-pattern notes surfaced by the search results
- Expanding a design-direction task into implementation work when the user did not ask for that

## Common Rules for Professional UI

These are frequent failure modes worth checking during review.

### Icons and visual elements

| Rule | Do | Don't |
|------|----|-------|
| **No emoji icons** | Use SVG icons from a consistent library | Use emojis as product UI icons |
| **Stable hover states** | Prefer color, opacity, shadow, or border changes | Use hover transforms that shift layout |
| **Correct brand logos** | Verify official logo assets before use | Guess or improvise brand marks |
| **Consistent icon sizing** | Use a consistent viewBox and scale system | Mix random icon proportions |

### Interaction and cursor

| Rule | Do | Don't |
|------|----|-------|
| **Pointer for clickable surfaces** | Use `cursor-pointer` on interactive cards and tiles | Leave clickable surfaces with default cursor |
| **Visible hover feedback** | Show a clear state change | Hide interactivity until click |
| **Controlled motion** | Keep transitions smooth and short | Use abrupt or slow decorative motion |
| **Keyboard visibility** | Keep focus states visible | Remove focus affordances |

### Light and dark contrast

| Rule | Do | Don't |
|------|----|-------|
| **Readable light mode text** | Use dark enough text for body copy | Use washed-out gray body text |
| **Visible glass surfaces** | Increase opacity enough to read content | Use transparency so low the component disappears |
| **Visible borders** | Use borders that read in both themes | Reuse near-invisible dark-theme borders in light mode |
| **Theme testing** | Check both light and dark before delivery | Assume one theme will translate automatically |

### Layout and spacing

| Rule | Do | Don't |
|------|----|-------|
| **Floating element spacing** | Leave breathing room from viewport edges | Pin floating UI flush to the window |
| **Fixed element clearance** | Account for fixed nav height in page layout | Let content hide behind sticky or fixed UI |
| **Consistent container width** | Reuse a small set of width tokens | Mix unrelated container widths across sections |
| **Mobile containment** | Check for overflow and horizontal scroll | Assume desktop spacing collapses cleanly on mobile |

## Verification Before Delivery

Before claiming the design direction is ready:
- run the exact command or commands your recommendation depends on
- verify the domains, stacks, and flags you referenced are actually supported
- make sure the recommendation names a pattern, style, palette, typography direction, and anti-patterns to avoid
- if reviewing existing UI, check the common rules below instead of relying on taste alone
- if persistence is part of the workflow, verify the expected files were created in the expected project folder

## Pre-Delivery Checklist

### Search grounding
- [ ] I ran the commands I am citing
- [ ] The domain or stack I referenced is supported by the current CLI
- [ ] I synthesized the result into explicit design direction
- [ ] I called out anti-patterns, not just positive recommendations

### Visual quality
- [ ] No emojis used as UI icons
- [ ] Icons come from a consistent library
- [ ] Hover states do not create layout shift
- [ ] Theme colors and contrast are intentional

### Interaction and accessibility
- [ ] Clickable elements signal interactivity
- [ ] Focus states remain visible
- [ ] Color is not the only status indicator
- [ ] `prefers-reduced-motion` is respected where motion matters

### Layout
- [ ] No content hides behind fixed UI
- [ ] No horizontal scroll on mobile
- [ ] Responsive checks were considered for 375px, 768px, 1024px, and 1440px

## Stop Conditions

Stop and ask, or hand off, when:
- the request is too vague to form a meaningful query
- the needed domain or stack is not in `search.py --help`
- the tool output conflicts with the documented workflow
- multiple materially different queries still return unusable results
- the task requires implementation, browser testing, or live UI validation beyond search and synthesis

## Remember

- Use the real repo path
- Start with `--design-system`
- Narrow only where needed
- Synthesize, do not dump
- Stay inside supported domains and stacks
- Verify commands before claiming the guidance is ready

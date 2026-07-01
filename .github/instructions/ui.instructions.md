---
applyTo: "**/*.{ts,tsx,css,module.css}"
---

# Design System — CineFlow Studio

## Color Palette

All tokens defined in `tailwind.config.ts`. Use via Tailwind classes only.

| Token | Hex | Usage |
|---|---|---|
| `white` | #FFFFFF | Primary background |
| `surface` | #F7F7F9 | Secondary surfaces |
| `text-primary` | #1F2937 | Primary text |
| `text-secondary` | #6B7280 | Secondary text |
| `brand-blue` | #00A4EF | Primary action, focus |
| `brand-yellow` | #FFB900 | In progress, alert |
| `brand-green` | #7FBA00 | Completed, success |
| `brand-red` | #F25022 | Blocked, error |
| `border` | #E5E7EB | Lines, dividers |

## Objective Status — Text + Icon + Color

Never use color alone. Status must always combine text label + icon + semantic color.

| Status | Color | Icon (Lucide) | Label (pt-BR) |
|---|---|---|---|
| `not_started` | `text-secondary` | `Circle` | Não iniciado |
| `in_progress` | `brand-yellow` | `Clock` | Em andamento |
| `completed` | `brand-green` | `CheckCircle2` | Concluído |

## Responsiveness Strategy

- **Desktop**: 2-column grid (sidebar + detail panel).
- **Tablet** (768px–1024px): Tabs or collapsible panels.
- **Mobile** (< 768px): Single-column lists, cards, floating action bar.

Tailwind breakpoints: `sm` 640 / `md` 768 / `lg` 1024 / `xl` 1280.

## Required Base Components

All in `src/components/`:

- `Button` — primary, secondary, tertiary variants
- `Badge` — for status tags
- `Card` — content containers
- `Modal` — dialogs
- `Spinner` — loading states
- `ProgressBar` — area progress
- `Avatar` — user presence
- `Tag` — labels
- `EmptyState` — no data placeholders
- `FeedbackToast` — autosave, errors, success messages

## Accessibility (WCAG 2.2 AA)

- Minimum contrast ratio 4.5:1 for all text.
- `aria-label` mandatory on icon-only buttons.
- Visible focus ring on all interactive elements.
- Never rely on color alone to indicate state.
- Keyboard navigation must work without mouse.

## Shadows & Spacing

- Prefer subtle shadows. Use Tailwind defaults: `shadow-sm`, `shadow-md`.
- Base spacing unit: 4px (Tailwind default). Use `p-2`, `p-4`, `p-6`, etc.
- Generous whitespace around content for breathing room.

## Typography

- Font: Use system fonts via Tailwind (Segoe UI via browser fallback).
- Heading hierarchy: H1 (page title) → H2 (sections) → H3 (subsections).
- Line height: 1.5 for body text, 1.2 for headings.

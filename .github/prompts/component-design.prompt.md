# @component-design

## Purpose
Guide component design and implementation following CineFlow Studio design system.

## When to Use
- Creating new reusable components
- Designing UI elements (buttons, cards, forms, etc.)
- Ensuring consistent visual language across the app
- Building accessible, responsive components

## Context
You are building UI components for CineFlow Studio, a collaborative audiovisual pre-production platform.

**Design System Rules**:
- Use Tailwind CSS 3 for styling
- Use CSS Modules for isolated component styles
- Follow Microsoft color palette: Red (#F25022), Green (#7FBA00), Blue (#00A4EF), Yellow (#FFB900), Primary Blue (#0078D4)
- Ensure WCAG 2.2 AA compliance
- Never use color as sole state indicator — always combine with text + icon
- Responsiveness: Mobile (320px), Tablet (768px), Desktop (1024px+)

**Component Structure**:
```
src/components/
├── Button/
│   ├── Button.tsx          (main component)
│   ├── Button.module.css   (styles)
│   ├── Button.types.ts     (TypeScript interfaces)
│   └── Button.test.tsx     (tests)
```

**Checklist**:
- ✅ Component is PascalCase
- ✅ Props are typed (TypeScript interface)
- ✅ Tailwind classes used for layout/utilities
- ✅ Module.css used for complex styles
- ✅ Accessibility props included (aria-label, role, etc.)
- ✅ Responsive design tested
- ✅ Component has storybook story (if complex)

## Prompt Template

```
@component-design: Create a [COMPONENT_NAME] component that:
- Follows CineFlow design system (Tailwind + Microsoft palette)
- Is accessible (WCAG 2.2 AA)
- Works on mobile, tablet, and desktop
- Accepts the following props: [LIST_PROPS]
- Uses TypeScript for type safety
- Is fully tested with Vitest + React Testing Library
```

## Example

```
@component-design: Create a StatusBadge component that displays Objective status 
(not_started, in_progress, completed) with:
- Appropriate icon from Lucide React
- Microsoft color palette (Red for not_started, Yellow for in_progress, Green for completed)
- Text label next to icon
- Accessible to screen readers
- Works on all screen sizes
```

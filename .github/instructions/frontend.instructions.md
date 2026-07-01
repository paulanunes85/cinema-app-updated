
---
applyTo: "src/**/*.{ts,tsx}"
---

# Frontend Conventions — CineFlow Studio

## Feature Structure

```text
src/
  features/
    auth/           # demo login, profiles, useAuthStore
    project/        # project creation and configuration
    area/           # checklist, progress, area view
    objective/      # detail, status, decisions, docs, comments
    supervision/    # Director/AD consolidated view
    templates/      # editable templates
  components/       # shared UI components
  types/            # domain.ts, auth.ts
  lib/              # permissions.ts, logger.ts, api.ts, debounce.ts
  data/             # demoProfiles.ts, seedData.ts
  store/            # Zustand stores
```

## Component Pattern

```tsx
interface Props {
  // Define explicit props here.
}

export function ComponentName({}: Props) {
  return null
}
```

- Use PascalCase for components.
- Avoid default exports for feature components.
- Keep UI-only components free from business logic.
- Move reusable business behavior into hooks.

## Hooks

- Business hooks must use the `use` prefix.
- Use `useObjective`, `useAreaProgress`, `usePermissions`, and `useAutosave` for shared behavior.
- Autosave must debounce changes by 800ms.
- Do not duplicate permission logic inside components.

## React Query

- Use stable query keys:
  - `['project', projectId]`
  - `['area', areaId]`
  - `['objective', objectiveId]`
- Mutations should invalidate the nearest parent query after success.
- In the local MVP, query functions may read from local seed data or local storage.

## Forms

- Use React Hook Form with Zod for user input.
- Keep schemas in `<feature>/schemas.ts`.
- Show loading, saving, success, and error states when data changes.

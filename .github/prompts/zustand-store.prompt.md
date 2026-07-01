# @zustand-store

## Purpose
Guide Zustand store creation and management following CineFlow Studio patterns.

## When to Use
- Creating global state stores
- Managing authentication state
- Managing permissions state
- Managing presence state (real-time collaboration)
- Implementing devtools integration

## Context
You are building state stores for CineFlow Studio using Zustand.

**Canonical Types**:
All domain types are defined in `src/types/domain.ts`. ALWAYS import from this file:
- `Project`, `Area`, `Objective` (core entities)
- `Decision`, `ExternalDoc`, `Comment`, `Presence`, `Template`
- `ObjectiveStatus` (type: "not_started" | "in_progress" | "completed")

**Store Patterns**:
```typescript
// Store signature
interface StoreState {
  // state
  items: Entity[];
  loading: boolean;
  error: string | null;
  
  // actions
  addItem: (item: Entity) => void;
  updateItem: (id: string, changes: Partial<Entity>) => void;
  deleteItem: (id: string) => void;
}

export const useStore = create<StoreState>((set, get) => ({
  items: [],
  loading: false,
  error: null,
  
  addItem: (item) => set((state) => ({
    items: [...state.items, item],
  })),
  
  updateItem: (id, changes) => set((state) => ({
    items: state.items.map((item) =>
      item.id === id ? { ...item, ...changes } : item
    ),
  })),
  
  deleteItem: (id) => set((state) => ({
    items: state.items.filter((item) => item.id !== id),
  })),
}));
```

**Three Core Stores**:
1. **useProjectStore**: Project, Area, Objective management
2. **useAuthStore**: User identity, authentication state
3. **usePresenceStore**: Real-time presence/collaboration (users viewing same objective)

**Best Practices**:
- Import types from `src/types/domain.ts` (never redefine locally)
- Use Zod for runtime validation if needed
- Keep stores focused (single responsibility)
- Always include error and loading states
- Use devtools for debugging

## Prompt Template

```
@zustand-store: Create a store for [DOMAIN] that:
- Manages [ENTITIES] from src/types/domain.ts
- Includes actions: [LIST_ACTIONS]
- Handles loading/error states
- Uses TypeScript strictly (no any)
- Integrates with TanStack Query for server sync
```

## Example

```
@zustand-store: Create useObjectiveStore that manages Objective entities with:
- State: objectives: Objective[], selectedId: string | null, loading: boolean, error: string | null
- Actions: loadObjectives(), selectObjective(id), updateObjective(id, changes), deleteObjective(id)
- Import Objective type from src/types/domain.ts
- Follow CineFlow patterns for mutation handling
```

# React + TypeScript Instructions

**Applies to**: `src/**/*.{ts,tsx}`

This instruction extends `.github/copilot-instructions.md` with detailed patterns for React component development.

---

## Component File Structure

Each feature component lives in its own folder:

```
src/features/ObjectiveDetail/
├── ObjectiveDetail.tsx           ← Main component (default export)
├── ObjectiveDetail.module.css    ← Isolated styles (CSS Modules)
├── ObjectiveDetail.types.ts      ← TypeScript interfaces/types
├── ObjectiveDetail.test.tsx      ← Vitest + RTL tests
├── hooks/
│   ├── useObjectiveData.ts       ← Data fetching hook
│   └── useObjectiveForm.ts       ← Form/state hook
├── components/
│   ├── ChecklistItem.tsx
│   ├── DecisionCard.tsx
│   └── CommentThread.tsx
└── utils/
    └── formatObjective.ts        ← Utility functions
```

**Shared components** (not feature-specific):
```
src/components/
├── Button/
├── Card/
├── Form/
├── Modal/
├── StatusBadge/
└── Spinner/
```

---

## Component Pattern

### Basic Component Template

```typescript
// ObjectiveDetail.tsx
import { FC, useCallback } from 'react';
import { Objective } from 'src/types/domain';
import { useObjectiveData } from './hooks/useObjectiveData';
import styles from './ObjectiveDetail.module.css';
import type { ObjectiveDetailProps } from './ObjectiveDetail.types';

/**
 * ObjectiveDetail displays full objective details with editing,
 * comments, and decision tracking.
 * 
 * Features:
 * - Real-time autosave (800ms debounce)
 * - Checklist management
 * - Comment thread
 * - Decision tracking
 */
export const ObjectiveDetail: FC<ObjectiveDetailProps> = ({ objectiveId }) => {
  const { objective, loading, error } = useObjectiveData(objectiveId);
  
  const handleSave = useCallback((updates: Partial<Objective>) => {
    // Handle save
  }, [objectiveId]);
  
  if (loading) return <Spinner />;
  if (error) return <ErrorBoundary error={error} />;
  if (!objective) return <EmptyState />;
  
  return (
    <div className={styles.container}>
      {/* Component JSX */}
    </div>
  );
};

ObjectiveDetail.displayName = 'ObjectiveDetail';
```

### Types File

```typescript
// ObjectiveDetail.types.ts
import type { Objective } from 'src/types/domain';

export interface ObjectiveDetailProps {
  objectiveId: string;
}

export interface ObjectiveDetailState {
  editing: boolean;
  saveStatus: 'idle' | 'saving' | 'saved' | 'error';
}
```

### Styles File (CSS Modules)

```css
/* ObjectiveDetail.module.css */
.container {
  @apply flex flex-col gap-4 p-4;
}

.header {
  @apply flex items-center justify-between border-b pb-4;
}

.title {
  @apply text-xl font-semibold text-slate-900;
}

.actions {
  @apply flex gap-2;
}

/* Dark mode support */
.container {
  @apply dark:bg-slate-950 dark:text-slate-50;
}
```

---

## State Management (Zustand)

### Store Pattern

```typescript
// stores/useObjectiveStore.ts
import { create } from 'zustand';
import type { Objective } from 'src/types/domain';

interface ObjectiveState {
  objectives: Objective[];
  selectedId: string | null;
  loading: boolean;
  error: string | null;
  
  setObjectives: (objectives: Objective[]) => void;
  selectObjective: (id: string) => void;
  updateObjective: (id: string, changes: Partial<Objective>) => void;
  deleteObjective: (id: string) => void;
}

export const useObjectiveStore = create<ObjectiveState>((set) => ({
  objectives: [],
  selectedId: null,
  loading: false,
  error: null,
  
  setObjectives: (objectives) => set({ objectives }),
  
  selectObjective: (id) => set({ selectedId: id }),
  
  updateObjective: (id, changes) => set((state) => ({
    objectives: state.objectives.map((obj) =>
      obj.id === id ? { ...obj, ...changes } : obj
    ),
  })),
  
  deleteObjective: (id) => set((state) => ({
    objectives: state.objectives.filter((obj) => obj.id !== id),
    selectedId: state.selectedId === id ? null : state.selectedId,
  })),
}));
```

### Three Core Stores

1. **useProjectStore** — Project, Area, Objective CRUD
2. **useAuthStore** — User identity, authentication state
3. **usePresenceStore** — Real-time presence/collaboration

---

## Data Fetching (TanStack Query)

### Query Pattern

```typescript
// hooks/useObjectiveData.ts
import { useQuery } from '@tanstack/react-query';
import type { Objective } from 'src/types/domain';

const objectiveQueryKey = (id: string) => ['objectives', id];

export const useObjectiveData = (objectiveId: string) => {
  return useQuery({
    queryKey: objectiveQueryKey(objectiveId),
    queryFn: async () => {
      const response = await fetch(`/api/objectives/${objectiveId}`);
      if (!response.ok) throw new Error('Failed to fetch objective');
      return response.json() as Promise<Objective>;
    },
    staleTime: 5 * 60 * 1000, // 5 minutes
    gcTime: 10 * 60 * 1000,    // 10 minutes (formerly cacheTime)
  });
};
```

### Mutation Pattern

```typescript
// hooks/useUpdateObjective.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';
import type { Objective } from 'src/types/domain';

export const useUpdateObjective = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: async (data: { id: string; updates: Partial<Objective> }) => {
      const response = await fetch(`/api/objectives/${data.id}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data.updates),
      });
      
      if (!response.ok) throw new Error('Failed to update objective');
      return response.json() as Promise<Objective>;
    },
    
    onSuccess: (updated) => {
      queryClient.setQueryData(['objectives', updated.id], updated);
    },
  });
};
```

---

## Forms (React Hook Form + Zod)

### Form Pattern

```typescript
// hooks/useObjectiveForm.ts
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import type { Objective } from 'src/types/domain';

const objectiveSchema = z.object({
  title: z.string().min(1, 'Title is required').max(255),
  description: z.string().optional(),
  status: z.enum(['not_started', 'in_progress', 'completed']),
  priority: z.enum(['low', 'medium', 'high']).optional(),
});

type ObjectiveFormData = z.infer<typeof objectiveSchema>;

export const useObjectiveForm = (objective: Objective) => {
  return useForm<ObjectiveFormData>({
    resolver: zodResolver(objectiveSchema),
    defaultValues: {
      title: objective.title,
      description: objective.description,
      status: objective.status,
      priority: objective.priority,
    },
  });
};
```

---

## TypeScript Standards

### Import Ordering
```typescript
// 1. React + hooks
import { FC, useCallback, useEffect } from 'react';

// 2. External libraries
import { useQuery } from '@tanstack/react-query';
import { useForm } from 'react-hook-form';

// 3. Internal (absolute imports from src/)
import { useProjectStore } from 'src/stores/useProjectStore';
import type { Objective } from 'src/types/domain';
import { formatDate } from 'src/utils/date';

// 4. Local (relative imports)
import { ChecklistItem } from './components/ChecklistItem';
import styles from './ObjectiveDetail.module.css';
import type { ObjectiveDetailProps } from './ObjectiveDetail.types';
```

### Type Safety Rules

✅ **DO**:
- Always type component props with `interface ComponentProps`
- Use `FC<Props>` for functional components
- Import types from `src/types/domain.ts` (canonical location)
- Use union types for enums: `'pending' | 'loading' | 'success' | 'error'`
- Strict null checks: `value || defaultValue`

❌ **DON'T**:
- Use `any` anywhere
- Redefine types locally (import from src/types/domain.ts)
- Mix default and named exports in same file
- Use `React.FC` (use `FC` instead)
- Inline types without extracting to `.types.ts`

---

## Hooks Guidelines

### Custom Hook Pattern

```typescript
// hooks/useObjectiveForm.ts
export const useObjectiveForm = (initialData: Objective) => {
  const [isSaving, setIsSaving] = useState(false);
  const saveObjective = useUpdateObjective();
  
  const handleSave = useCallback(async (data: Partial<Objective>) => {
    setIsSaving(true);
    try {
      await saveObjective.mutateAsync({ 
        id: initialData.id, 
        updates: data 
      });
    } catch (error) {
      logger.error('Failed to save objective', error);
    } finally {
      setIsSaving(false);
    }
  }, [initialData.id, saveObjective]);
  
  return { handleSave, isSaving };
};
```

### Hook Naming
- `use*` prefix (required)
- `use*Data` — for fetching data hooks
- `use*Form` — for form state hooks
- `use*Store` — for Zustand store hooks

---

## Error Handling

### Component Error Boundary

```typescript
import { Component, ReactNode } from 'react';

interface ErrorBoundaryProps {
  children: ReactNode;
}

interface ErrorBoundaryState {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends Component<
  ErrorBoundaryProps,
  ErrorBoundaryState
> {
  constructor(props: ErrorBoundaryProps) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error) {
    logger.error('Error caught by boundary:', error);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }

    return this.props.children;
  }
}
```

### Query Error Handling

```typescript
const { data, error, isLoading } = useQuery({
  queryKey: ['objectives', id],
  queryFn: fetchObjective,
});

if (error) {
  return (
    <ErrorAlert 
      message="Failed to load objective"
      retry={() => refetch()}
    />
  );
}
```

---

## Performance Optimization

### Code Splitting
```typescript
// Lazy load route components
const ObjectiveDetail = lazy(() =>
  import('./pages/ObjectiveDetail').then(m => ({
    default: m.ObjectiveDetail
  }))
);
```

### Memoization
```typescript
// Memoize expensive components
export const ObjectiveCard = memo(
  ({ objective, onClick }: Props) => {
    return <div onClick={onClick}>{objective.title}</div>;
  },
  (prev, next) => prev.objective.id === next.objective.id
);
```

### useCallback for Event Handlers
```typescript
const handleDelete = useCallback(() => {
  deleteObjective(id);
}, [id, deleteObjective]);
```

---

## Logging

Always use the logger instead of console:

```typescript
import { logger } from 'src/lib/logger';

logger.debug('Component mounted', { componentName: 'ObjectiveDetail' });
logger.info('Objective saved', { objectiveId: id });
logger.error('Failed to save', { error: err.message });
logger.warn('Permission check failed', { userId, action: 'edit' });
```

---

## Accessibility (WCAG 2.2 AA)

### Semantic HTML
```typescript
// ✅ Good
<button onClick={handleSave} aria-label="Save objective">
  Save
</button>

// ❌ Bad
<div onClick={handleSave}>Save</div>
```

### ARIA Labels
```typescript
<input
  aria-label="Objective title"
  aria-required="true"
  aria-invalid={errors.title ? true : false}
/>
```

### Focus Management
```typescript
const inputRef = useRef<HTMLInputElement>(null);

useEffect(() => {
  inputRef.current?.focus();
}, []);
```

---

## Permission Checking

Always use the `usePermissions` hook:

```typescript
import { usePermissions } from 'src/hooks/usePermissions';

const { canEdit, canDelete } = usePermissions();

if (!canEdit(objectiveId)) {
  return <ReadOnlyView />;
}
```

Never inline permission logic. Never duplicate permission checks.

---

## Testing (See testing.instructions.md)

Every component must have tests. Use `@test-component` prompt for guidance.

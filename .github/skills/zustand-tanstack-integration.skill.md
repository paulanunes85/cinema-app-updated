# CineFlow Zustand + TanStack Query Integration Skill

## Purpose

Master the integration between Zustand stores and TanStack Query for optimal state management in CineFlow Studio.

## When to Use

- Synchronizing global state (Zustand) with server state (TanStack Query)
- Handling mutations that update both caches and stores
- Preventing state duplication between client and server
- Building efficient cache invalidation strategies
- Implementing optimistic updates with rollback

## Core Pattern

### Store + Query Integration

```typescript
// Store (Zustand) - Local/sync state
interface ProjectState {
  selectedProjectId: string | null;
  setSelectedProject: (id: string) => void;
}

// Query (TanStack Query) - Server state
const useProjectData = (projectId: string) => {
  return useQuery({
    queryKey: ['projects', projectId],
    queryFn: () => fetchProject(projectId),
    enabled: !!projectId,
  });
};

// Combined Hook
export const useProject = (projectId: string) => {
  const { data } = useProjectData(projectId);
  const { setSelectedProject } = useProjectStore();
  
  useEffect(() => {
    if (data?.id) setSelectedProject(data.id);
  }, [data?.id, setSelectedProject]);
  
  return data;
};
```

## Mutation + Store Update

```typescript
const useUpdateProject = () => {
  const queryClient = useQueryClient();
  const { selectedProjectId } = useProjectStore();
  
  return useMutation({
    mutationFn: (updates: Partial<Project>) =>
      updateProject(selectedProjectId!, updates),
    
    // Optimistic update
    onMutate: (updates) => {
      queryClient.setQueryData(
        ['projects', selectedProjectId],
        (old: Project) => ({ ...old, ...updates })
      );
    },
    
    // Success: update cache and store
    onSuccess: (updated) => {
      queryClient.setQueryData(['projects', updated.id], updated);
      // Store updates automatically via useEffect hook above
    },
    
    // Error: rollback
    onError: (error, updates) => {
      queryClient.invalidateQueries({ 
        queryKey: ['projects', selectedProjectId] 
      });
    },
  });
};
```

## Cache Invalidation Strategy

```typescript
// When to invalidate different query keys
const handleAreaCreated = (area: Area) => {
  queryClient.invalidateQueries({
    queryKey: ['projects', area.projectId, 'areas'],  // Specific
  });
};

const handlePermissionChanged = (projectId: string) => {
  queryClient.invalidateQueries({
    queryKey: ['projects', projectId],  // Project + children
  });
};

const handleUserLoggedOut = () => {
  queryClient.clear();  // Clear everything
};
```

## Store Responsibilities vs Query Responsibilities

| Responsibility | Store (Zustand) | Query (TanStack) |
|---|---|---|
| Selected/Active IDs | ✅ | ❌ |
| Filters/Sort | ✅ | ❌ |
| List data | ❌ | ✅ |
| User permissions | ✅ | ❌ |
| Form draft state | ✅ | ❌ |
| Server data | ❌ | ✅ |
| Caching | ❌ | ✅ |
| Syncing | ❌ | ✅ |

## Anti-Patterns

❌ **DON'T**:
- Store server data in both Zustand AND Query
- Forget to invalidate cache after mutations
- Use Query for UI state (selection, filters, etc.)
- Update store without updating query cache
- Duplicate types between store and query

✅ **DO**:
- Keep store for UI/sync state only
- Use Query for all server data
- Invalidate cache on mutations
- Use single source of truth per entity
- Sync store + cache on mutation success

## Cineflow-Specific: Three Stores

```typescript
// 1. useProjectStore - Navigation + Selection
interface ProjectState {
  selectedProjectId: string | null;
  selectedAreaId: string | null;
  selectedObjectiveId: string | null;
}

// 2. useAuthStore - Identity + Permissions
interface AuthState {
  userId: string;
  userRole: 'viewer' | 'editor' | 'manager';
  canEdit: (resourceId: string) => boolean;
}

// 3. usePresenceStore - Real-time Collaboration
interface PresenceState {
  activeUsers: Presence[];
  userCursors: Record<string, CursorPosition>;
}

// All THREE use Query for their data
const useProjects = () => useQuery({...}); // Fetch projects
const useCurrentUser = () => useQuery({...}); // Fetch user + permissions
const usePresence = (objectiveId: string) => useQuery({...}); // Poll active users
```

## See Also

- `@zustand-store` prompt — Store creation template
- `.github/instructions/react-typescript.instructions.md` — Store patterns
- TanStack Query docs — Cache management

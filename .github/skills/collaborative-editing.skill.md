# CineFlow Collaborative Editing Skill

## Purpose
Build real-time collaborative editing patterns using WebSockets, presence tracking, and optimistic updates.

## When to Use
- Implementing real-time user presence (who's viewing this objective?)
- Building collaborative cursors/annotations
- Synchronizing state across multiple clients
- Handling conflict resolution for concurrent edits
- Implementing presence indicators (active users, last seen, etc.)

## Core Concepts

### Presence Tracking
```typescript
// usePresenceStore - Track who's editing
interface PresenceState {
  activeUsers: Presence[];  // Users viewing this objective
  lastActivity: Record<string, number>;  // userId -> timestamp
  
  registerPresence: (userId: string, objectiveId: string) => void;
  unregisterPresence: (userId: string, objectiveId: string) => void;
}
```

### Optimistic Updates
```typescript
// Save immediately on client, sync in background
const handleUpdate = async (changes: Partial<Objective>) => {
  // 1. Update local store immediately (optimistic)
  setObjective({ ...objective, ...changes });
  
  // 2. Send to server in background
  const result = await updateObjectiveMutation.mutateAsync(changes);
  
  // 3. Handle conflict if server returned different state
  if (result.version > objective.version) {
    setObjective(result);  // Accept server version
  }
};
```

### Real-time Sync with TanStack Query
```typescript
// Invalidate queries when presence changes
const { activeUsers } = usePresenceStore();

useEffect(() => {
  // Refetch when new user joins
  queryClient.invalidateQueries({ 
    queryKey: ['objectives', objectiveId, 'comments'] 
  });
}, [activeUsers.length]);
```

## Best Practices

✅ **DO**:
- Broadcast presence changes via WebSocket
- Use optimistic updates for instant feedback
- Handle network failures gracefully
- Debounce presence updates (100-200ms)
- Clear presence on disconnect
- Use version numbers for conflict detection
- Show which user made which edit

❌ **DON'T**:
- Block UI while syncing
- Send presence updates on every keystroke
- Assume network always succeeds
- Share user presence across unrelated features
- Forget to cleanup on unmount

## Pattern: Real-time Comments

```typescript
// Comments auto-sync when new user presence detected
const useCommentSync = (objectiveId: string) => {
  const { activeUsers } = usePresenceStore();
  const { data: comments, refetch } = useQuery({
    queryKey: ['objectives', objectiveId, 'comments'],
    queryFn: fetchComments,
    enabled: true,
  });

  // Refetch comments when user joins (they might have left comments)
  useEffect(() => {
    if (activeUsers.length > 0) {
      refetch();
    }
  }, [activeUsers.length, refetch]);

  return { comments };
};
```

## See Also
- `@autosave-form` prompt — Debounce patterns
- `.github/instructions/react-typescript.instructions.md` — State management
- `usePresenceStore` — Presence tracking store

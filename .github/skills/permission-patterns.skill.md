# CineFlow Permission & Access Control Skill

## Purpose

Implement permission checking patterns using `usePermissions` hook. Never duplicate permission logic.

## When to Use

- Conditionally rendering UI based on user role
- Protecting component interactions (enable/disable buttons)
- Guarding API calls and mutations
- Showing/hiding features based on permission level
- Implementing audit logging for permission checks

## Core Pattern

### Permission Hook

```typescript
// hooks/usePermissions.ts
export const usePermissions = () => {
  const { userId, userRole } = useAuthStore();
  const { selectedProjectId } = useProjectStore();

  return {
    canViewProject: (projectId: string) => {
      // Check user has access to this project
      return projectId === selectedProjectId;
    },
    
    canEditObjective: (objectiveId: string) => {
      // Check user role + resource
      return userRole === 'editor' || userRole === 'manager';
    },
    
    canDeleteObjective: (objectiveId: string) => {
      // Only managers can delete
      return userRole === 'manager';
    },
    
    canApproveObjective: (objectiveId: string) => {
      // Only managers can approve
      return userRole === 'manager';
    },
  };
};
```

### Component Usage

```typescript
const ObjectiveActions = ({ objectiveId }: Props) => {
  const { canEditObjective, canDeleteObjective, canApproveObjective } = usePermissions();

  const canEdit = canEditObjective(objectiveId);
  const canDelete = canDeleteObjective(objectiveId);
  const canApprove = canApproveObjective(objectiveId);

  return (
    <div className="flex gap-2">
      <button 
        disabled={!canEdit}
        title={!canEdit ? "You don't have permission to edit" : undefined}
        onClick={() => { /* edit */ }}
      >
        Edit
      </button>

      <button 
        disabled={!canApprove}
        title={!canApprove ? "Only managers can approve" : undefined}
        onClick={() => { /* approve */ }}
      >
        Approve
      </button>

      <button 
        disabled={!canDelete}
        title={!canDelete ? "Only managers can delete" : undefined}
        onClick={() => { /* delete */ }}
      >
        Delete
      </button>
    </div>
  );
};
```

## Permission Levels

```typescript
type UserRole = 'viewer' | 'editor' | 'manager';

// Viewer: Read-only access
const viewerPermissions = {
  canViewProject: true,
  canEditObjective: false,
  canDeleteObjective: false,
};

// Editor: Can edit their content
const editorPermissions = {
  canViewProject: true,
  canEditObjective: true,  // Own only
  canDeleteObjective: false,
};

// Manager: Full control
const managerPermissions = {
  canViewProject: true,
  canEditObjective: true,
  canDeleteObjective: true,
  canApproveObjective: true,
};
```

## UI Patterns

### Disable vs Hide

✅ **DO**: Disable + show reason
```typescript
<button disabled={!canEdit} title="You lack edit permission">
  Edit
</button>
```

❌ **DON'T**: Hide without explanation
```typescript
{canEdit && <button>Edit</button>}
```

### With Tooltip

```typescript
<Tooltip content="Only managers can delete">
  <button disabled={!canDelete}>Delete</button>
</Tooltip>
```

## Audit Logging

```typescript
const logPermissionCheck = (userId: string, action: string, resource: string, allowed: boolean) => {
  logger.info('Permission check', {
    userId,
    action,
    resource,
    allowed,
    timestamp: new Date().toISOString(),
  });
};

const canDelete = canDeleteObjective(objectiveId);
logPermissionCheck(userId, 'delete', objectiveId, canDelete);
```

## Golden Rules

✅ **DO**:
- Use `usePermissions()` hook centrally (one definition)
- Check permissions at component boundary
- Pass permission result as prop to children
- Disable UI, not hide (better UX)
- Show tooltips explaining why disabled
- Combine text + icon for status (accessibility)
- Log permission checks for audit
- Never duplicate permission logic

❌ **DON'T**:
- Check permissions inline (`if (user.role === 'admin')`)
- Hide UI without explanation
- Use color alone for disabled state
- Trust client-side checks alone
- Implement permissions in multiple places
- Forget to validate on server

## Server-Side Validation

Always validate on server:

```typescript
// API route
async function deleteObjective(req, res) {
  const { objectiveId } = req.params;
  const userId = req.user.id;

  // Client permissions are hints; verify server-side
  const canDelete = await checkUserPermission(userId, 'delete', objectiveId);
  if (!canDelete) {
    return res.status(403).json({ error: 'Permission denied' });
  }

  // Proceed with deletion
  await db.objectives.delete(objectiveId);
  res.json({ success: true });
}
```

## See Also

- `@permission-check` prompt — Quick template
- `useAuthStore` — User identity + role
- `.github/instructions/react-typescript.instructions.md` — Hook patterns

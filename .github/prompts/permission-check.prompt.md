# @permission-check

## Purpose
Guide permission checking and access control implementation using the CineFlow permission hook.

## When to Use
- Conditionally rendering features based on user permissions
- Protecting component interactions (enable/disable buttons)
- Guarding API calls
- Showing/hiding UI elements based on role
- Audit/logging permission violations

## Context
You are implementing permission checks in CineFlow Studio components.

**Permission Hook**:
```typescript
const usePermissions = () => {
  // Returns an object with permission checkers
  // Example: { canEdit, canDelete, canComment, canApprove }
}
```

**Usage Pattern**:
```typescript
import { usePermissions } from 'src/hooks/usePermissions';

export const ObjectiveEditor: React.FC<Props> = ({ objectiveId }) => {
  const { canEdit } = usePermissions();
  
  // Check permission before rendering edit UI
  if (!canEdit(objectiveId)) {
    return <ObjectiveViewer objectiveId={objectiveId} />;
  }
  
  return <EditForm objectiveId={objectiveId} />;
};
```

**Golden Rules**:
- ✅ Use `usePermissions()` hook — never inline permission logic
- ✅ Check permissions ONCE at component level
- ✅ Pass permission result as prop to child components
- ✅ Disable UI element if user lacks permission (button.disabled = true)
- ✅ Never HIDE from UI; DISABLE + show tooltip explaining why
- ❌ Never duplicate permission logic across components
- ❌ Never check permission inline (if (user.role === 'admin'))

**Component Pattern**:
```typescript
interface Props {
  objectiveId: string;
}

export const ObjectiveActions: React.FC<Props> = ({ objectiveId }) => {
  const { canEdit, canDelete, canApprove } = usePermissions();
  
  const hasEditPermission = canEdit(objectiveId);
  const hasDeletePermission = canDelete(objectiveId);
  const hasApprovePermission = canApprove(objectiveId);
  
  return (
    <div className="actions">
      <button 
        disabled={!hasEditPermission}
        title={!hasEditPermission ? "You don't have permission to edit" : undefined}
      >
        Edit
      </button>
      
      <button 
        disabled={!hasDeletePermission}
        title={!hasDeletePermission ? "You don't have permission to delete" : undefined}
      >
        Delete
      </button>
      
      <button 
        disabled={!hasApprovePermission}
        title={!hasApprovePermission ? "You don't have permission to approve" : undefined}
      >
        Approve
      </button>
    </div>
  );
};
```

## Prompt Template

```
@permission-check: Implement permission checks for [COMPONENT/FEATURE] that:
- Uses usePermissions() hook
- Checks: [LIST_PERMISSIONS]
- Disables UI elements (don't hide)
- Shows tooltips explaining why disabled
- Never duplicates permission logic
```

## Example

```
@permission-check: Add permission checks to AreaEditor component:
- Check canEditArea(areaId) before showing edit form
- Check canDeleteArea(areaId) before showing delete button
- Check canManageTeam(areaId) before showing team assignment
- Disable buttons with explanatory tooltips when user lacks permission
- Use usePermissions() hook (no inline role checks)
```

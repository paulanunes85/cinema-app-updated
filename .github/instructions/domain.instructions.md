---
applyTo: "**/*.{ts,tsx}"
---

# Domain Rules — CineFlow Studio

## Tipos canônicos (src/types/domain.ts)

```typescript
type ObjectiveStatus = 'not_started' | 'in_progress' | 'completed'
type UserRole = 'director' | 'ad' | 'area_chief' | 'member'

interface Project { id: string; name: string; cover?: string; description?: string; areas: Area[]; members: ProjectMember[] }
interface Area { id: string; projectId: string; name: string; color: string; objectives: Objective[] }
interface Objective { id: string; areaId: string; sharedAreaIds: string[]; title: string; status: ObjectiveStatus; assignees: string[]; dueDate?: string; phase?: string; decisions: Decision[]; docs: ExternalDoc[]; comments: Comment[]; presence: Presence[]; templateId?: string }
interface Decision { id: string; objectiveId: string; content: string; authorId: string; createdAt: string }
interface ExternalDoc { id: string; objectiveId: string; url: string; label: string; service: 'gdrive'|'figma'|'dropbox'|'onedrive'|'other'; authorId: string; updatedAt: string }
interface Comment { id: string; objectiveId: string; content: string; authorId: string; createdAt: string; reactions: Record<string,string[]> }
interface Presence { userId: string; objectiveId: string; since: string }
interface Template {
  id: string
  name: string
  guidance: string
  checklistItems: string[]
}
```

## Business Rules

- `in_progress` means the current user appears as an active collaborator.
- `completed` counts toward area progress.
- Area progress is calculated as `completedObjectives / totalObjectives * 100`.
- Director and AD can comment on any objective.
- Director and AD must not edit unfinished documents unless they are active collaborators.
- Permission logic must live in `src/lib/permissions.ts`.
- Demo profiles must live in `src/data/demoProfiles.ts`.

## Zustand Stores

- `useProjectStore`: active project, areas, and objectives.
- `useAuthStore`: current user, role, and assigned areas.
- `usePresenceStore`: local MVP presence state.
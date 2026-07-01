# @autosave-form

## Purpose
Implement autosave functionality with debounce and visual feedback for forms.

## When to Use
- Form fields that should save automatically on change
- Avoiding data loss in collaborative editing
- Providing real-time persistence feedback to users
- Reducing friction in data entry workflows

## Context
You are implementing autosave for CineFlow Studio forms using React Hook Form + TanStack Query.

**Autosave Pattern**:
```typescript
import { useForm } from 'react-hook-form';
import { useMutation } from '@tanstack/react-query';
import { useState, useCallback } from 'react';

interface FormData {
  title: string;
  description: string;
  // ... other fields
}

export const ObjectiveForm: React.FC<Props> = ({ objectiveId }) => {
  const { watch, control, formState: { isDirty } } = useForm<FormData>();
  const [saveStatus, setSaveStatus] = useState<'idle' | 'saving' | 'saved' | 'error'>('idle');
  
  const saveObjectiveMutation = useMutation({
    mutationFn: (data: FormData) => updateObjective(objectiveId, data),
    onSuccess: () => {
      setSaveStatus('saved');
      setTimeout(() => setSaveStatus('idle'), 2000);
    },
    onError: () => {
      setSaveStatus('error');
    },
  });
  
  // Watch form changes and autosave
  const watchedData = watch();
  
  const debouncedSave = useCallback(() => {
    if (isDirty) {
      setSaveStatus('saving');
      saveObjectiveMutation.mutate(watchedData);
    }
  }, [isDirty, watchedData, saveObjectiveMutation]);
  
  // Debounce: 800ms
  useEffect(() => {
    const timer = setTimeout(() => {
      debouncedSave();
    }, 800);
    
    return () => clearTimeout(timer);
  }, [watchedData, debouncedSave]);
  
  return (
    <form>
      <input {...register('title')} />
      <textarea {...register('description')} />
      
      {/* Visual Feedback */}
      <SaveIndicator status={saveStatus} />
    </form>
  );
};

// Indicator Component
interface SaveIndicatorProps {
  status: 'idle' | 'saving' | 'saved' | 'error';
}

const SaveIndicator: React.FC<SaveIndicatorProps> = ({ status }) => {
  switch (status) {
    case 'saving':
      return <span className="text-yellow-600">💾 Saving...</span>;
    case 'saved':
      return <span className="text-green-600">✓ Saved</span>;
    case 'error':
      return <span className="text-red-600">⚠ Error saving</span>;
    default:
      return null;
  }
};
```

**Visual Feedback States**:
- **Idle**: No indicator (or hidden)
- **Saving**: Loading spinner + "Saving..." text (Yellow #FFB900)
- **Saved**: Checkmark + "Saved" text (Green #7FBA00) — show for 2 seconds
- **Error**: Warning icon + "Error saving" text (Red #F25022)

**Golden Rules**:
- ✅ Debounce 800ms before each save
- ✅ Show loading state while saving
- ✅ Show success state (2-second auto-dismiss)
- ✅ Show error state with retry option
- ✅ Use Icon + Text (never color alone) for accessibility
- ✅ Disable form submission during save
- ✅ Persist unsaved state on browser close (warn user)
- ❌ Never auto-save on every keystroke (too noisy)
- ❌ Never show success without visual indication
- ❌ Never silently fail — always show error

**Accessibility**:
- Use aria-live="polite" for status updates
- Provide keyboard shortcuts (Ctrl+S) for manual save
- Ensure spinner is not blocking form interaction
- Color + Icon + Text for all states (WCAG 2.2 AA)

## Prompt Template

```
@autosave-form: Implement autosave for [FORM_COMPONENT] with:
- Debounce: 800ms
- Fields to autosave: [LIST_FIELDS]
- Save endpoint: [API_CALL]
- Visual feedback: saving, saved, error states
- Icons from Lucide React + text labels
- Accessibility compliant (aria-live, WCAG 2.2 AA)
```

## Example

```
@autosave-form: Add autosave to ObjectiveDetailForm:
- Watch title, description, status, priority fields
- Debounce 800ms before each save
- Call updateObjective(objectiveId, data)
- Show spinner + "Saving..." while saving
- Show checkmark + "Saved" for 2 seconds on success
- Show warning + "Error saving" on failure with retry button
- Use Lucide React icons (Loader2, CheckCircle, AlertCircle)
- Add aria-live="polite" to status element
- Disable Save button during autosave
- Warn user if form has unsaved changes on page leave
```

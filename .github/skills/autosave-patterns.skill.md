# CineFlow Autosave + Debounce Patterns Skill

## Purpose

Build reliable autosave workflows with 800ms debounce, error handling, and visual feedback.

## When to Use

- Adding autosave to forms and input fields
- Implementing periodic state synchronization
- Handling connection failures gracefully
- Providing real-time feedback to users
- Building collaborative editing that doesn't block UI

## Core Pattern

### 800ms Debounce with Visual Feedback

```typescript
const useAutoSave = (data: ObjectiveData, onSave: (data: ObjectiveData) => Promise<void>) => {
  const [status, setStatus] = useState<'idle' | 'saving' | 'saved' | 'error'>('idle');
  const timeoutRef = useRef<NodeJS.Timeout>();

  const save = useCallback(async () => {
    setStatus('saving');
    try {
      await onSave(data);
      setStatus('saved');
      setTimeout(() => setStatus('idle'), 2000);
    } catch (error) {
      setStatus('error');
      logger.error('Autosave failed', error);
    }
  }, [data, onSave]);

  // Debounce: cancel previous timeout and set new one
  useEffect(() => {
    clearTimeout(timeoutRef.current);
    timeoutRef.current = setTimeout(() => {
      save();
    }, 800); // 800ms debounce

    return () => clearTimeout(timeoutRef.current);
  }, [data, save]);

  return { status };
};
```

### Integration with React Hook Form

```typescript
const ObjectiveForm = ({ objectiveId }: Props) => {
  const { watch, formState: { isDirty } } = useForm<ObjectiveData>();
  const updateObjective = useUpdateObjective();
  const { status } = useAutoSave(watch(), async (data) => {
    if (isDirty) {
      await updateObjective.mutateAsync({ id: objectiveId, ...data });
    }
  });

  return (
    <>
      <form>{/* fields */}</form>
      <SaveIndicator status={status} />
    </>
  );
};
```

## Status Indicator Component

```typescript
interface SaveIndicatorProps {
  status: 'idle' | 'saving' | 'saved' | 'error';
}

export const SaveIndicator: React.FC<SaveIndicatorProps> = ({ status }) => {
  if (status === 'idle') return null;

  const config = {
    saving: {
      icon: Loader2,
      text: 'Saving...',
      color: 'text-yellow-600',
    },
    saved: {
      icon: CheckCircle,
      text: 'Saved',
      color: 'text-green-600',
    },
    error: {
      icon: AlertCircle,
      text: 'Error saving',
      color: 'text-red-600',
    },
  };

  const { icon: Icon, text, color } = config[status];

  return (
    <div className={`flex items-center gap-2 ${color}`} aria-live="polite" aria-atomic>
      <Icon size={16} />
      <span className="text-sm font-medium">{text}</span>
    </div>
  );
};
```

## Accessibility Requirements

✅ **WCAG 2.2 AA Compliance**:
- Use `aria-live="polite"` for status updates
- Combine icon + text (never color alone)
- High contrast colors: Green #7FBA00, Yellow #FFB900, Red #F25022
- Keyboard accessible (Tab to focus)
- No auto-dismiss without user confirmation

## Error Handling

```typescript
const handleSaveError = (error: Error) => {
  if (error.name === 'NetworkError') {
    // Retry automatically after 2s
    setTimeout(() => save(), 2000);
  } else if (error.name === 'ValidationError') {
    // Show validation errors
    setStatus('error');
  } else if (error.status === 409) {
    // Conflict - show merge dialog
    showMergeDialog();
  }
};
```

## Anti-Patterns

❌ **DON'T**:
- Save on every keystroke (too noisy)
- Block form interaction during save
- Silently fail without feedback
- Save without debounce
- Use only color for status (accessibility)
- Auto-dismiss success without timeout

✅ **DO**:
- Debounce at 800ms
- Allow continued editing while saving
- Show all status changes
- Use visual + textual feedback
- Auto-dismiss only after 2 seconds
- Retry on network failures
- Log errors for debugging

## See Also

- `@autosave-form` prompt — Quick template
- `.github/instructions/react-typescript.instructions.md` — useEffect patterns
- Testing guide — Testing async saves

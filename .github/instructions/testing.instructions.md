# Testing Instructions

**Applies to**: `**/*.test.{tsx,ts}`

This instruction extends `.github/copilot-instructions.md` with detailed testing patterns using Vitest + React Testing Library.

---

## Test File Structure

Every component must have corresponding tests:

```
src/features/ObjectiveDetail/
├── ObjectiveDetail.tsx
├── ObjectiveDetail.module.css
├── ObjectiveDetail.types.ts
└── ObjectiveDetail.test.tsx          ← Test file (same name + .test)

src/hooks/
├── useObjectiveData.ts
└── useObjectiveData.test.ts

src/utils/
├── formatObjective.ts
└── formatObjective.test.ts
```

---

## Test File Template

```typescript
// ObjectiveDetail.test.tsx
import { render, screen, within } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { vi } from 'vitest';
import { ObjectiveDetail } from './ObjectiveDetail';
import * as objectiveApi from 'src/api/objectives';

// Mock API calls
vi.mock('src/api/objectives');

describe('ObjectiveDetail', () => {
  // Setup before each test
  beforeEach(() => {
    vi.clearAllMocks();
  });

  // Happy path test
  it('should render objective title and description', async () => {
    const objective = {
      id: '1',
      title: 'Design login flow',
      description: 'Create wireframes for login',
      status: 'in_progress' as const,
    };

    vi.mocked(objectiveApi.getObjective).mockResolvedValue(objective);

    render(<ObjectiveDetail objectiveId="1" />);

    const heading = await screen.findByRole('heading', {
      name: /design login flow/i,
    });
    expect(heading).toBeInTheDocument();
  });

  // User interaction test
  it('should update objective title on input change', async () => {
    const user = userEvent.setup();
    const objective = { id: '1', title: 'Old title', status: 'not_started' };

    vi.mocked(objectiveApi.getObjective).mockResolvedValue(objective);
    vi.mocked(objectiveApi.updateObjective).mockResolvedValue({
      ...objective,
      title: 'New title',
    });

    render(<ObjectiveDetail objectiveId="1" />);

    const input = await screen.findByDisplayValue(/old title/i);
    await user.clear(input);
    await user.type(input, 'New title');

    // Wait for autosave (800ms debounce)
    expect(
      await screen.findByText(/saved/i)
    ).toBeInTheDocument();
  });

  // Error state test
  it('should show error message if fetch fails', async () => {
    vi.mocked(objectiveApi.getObjective).mockRejectedValue(
      new Error('Network error')
    );

    render(<ObjectiveDetail objectiveId="1" />);

    const error = await screen.findByText(/failed to load/i);
    expect(error).toBeInTheDocument();
  });

  // Permission test
  it('should disable edit button if user lacks permission', async () => {
    const objective = { id: '1', title: 'Test', status: 'not_started' };

    vi.mocked(objectiveApi.getObjective).mockResolvedValue(objective);
    vi.mock('src/hooks/usePermissions', () => ({
      usePermissions: () => ({ canEdit: () => false }),
    }));

    render(<ObjectiveDetail objectiveId="1" />);

    const button = await screen.findByRole('button', { name: /edit/i });
    expect(button).toBeDisabled();
  });

  // Edge case: empty state
  it('should show empty state when no objective found', async () => {
    vi.mocked(objectiveApi.getObjective).mockResolvedValue(null);

    render(<ObjectiveDetail objectiveId="999" />);

    const empty = await screen.findByText(/not found/i);
    expect(empty).toBeInTheDocument();
  });

  // Accessibility test
  it('should be accessible with keyboard navigation', async () => {
    const user = userEvent.setup();
    const objective = { id: '1', title: 'Test', status: 'not_started' };

    vi.mocked(objectiveApi.getObjective).mockResolvedValue(objective);

    render(<ObjectiveDetail objectiveId="1" />);

    const button = await screen.findByRole('button', { name: /save/i });

    // Tab to button
    await user.tab();
    expect(button).toHaveFocus();

    // Space to activate
    await user.keyboard(' ');
    expect(objectiveApi.updateObjective).toHaveBeenCalled();
  });
});
```

---

## Testing Patterns

### Query Priority (Use Most Accessible First)

```typescript
// ✅ BEST — By role (most accessible)
screen.getByRole('button', { name: /submit/i });
screen.getByRole('heading', { level: 1 });
screen.getByRole('textbox', { name: /title/i });

// ✅ GOOD — By label (form inputs)
screen.getByLabelText(/password/i);

// ✅ ACCEPTABLE — By text content
screen.getByText(/error message/i);

// ❌ LAST RESORT — By test ID (avoid when possible)
screen.getByTestId('submit-button');

// ❌ AVOID — Container queries (implementation detail)
screen.getByDisplayValue(/old value/i); // Only if no role available
```

### Async Patterns

```typescript
// Pattern 1: waitFor with query
await waitFor(() => {
  expect(screen.getByText(/loaded/i)).toBeInTheDocument();
});

// Pattern 2: findBy (preferred for async)
const element = await screen.findByText(/loaded/i);
expect(element).toBeInTheDocument();

// Pattern 3: waitForElementToBeRemoved
await waitForElementToBeRemoved(() =>
  screen.queryByTestId('loading-spinner')
);
```

### User Events

```typescript
const user = userEvent.setup();

// Typing
await user.type(screen.getByRole('textbox'), 'hello');

// Clicking
await user.click(screen.getByRole('button'));

// Selection
await user.selectOptions(
  screen.getByRole('combobox'),
  'option-value'
);

// Keyboard
await user.tab(); // Focus next element
await user.keyboard('{Enter}'); // Press Enter
await user.keyboard('{Shift>}a{/Shift}'); // Hold Shift + A
```

### Mocking

```typescript
// Mock module
vi.mock('src/api/objectives', () => ({
  getObjective: vi.fn(),
  updateObjective: vi.fn(),
}));

// Mock function
const mockFn = vi.fn().mockReturnValue(result);

// Mock implementation
vi.mocked(objectiveApi.getObjective).mockImplementation(async (id) => {
  if (id === '1') return { id: '1', title: 'Test' };
  throw new Error('Not found');
});

// Spy on function
vi.spyOn(console, 'log');
```

### Testing Hooks

```typescript
// Using renderHook
import { renderHook, act } from '@testing-library/react';
import { useObjectiveStore } from 'src/stores/useObjectiveStore';

it('should add objective', () => {
  const { result } = renderHook(() => useObjectiveStore());

  act(() => {
    result.current.setObjectives([{ id: '1', title: 'Test' }]);
  });

  expect(result.current.objectives).toHaveLength(1);
});
```

### Testing Forms

```typescript
it('should submit form with valid data', async () => {
  const user = userEvent.setup();
  const onSubmit = vi.fn();

  render(<ObjectiveForm onSubmit={onSubmit} />);

  const titleInput = screen.getByLabelText(/title/i);
  await user.type(titleInput, 'New objective');

  const submitButton = screen.getByRole('button', { name: /submit/i });
  await user.click(submitButton);

  expect(onSubmit).toHaveBeenCalledWith({
    title: 'New objective',
  });
});

// Test validation
it('should show error for empty title', async () => {
  const user = userEvent.setup();

  render(<ObjectiveForm onSubmit={vi.fn()} />);

  const submitButton = screen.getByRole('button', { name: /submit/i });
  await user.click(submitButton);

  const error = await screen.findByText(/title is required/i);
  expect(error).toBeInTheDocument();
});
```

---

## Common Test Scenarios

### 1. Component Renders
```typescript
it('should render without crashing', () => {
  render(<Component />);
  expect(screen.getByRole('heading')).toBeInTheDocument();
});
```

### 2. User Interactions
```typescript
it('should toggle checkbox on click', async () => {
  const user = userEvent.setup();
  render(<CheckboxComponent />);

  const checkbox = screen.getByRole('checkbox');
  expect(checkbox).not.toBeChecked();

  await user.click(checkbox);
  expect(checkbox).toBeChecked();
});
```

### 3. Async Data Loading
```typescript
it('should show loading then data', async () => {
  const { rerender } = render(<Component loading />);
  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  rerender(<Component loading={false} data={mockData} />);
  expect(await screen.findByText(/data/i)).toBeInTheDocument();
});
```

### 4. Error Handling
```typescript
it('should show error if API fails', async () => {
  mockFetch.mockRejectedValue(new Error('API error'));
  render(<Component />);

  const error = await screen.findByText(/error/i);
  expect(error).toBeInTheDocument();
});
```

### 5. Permission-based Rendering
```typescript
it('should show edit button only if user has permission', async () => {
  const { rerender } = render(
    <Component permissions={{ canEdit: false }} />
  );
  expect(screen.queryByRole('button', { name: /edit/i })).not.toBeInTheDocument();

  rerender(<Component permissions={{ canEdit: true }} />);
  expect(screen.getByRole('button', { name: /edit/i })).toBeInTheDocument();
});
```

### 6. Accessibility
```typescript
it('should be keyboard accessible', async () => {
  const user = userEvent.setup();
  render(<Component />);

  // Tab to button
  await user.tab();
  expect(screen.getByRole('button')).toHaveFocus();

  // Enter to activate
  await user.keyboard('{Enter}');
  expect(mockOnClick).toHaveBeenCalled();
});
```

---

## Test Organization (AAA Pattern)

Every test should follow Arrange → Act → Assert:

```typescript
it('should update objective status', async () => {
  // ARRANGE: Set up test data and mocks
  const user = userEvent.setup();
  const objective = { id: '1', status: 'not_started' };
  vi.mocked(getObjective).mockResolvedValue(objective);
  vi.mocked(updateObjective).mockResolvedValue({
    ...objective,
    status: 'in_progress',
  });

  // ACT: Perform user action
  render(<ObjectiveDetail objectiveId="1" />);
  const statusButton = await screen.findByRole('button', {
    name: /in progress/i,
  });
  await user.click(statusButton);

  // ASSERT: Verify result
  expect(updateObjective).toHaveBeenCalledWith('1', {
    status: 'in_progress',
  });
  expect(await screen.findByText(/saved/i)).toBeInTheDocument();
});
```

---

## Coverage Goals

Aim for:
- **Statements**: 80%+
- **Branches**: 75%+
- **Functions**: 80%+
- **Lines**: 80%+

Priority order:
1. User-facing functionality (happy path)
2. Error states
3. Edge cases
4. Permission checks
5. Accessibility

---

## Running Tests

```bash
# Run all tests
npm run test

# Run specific file
npm run test ObjectiveDetail

# Watch mode
npm run test:watch

# Coverage report
npm run test:coverage

# Debug mode
node --inspect-brk node_modules/vitest/vitest.mjs run
```

---

## Best Practices

✅ **DO**:
- Test user behavior, not implementation
- Use semantic queries (getByRole > getByLabelText > getByText)
- Mock API calls (never make real network requests)
- Test error states and edge cases
- Use userEvent for realistic interactions
- Keep tests focused (one assertion per test ideally)
- Use async/await for clarity
- Name tests descriptively ("should [action] when [condition]")
- Use beforeEach for common setup

❌ **DON'T**:
- Test internal state (test observable output)
- Use getByTestId unless necessary
- Over-mock (mock only external dependencies)
- Use shallow rendering (render full component tree)
- Test implementation details (refactoring breaks tests)
- Use setTimeout in tests (use waitFor/findBy)
- Write flaky tests (avoid randomness)
- Test third-party libraries (assume they work)
- Ignore accessibility (every test should be accessible)

---

## Debugging Tests

```typescript
// Print DOM structure
screen.debug();

// Print specific element
screen.debug(screen.getByRole('button'));

// Pause test execution
debugger;

// Verbose query output
screen.logTestingPlaygroundURL();
```

---

## See Also

- `@test-component` prompt — Quick template for new tests
- `.github/instructions/react-typescript.instructions.md` — Component patterns
- `.github/copilot-instructions.md` — Universal CineFlow rules

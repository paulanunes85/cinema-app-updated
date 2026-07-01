# @test-component

## Purpose
Guide comprehensive React component testing using Vitest + React Testing Library.

## When to Use
- Writing unit tests for React components
- Testing user interactions (clicks, form inputs, etc.)
- Testing async behavior (data fetching, timers)
- Testing accessibility features
- Ensuring component behavior matches requirements

## Context
You are testing React components for CineFlow Studio.

**Testing Stack**:
- **Test Runner**: Vitest
- **Component Testing**: React Testing Library
- **Assertions**: Vitest's built-in matchers
- **Mocking**: vi.mock(), vi.spyOn()
- **Async**: waitFor(), screen.findBy*()

**Test File Structure**:
```
src/features/Example/
├── Example.tsx
├── Example.module.css
├── Example.types.ts
└── Example.test.tsx  ← One test file per component
```

**Test Pattern**:
```typescript
import { render, screen, within } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Example } from './Example';

describe('Example', () => {
  it('should render heading', () => {
    render(<Example />);
    const heading = screen.getByRole('heading', { name: /example/i });
    expect(heading).toBeInTheDocument();
  });

  it('should handle user interaction', async () => {
    const user = userEvent.setup();
    render(<Example />);
    
    const button = screen.getByRole('button', { name: /submit/i });
    await user.click(button);
    
    const result = screen.getByText(/success/i);
    expect(result).toBeInTheDocument();
  });

  it('should load data', async () => {
    render(<Example />);
    
    const result = await screen.findByText(/loaded/i);
    expect(result).toBeInTheDocument();
  });
});
```

**Best Practices**:
- Test user behavior, not implementation details
- Use screen queries (getByRole, getByLabelText) instead of container queries
- Test accessibility (aria-labels, roles)
- Mock external dependencies (API calls, localStorage, etc.)
- Use waitFor() for async operations
- Test loading and error states
- Avoid testing internal state — test observable output

**Queries Priority**:
1. `getByRole()` — most accessible
2. `getByLabelText()` — form inputs
3. `getByText()` — text content
4. `getByTestId()` — last resort (avoid when possible)

## Prompt Template

```
@test-component: Write comprehensive tests for [COMPONENT_NAME] that:
- Test [LIST_USER_BEHAVIORS]
- Mock [DEPENDENCIES]
- Use React Testing Library best practices
- Cover happy path, error state, and edge cases
- Ensure accessibility is tested
- Include async/await patterns where needed
```

## Example

```
@test-component: Write tests for ObjectiveDetail component that:
- Renders objective title, description, and checklist items
- Allows user to toggle checklist items (checkbox click)
- Shows loading state while fetching comments
- Displays error message if fetch fails
- Handles autosave (debounce 800ms + visual feedback)
- Mock usePermissions() to test conditional rendering
- Test all status states (not_started, in_progress, completed)
```

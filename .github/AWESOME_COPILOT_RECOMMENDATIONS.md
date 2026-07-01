# CineFlow Studio — Awesome Copilot Recommendations

**Source**: [awesome-copilot](https://github.com/github/awesome-copilot) (Community-contributed agents, skills, instructions, prompts)

This document maps recommended resources from awesome-copilot that align with CineFlow Studio's tech stack and development workflow.

---

## 🤖 Recommended Agents

### 1. **expert-react-frontend-engineer** ⭐⭐⭐ (PRIORITY)
**Relevance**: High  
**Source**: `awesome-copilot/agents/expert-react-frontend-engineer.agent.md`  
**Purpose**: Deep expertise in React 18+, TypeScript, hooks, concurrent rendering, Server Components.

**Why**: This agent directly matches our React 18 + TypeScript 5 stack and can guide component design, state management (Zustand), and modern React patterns.

**Integration**:
```bash
# Copy to your project:
.github/agents/expert-react-frontend-engineer.agent.md
```

**When to use**: "As an expert React engineer, help me architect this component..." or "Review my Zustand store design..."

---

### 2. **repo-architect** ⭐⭐
**Relevance**: Medium-High  
**Source**: `awesome-copilot/agents/repo-architect.agent.md`  
**Purpose**: Guides project structure, folder organization, best practices.

**Why**: CineFlow Studio has a feature-based folder structure. This agent helps maintain consistency.

**Use cases**: Planning new features, validating folder structure, suggesting module boundaries.

---

## 🛠️ Recommended Skills

### 1. **javascript-typescript-jest** ⭐⭐⭐ (PRIORITY)
**Relevance**: High  
**Source**: `awesome-copilot/skills/javascript-typescript-jest/SKILL.md`  
**Stack**: Jest + Vitest + React Testing Library

**Coverage**:
- Unit test patterns for TypeScript
- Mocking strategies (jest.mock, jest.spyOn)
- React component testing (Testing Library)
- Async/await patterns
- Test organization and file structure

**Integration**: Already on your stack (Vitest + Testing Library). Load this skill when writing tests.

---

### 2. **typescript-upgrade** ⭐⭐
**Relevance**: Medium  
**Purpose**: TypeScript version upgrades, type safety improvements, strict mode migration.

**When relevant**: If you need to upgrade TypeScript 5 or enforce stricter type checking.

---

### 3. **refactor-plan** ⭐⭐
**Relevance**: Medium  
**Purpose**: Systematic refactoring guidance, code quality improvements.

**When relevant**: Large feature refactors or technical debt cleanup.

---

### 4. **typescript-migration** ⭐
**Relevance**: Low (only if adding existing JS libraries)  
**Purpose**: Migrate JavaScript code to TypeScript.

---

## 📋 Recommended Instructions

### 1. **React + TypeScript Best Practices**
**Relevance**: High  
**Coverage**:
- Component patterns (functional, hooks, composition)
- State management (Zustand rules)
- TypeScript strict mode
- Naming conventions
- File organization

**Action**: Create `.github/instructions/react-typescript.instructions.md` (similar structure to your existing instructions)

---

### 2. **Testing Patterns**
**Relevance**: High  
**Coverage**:
- Unit test structure
- Integration test guidelines
- Component testing with Testing Library
- Mock data patterns
- Test naming

**Reference**: Align with `javascript-typescript-jest` skill

---

### 3. **Design System & Accessibility**
**Relevance**: High  
**Coverage**:
- Tailwind CSS patterns
- CSS Modules for component isolation
- WCAG 2.2 AA compliance (already in your `ui.instructions.md`)
- Color contrast, keyboard navigation, ARIA labels

---

## 🎯 Recommended Prompts

Common prompts to add to your team's workflow:

### 1. **@component-design**
```
"@component-design: Design a reusable button component following CineFlow Studio design system (Tailwind CSS, Microsoft color palette, WCAG 2.2 AA compliance)."
```

### 2. **@zustand-store**
```
"@zustand-store: Create a Zustand store following CineFlow patterns, including TypeScript types from src/types/domain.ts."
```

### 3. **@test-component**
```
"@test-component: Write comprehensive tests for this React component using Vitest + React Testing Library, including user interactions and async updates."
```

### 4. **@permission-check**
```
"@permission-check: Implement permission checking using usePermissions() hook. Never duplicate permission logic inline."
```

### 5. **@autosave-form**
```
"@autosave-form: Add autosave with 800ms debounce and visual feedback (loading, saving, success, error) to this form."
```

---

## 📚 How to Use These Resources

### Step 1: Copy Agents
```bash
cd .github/
mkdir -p agents
# Copy from awesome-copilot:
# - expert-react-frontend-engineer.agent.md
# - repo-architect.agent.md
```

### Step 2: Reference Skills in Chat
When writing code:
- **"Use the javascript-typescript-jest skill for testing patterns."**
- **"Follow expert-react-frontend-engineer patterns for this component."**

### Step 3: Create Custom Prompts
Store in `.github/prompts/`:
- `component-design.prompt.md`
- `zustand-store.prompt.md`
- `test-component.prompt.md`
- `permission-check.prompt.md`
- `autosave-form.prompt.md`

### Step 4: Update Main Instructions
Link recommended skills to your main `copilot-instructions.md`:
```markdown
## Recommended Skills
- **javascript-typescript-jest**: Load when writing tests
- **expert-react-frontend-engineer**: Load for React architecture
- **refactor-plan**: Load for large refactors
```

---

## 🎓 Awesome Copilot Repository Stats

**Current collection** (as of search):
- 175+ community agents
- 208+ community skills
- 176+ community instructions
- 48+ plugins
- 7 agentic workflows
- 3 hooks

**Browse**: https://github.com/github/awesome-copilot

---

## 🔄 Integration Workflow

### Phase 0: Foundation ✅ COMPLETE

- ✅ Created main `copilot-instructions.md`
- ✅ Created contextual instructions (domain, frontend, ui, docker)
- ✅ Created AWESOME_COPILOT_RECOMMENDATIONS.md

### Phase 1: Agents & Skills 🔄 IN PROGRESS

- 📋 Copy `expert-react-frontend-engineer.agent.md` → `.github/agents/`
- 📋 Copy `repo-architect.agent.md` → `.github/agents/`
- 📋 Update skill references in `copilot-instructions.md`

### Phase 2: Custom Prompts ✅ COMPLETE

- ✅ Created `.github/prompts/component-design.prompt.md`
- ✅ Created `.github/prompts/zustand-store.prompt.md`
- ✅ Created `.github/prompts/test-component.prompt.md`
- ✅ Created `.github/prompts/permission-check.prompt.md`
- ✅ Created `.github/prompts/autosave-form.prompt.md`

### Phase 3: Extended Instructions ✅ COMPLETE

- ✅ Created `.github/instructions/react-typescript.instructions.md` (applyTo: src/**/*.{tsx,ts})
- ✅ Created `.github/instructions/testing.instructions.md` (applyTo: **/*.test.{tsx,ts})

---

## 📝 Success Criteria

- [ ] At least 2 agents copied from awesome-copilot
- [ ] At least 5 custom prompts created
- [ ] Skills referenced in main instructions
- [ ] Team can say: "@expert-react-engineer, review this component" and get context-aware help
- [ ] Test writing accelerated by jest skill + testing prompts

---

## 🔗 Quick Links

| Resource | URL |
|---|---|
| Awesome Copilot Repo | https://github.com/github/awesome-copilot |
| Agents Index | https://github.com/github/awesome-copilot/tree/main/agents |
| Skills Index | https://github.com/github/awesome-copilot/tree/main/skills |
| Instructions Index | https://github.com/github/awesome-copilot/tree/main/instructions |
| Prompts Index | https://github.com/github/awesome-copilot/tree/main/prompts |

---

**Last Updated**: 2026-07-01  
**Recommended by**: Copilot Planning Phase

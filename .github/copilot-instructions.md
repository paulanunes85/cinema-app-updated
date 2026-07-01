# CineFlow Studio — Copilot Instructions

## Project
Collaborative web platform for audiovisual pre-production.
Stack: React 18 + TypeScript 5 + Vite + Tailwind CSS + Zustand + React Query.
Environment: Local Docker Desktop, no mandatory SSO, no cloud in MVP.
All code language, comments, and docs: **English**. User-visible UI: **Portuguese (pt-BR)**.

## Fixed Stack — do not suggest alternatives
| Layer | Technology |
|---|---|
| UI | React 18 + TypeScript 5 |
| Build | Vite 5 |
| Styling | Tailwind CSS 3 (custom config) + CSS Modules for isolated components |
| Global state | Zustand |
| State server / cache | TanStack Query v5 |
| Routing | React Router v6 |
| Forms | React Hook Form + Zod |
| Icons | Lucide React |
| Testing | Vitest + Testing Library |
| Container | Docker + docker-compose |

## Domain Primitives
Project → Area → Objective (central unit).
Objective has: Checklist, Decision[], ExternalDoc[], Comment[], Presence[], Template?

Canonical types live in `src/types/domain.ts`. **Always import from this file, never redefine locally.**

## Universal Rules
- Components: PascalCase. Hooks: `use` prefix. Utils: camelCase. Types/Interfaces: PascalCase.
- Folder structure by feature in `src/features/<feature>/`. Shared components in `src/components/`.
- Never use `any`. Never use `console.log` in production — use `src/lib/logger.ts`.
- Autosave via 800ms debounce. Visual feedback mandatory: loading / saving / error / success.
- Objective status: `not_started` | `in_progress` | `completed`. Status never as sole visual cue — always text + icon.
- Permissions checked via `usePermissions()` hook, never inline with duplicated logic.
- Color never as sole state indicator (WCAG 2.2 AA compliance).

## What NOT to do
- Do not install libraries outside the stack without explicit instruction.
- Do not create React contexts for state already managed by Zustand.
- Do not duplicate types already defined in `src/types/`.
- Do not host files — the platform stores only metadata and links.
- Do not add mandatory external service dependencies in the MVP.

## 🤖 Recommended Agents

Load these agents for domain expertise:

### expert-react-frontend-engineer (Priority ⭐⭐⭐)
**When**: Building React components, optimizing performance, reviewing architecture
**Expertise**: React 18+, hooks, TypeScript, state management, Zustand, TanStack Query
**Location**: `.github/agents/expert-react-frontend-engineer.agent.md`

### repo-architect (Priority ⭐⭐)
**When**: Planning features, validating folder structure, architectural decisions
**Expertise**: Project structure, patterns, validation, scaffolding
**Location**: `.github/agents/repo-architect.agent.md`

## 🛠️ Recommended Skills

### CineFlow Custom Skills (Project-Specific)

These skills are tailored to CineFlow Studio architecture and patterns:

1. **collaborative-editing** — Real-time presence, optimistic updates, conflict resolution
   - Load when: Implementing multi-user editing, presence tracking
   - Location: `.github/skills/collaborative-editing.skill.md`

2. **zustand-tanstack-integration** — Master Zustand + TanStack Query sync
   - Load when: Building stores, queries, mutations, cache invalidation
   - Location: `.github/skills/zustand-tanstack-integration.skill.md`

3. **autosave-patterns** — 800ms debounce, error handling, visual feedback
   - Load when: Implementing autosave forms
   - Location: `.github/skills/autosave-patterns.skill.md`

4. **permission-patterns** — `usePermissions` hook, access control
   - Load when: Adding role-based UI controls and permission checks
   - Location: `.github/skills/permission-patterns.skill.md`

### Community Skills (Awesome Copilot)

Integrated community skills complement the custom ones:

- **javascript-typescript-jest** — Vitest + React Testing Library patterns
- **refactor-plan** — Systematic refactoring guidance  
- **typescript-upgrade** — TS version upgrades and strict mode

See `.github/AWESOME_COPILOT_RECOMMENDATIONS.md` for full skill matrix and integration details.

## 📋 Contextual Instructions

Auto-loaded based on file type:

- `.github/instructions/domain.instructions.md` — applyTo: `**/*.{ts,tsx}`
- `.github/instructions/frontend.instructions.md` — Feature structure, components
- `.github/instructions/ui.instructions.md` — applyTo: `**/*.{ts,tsx,css,module.css}`
- `.github/instructions/docker.instructions.md` — applyTo: `Dockerfile*,docker-compose*,.env*`
- `.github/instructions/react-typescript.instructions.md` — React patterns, hooks, state
- `.github/instructions/testing.instructions.md` — Testing patterns, best practices

## 🎯 Quick Prompts

Use these in chat for quick templates:

- `@component-design` — Design system components
- `@zustand-store` — Zustand store patterns
- `@test-component` — Vitest + Testing Library tests
- `@permission-check` — Permission checking patterns
- `@autosave-form` — Autosave with 800ms debounce

See `.github/prompts/` for full prompt documentation.
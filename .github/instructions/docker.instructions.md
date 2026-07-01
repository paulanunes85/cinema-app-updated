---
applyTo: "Dockerfile*,docker-compose*,.env*"
---

# Docker Local — CineFlow Studio

## MVP Architecture

The local MVP is a single-container application:

- **Frontend**: React SPA via Vite dev server (dev mode) or nginx (production).
- **State**: Local storage + seed JSON data. No external database required.
- **API**: Mock layer in `src/lib/api.ts` for demo purposes.
- **Ports**: Frontend on `3000`.

## Environment Variables

Create `.env.local` in the project root:

```plaintext
VITE_APP_TITLE=CineFlow Studio
VITE_DEMO_MODE=true
VITE_API_URL=http://localhost:3000
```

These are baked into the Vite build at compile time. Change only when needed.

## Docker Compose Structure

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:80"
    environment:
      - VITE_APP_TITLE=CineFlow Studio
      - VITE_DEMO_MODE=true
```

## Dockerfile (Multi-Stage)

**Build stage**:
- Base: `node:20-alpine`
- Install dependencies: `npm ci`
- Build: `vite build` → output in `dist/`

**Serve stage**:
- Base: `nginx:alpine`
- Copy `dist/` → `/usr/share/nginx/html`
- Expose port `80`
- Default nginx config routes all requests to `index.html` (SPA routing).

## `.dockerignore`

```plaintext
node_modules
.git
.env.local
*.log
.DS_Store
build
dist
coverage
.gitignore
README.md
```

## Local Commands

```bash
# Build and start
docker compose up

# Stop
docker compose down

# View logs
docker compose logs -f app

# Rebuild (after Dockerfile changes)
docker compose up --build
```

## Seed Data

Automatic seed loading when `VITE_DEMO_MODE=true`:

- Loaded from `src/data/seedData.ts`
- Must include: 1 project, 3+ areas, 8+ objectives in mixed states, sample decisions and comments.
- Persisted to `localStorage` on app mount.
- Reset by clearing browser storage.

## Environment-Specific Builds

**Development** (local):
```bash
docker compose -f docker-compose.yml up
```

**Production simulation**:
```bash
docker compose -f docker-compose.prod.yml up
```

(Optional: separate prod compose file with optimized nginx config.)

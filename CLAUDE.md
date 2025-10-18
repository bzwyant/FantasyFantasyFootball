# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Fantasy Fantasy Football is a full-stack TypeScript monorepo for an advanced fantasy football platform. The platform aims to support unique features not found in traditional fantasy football platforms, including:
- FAAB in trades (Free Agent Acquisition Budget as tradeable currency)
- Automatic lineup management
- Future draft pick trading
- Player loans with configurable terms

## Architecture

### Monorepo Structure

This is a **pnpm workspace monorepo** managed by **Turborepo**. The architecture follows a strict separation:

- `apps/client` - Next.js 14 frontend with App Router (@fff/client)
- `apps/server` - Express.js REST API (@fff/server)
- `packages/types` - Shared TypeScript types and Zod schemas (@fff/types)
- `packages/database` - Database utilities and models (@fff/database)

**Critical**: Changes to `packages/*` require rebuilding dependent apps. Turborepo handles this via the `^build` dependency chain.

### Package Management

- **Package Manager**: pnpm 8.15.0+ (enforced via engines field)
- **Node Version**: >= 18.0.0 (enforced via engines field)
- **Never use npm or yarn** - this will break the workspace

### TypeScript Configuration

The monorepo uses **composite TypeScript projects** with project references:
- Root `tsconfig.json` provides base configuration
- Each package has its own `tsconfig.json` that extends the root
- Strict mode is enabled throughout
- Target: ES2020 with CommonJS modules

### Turborepo Pipeline

The build pipeline is defined in `turbo.json`:

1. **build**: Depends on dependencies being built first (`^build`). Outputs to `.next/**` (client) and `dist/**` (server/packages)
2. **dev**: Persistent, cache-disabled for hot reloading
3. **type-check**: Type checks with dependency ordering
4. **lint**: Lints with dependency ordering
5. **clean**: Cache-disabled cleanup task

**Global Dependencies**: All `.env.*local` files trigger cache invalidation

## Common Commands

### Development

```bash
# Start all apps in development mode (client on :3000, server on :3001)
pnpm dev

# Start only the client
cd apps/client && pnpm dev

# Start only the server (uses tsx watch for hot reload)
cd apps/server && pnpm dev
```

### Building

```bash
# Build all packages (respects dependency order)
pnpm build

# Build specific package
cd apps/client && pnpm build
cd apps/server && pnpm build
cd packages/types && pnpm build
```

### Type Checking

```bash
# Type check all packages
pnpm type-check

# Type check specific package (use tsc --noEmit)
cd apps/client && pnpm type-check
```

### Linting

```bash
# Lint all packages
pnpm lint

# Lint client (uses Next.js ESLint config)
cd apps/client && pnpm lint
```

### Cleaning

```bash
# Clean all build artifacts and caches
pnpm clean

# Clean specific package
cd apps/server && pnpm clean  # Removes dist/ and .turbo/
cd apps/client && pnpm clean  # Removes .next/ and .turbo/
```

## Development Workflow

### Working with Shared Packages

When modifying `packages/types` or `packages/database`:

1. Make changes to the package
2. Run `pnpm build` from root (Turborepo rebuilds dependents)
3. Or run `pnpm build` in the specific package, then restart consuming apps

**Note**: The `dev` command does NOT automatically rebuild shared packages. You must manually rebuild them.

### Adding Dependencies

```bash
# Add to workspace root (dev dependencies only)
pnpm add -D <package> -w

# Add to specific app/package
pnpm add <package> --filter @fff/client
pnpm add <package> --filter @fff/server
pnpm add <package> --filter @fff/types
pnpm add <package> --filter @fff/database
```

### Environment Configuration

**Server** (`apps/server/.env`):
- `PORT` - Server port (default: 3001)
- `NODE_ENV` - Environment (development/production)

**Client** (`apps/client/.env.local`):
- `NEXT_PUBLIC_API_URL` - Backend API URL (default: http://localhost:3001)

### API Structure

The Express server (`apps/server/src/index.ts`) currently has:
- `GET /api/health` - Health check endpoint
- `GET /api` - Main API endpoint

When adding new endpoints:
1. Define route handlers in `apps/server/src/index.ts` (or create a routes directory)
2. Define request/response types in `packages/types/src/index.ts` using Zod schemas
3. Import and use types in both client and server

### Client-Server Communication

The client uses Next.js App Router. When making API calls:
- Use `process.env.NEXT_PUBLIC_API_URL` to construct URLs
- Client components: Use `'use client'` directive for state/effects
- Server components: Default in App Router, can fetch directly

## Key Conventions

### Package Naming

All internal packages use the `@fff` scope:
- `@fff/client`
- `@fff/server`
- `@fff/types`
- `@fff/database`

### Validation

Use **Zod** for runtime validation:
- Define schemas in `packages/types`
- Use for API request/response validation
- Export both TypeScript types and Zod schemas

### TypeScript

- Strict mode enabled - no implicit any, null checks enforced
- Use explicit types for function parameters and return values
- Leverage composite projects for cross-package type checking

## Troubleshooting

### "Cannot find module '@fff/types'"

Run `pnpm build` to compile shared packages. The packages must be built before apps can import them.

### Port already in use

Check if server is already running on port 3001 or client on port 3000. Kill the process or change ports via environment variables.

### pnpm install fails

Ensure you're using pnpm 8.0.0+ and Node.js 18.0.0+. Check versions with `pnpm --version` and `node --version`.

### Turborepo cache issues

Run `pnpm clean` to clear all caches and build artifacts, then rebuild with `pnpm build`.

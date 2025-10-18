# Fantasy Fantasy Football

A full-stack TypeScript monorepo application for managing fantasy fantasy football games.

## Overview

Fantasy Fantasy Football is built as a modern web application using a monorepo architecture with pnpm workspaces and Turborepo. The project combines a Next.js frontend with an Express.js backend API, sharing type definitions and utilities across packages.

### What Makes This Platform Different

This isn't your standard fantasy football platform. Fantasy Fantasy Football is designed to support advanced league mechanics that go beyond traditional fantasy football:

- **FAAB in Trades** - Include Free Agent Acquisition Budget as tradeable currency in player transactions
- **Automatic Lineup Management** - Set-and-forget lineup optimization based on your preferences
- **Future Draft Pick Trading** - Trade picks from upcoming seasons to build your dynasty
- **Player Loans** - Temporarily loan players between teams with configurable terms
- **And More** - The platform is built to support creative new features as they're conceived

The goal is to provide a flexible, feature-rich platform that enables commissioners and league members to customize their fantasy experience with mechanics that traditional platforms don't support.

## Tech Stack

- **Language**: TypeScript 5.3.3
- **Package Manager**: pnpm 8.0.0+
- **Build System**: Turborepo 1.11.0
- **Frontend**: Next.js 14.0.4 with React 18.2.0
- **Backend**: Express.js 4.18.2
- **Validation**: Zod 4.1.12
- **Runtime**: Node.js 18.0.0+

## Project Structure

```
FantasyFantasyFootball/
├── apps/
│   ├── client/          # Next.js frontend application
│   └── server/          # Express.js backend API
├── packages/
│   ├── types/           # Shared TypeScript types & Zod schemas
│   └── database/        # Database utilities
├── package.json         # Root workspace configuration
├── pnpm-workspace.yaml  # pnpm workspace definition
└── turbo.json          # Turborepo pipeline configuration
```

### Applications

#### Client ([apps/client](apps/client))
- Next.js 14 with App Router
- React 18 with TypeScript
- Environment variables via `.env.local`

#### Server ([apps/server](apps/server))
- Express.js REST API
- CORS enabled
- Health check endpoint at `/api/health`
- Main API endpoint at `/api`
- Configurable port (default: 3001)

### Shared Packages

#### Types ([packages/types](packages/types))
- Centralized TypeScript type definitions
- Zod runtime validation schemas
- Shared between client and server

#### Database ([packages/database](packages/database))
- Database connection utilities
- Query helpers and models

## Getting Started

### Prerequisites

- Node.js >= 18.0.0
- pnpm >= 8.0.0

### Installation

```bash
# Install dependencies
pnpm install
```

### Environment Setup

1. **Server Configuration**

Create `apps/server/.env` based on `.env.example`:

```env
PORT=3001
NODE_ENV=development
```

2. **Client Configuration**

Create `apps/client/.env.local` based on `.env.local.example`:

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
```

### Development

Start all applications in development mode:

```bash
pnpm dev
```

This will start:
- Client on [http://localhost:3000](http://localhost:3000)
- Server on [http://localhost:3001](http://localhost:3001)

### Building

Build all packages:

```bash
pnpm build
```

### Type Checking

Run TypeScript type checking across all packages:

```bash
pnpm type-check
```

### Linting

Run ESLint across all packages:

```bash
pnpm lint
```

### Cleaning

Remove build artifacts and caches:

```bash
pnpm clean
```

## Available Scripts

### Root Workspace

- `pnpm dev` - Start development servers for all packages
- `pnpm build` - Build all packages for production
- `pnpm type-check` - Type check all packages
- `pnpm lint` - Lint all packages
- `pnpm clean` - Clean build artifacts and caches

### Server Package

```bash
cd apps/server
npm dev          # Watch and rebuild in development
npm build        # Compile TypeScript
npm start        # Run compiled server
npm type-check   # Type checking only
npm clean        # Remove dist and cache
```

### Client Package

```bash
cd apps/client
npm dev          # Start Next.js dev server
npm build        # Build Next.js production bundle
npm start        # Start production server
npm type-check   # Type checking only
npm lint         # Run ESLint
npm clean        # Remove .next and cache
```

## API Endpoints

### Health Check
```
GET /api/health
```

Response:
```json
{
  "status": "ok",
  "message": "Server is running"
}
```

### Main API
```
GET /api
```

Response:
```json
{
  "message": "Fantasy Fantasy Football API"
}
```

## Turborepo Pipeline

The monorepo uses Turborepo for efficient task orchestration:

- **build** - Builds packages with dependency ordering
- **dev** - Persistent development mode (cache disabled)
- **type-check** - TypeScript validation with dependency ordering
- **lint** - Code linting with dependency ordering
- **clean** - Cleanup task (cache disabled)

## TypeScript Configuration

The project uses a strict TypeScript configuration with:
- ES2020 target
- ESNext module resolution
- Strict mode enabled
- Composite project references for workspace packages

## Contributing

This project uses:
- pnpm workspaces for monorepo management
- Turborepo for build caching and task orchestration
- TypeScript for type safety across the stack
- Zod for runtime validation

## License

ISC

## Repository

[https://github.com/bzwyant/FantasyFantasyFootball](https://github.com/bzwyant/FantasyFantasyFootball)

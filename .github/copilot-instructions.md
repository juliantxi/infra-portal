# Infrastructure Portal - AI Agent Instructions

## Project Overview
Infrastructure Portal is an internal DevSecOps platform for managing cloud and on-prem infrastructure with automation, drift monitoring, spend tracking, and workload health dashboards.

**Tech Stack:**
- **Frontend**: Next.js 16 (App Router), React 19, TypeScript
- **Styling**: Tailwind CSS 4 with shadcn/ui components (New York style)
- **Database**: PostgreSQL 18 (via Prisma ORM)
- **Development**: Docker Compose for local PostgreSQL + Adminer

## Architecture & Structure

```
infra-portal/
├── portal-ui/          # Next.js frontend application
│   ├── app/            # App Router pages and layouts
│   ├── components/ui/  # shadcn/ui components
│   ├── lib/            # Utilities and generated Prisma client
│   └── prisma/         # Database schema and migrations
└── docker-compose.yml  # PostgreSQL and Adminer services
```

**Monorepo Setup**: The frontend lives in `portal-ui/` subdirectory. Always run commands from the appropriate directory context.

## Development Workflow

### Local Setup
```bash
# Start PostgreSQL and Adminer
docker compose up -d

# Navigate to frontend
cd portal-ui

# Install dependencies
bun install

# Run development server
bun dev  # http://localhost:3000

# Access database UI
# Adminer: http://localhost:8386
# Server: db, User: mypguser, Password: mypgpassword, Database: infra_portal
```

### Database Management
- **Prisma client** generates to `lib/generated/prisma` (non-standard location)
- **Schema location**: `portal-ui/prisma/schema.prisma`
- **Connection**: Configure via `DATABASE_URL` env var (see `env.example`)
- Use `prisma.config.ts` for Prisma configuration (uses Bun runtime assumptions)

## Code Conventions

### Component Patterns
- **shadcn/ui components**: Use New York style with `data-slot` attributes for flexible styling
- **Styling utility**: Use `cn()` from `@/lib/utils` for conditional class merging (clsx + tailwind-merge)
- **Component structure**: Compound components with sub-components (e.g., Card, CardHeader, CardTitle)

Example from [components/ui/card.tsx](../portal-ui/components/ui/card.tsx):
```tsx
function Card({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="card"
      className={cn("bg-card text-card-foreground flex flex-col gap-6...", className)}
      {...props}
    />
  )
}
```

### TypeScript & Imports
- **Path alias**: `@/*` maps to `portal-ui/` root (configured in tsconfig.json)
- **Component typing**: Use `React.ComponentProps<"element">` for extending native elements
- **Strict mode**: TypeScript strict mode enabled

### Styling Guidelines
- **Tailwind v4**: Using new PostCSS-based Tailwind (not config file)
- **Design tokens**: Uses CSS variables for theming (defined in `app/globals.css`)
- **Base color**: Zinc color palette
- **Dark mode**: Dark mode support via Tailwind's `dark:` variants
- **Responsive**: Mobile-first approach with `sm:`, `md:` breakpoints
- **Container queries**: Use `@container` for component-scoped responsive design

## Key Files & Their Purpose

- **[components.json](../portal-ui/components.json)**: shadcn/ui configuration (style: new-york, RSC enabled)
- **[prisma.config.ts](../portal-ui/prisma.config.ts)**: Prisma config (expects Bun runtime via `--bun` flag)
- **[lib/utils.ts](../portal-ui/lib/utils.ts)**: `cn()` utility for className composition
- **[docker-compose.yml](../docker-compose.yml)**: PostgreSQL on port 5432, Adminer on port 8386

## Critical Details

1. **Prisma Client Path**: Client generates to `lib/generated/prisma`, not default `node_modules/.prisma/client`
2. **Database Connection**: Use Docker Compose services, not external DB. Connection via `localhost:5432`
3. **Component Library**: Don't manually install shadcn/ui components - they're already in `components/ui/`
4. **Font Loading**: Uses Geist Sans and Geist Mono (Next.js font optimization)
5. **App Router**: Using Next.js 16 App Router (not Pages Router) - components in `app/` directory

## Common Tasks

**Add shadcn/ui component**:
```bash
bunx shadcn@latest add [component-name]
```

**Generate Prisma client** (after schema changes):
```bash
cd portal-ui
bunx prisma generate
```

**Run Prisma migrations**:
```bash
cd portal-ui
bunx prisma migrate dev --name [migration-name]
```

**Lint code**:
```bash
cd portal-ui
bun run lint
```

## Context for AI Agents

- This is an **internal tool** - prioritize developer experience over public-facing polish
- **Automation-focused**: Features should support infrastructure automation workflows
- **Dashboard emphasis**: UI should support monitoring drift, spend, and workload health
- PostgreSQL is the **single source of truth** for infrastructure state
- Use **React Server Components** (RSC) by default - this is a Next.js 16 App Router project

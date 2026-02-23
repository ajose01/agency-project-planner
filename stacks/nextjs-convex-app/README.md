# Stack: Next.js + Convex (Full Production App)

Full-featured app with real-time data, auth, file storage, and background jobs.
This is the stack for anything that needs to last.

## When to Use This Stack

**Use this when:**
- Multiple users need accounts (clients, team members)
- Data needs to update live (dashboards, status boards, collaborative tools)
- The app will be actively developed over months
- You need file uploads and storage
- Background jobs are part of the app (nightly digests, processing queues)
- You want everything in one codebase with type safety end-to-end

**Do NOT use this when:**
- It's a pitch demo (nextjs-light is faster to ship)
- There's no UI needed (typescript-workflow is simpler)
- The client just needs two systems connected (typescript-workflow or n8n)

## Core Stack

| Layer | Tool | Notes |
|-------|------|-------|
| Framework | Next.js 15 (App Router) | |
| Backend / DB | Convex | Real-time, type-safe, serverless |
| Auth | Clerk + Convex Auth | Clerk for UI, Convex for data access |
| File storage | Convex File Storage | Built-in, tied to DB |
| Background jobs | Trigger.dev | For heavy/long-running tasks |
| Scheduled jobs | Trigger.dev schedules | Or Convex crons for simple tasks |
| Styling | Tailwind CSS | |
| UI components | shadcn/ui | |
| AI | Vercel AI SDK | `ai` + `@ai-sdk/anthropic` |
| Deployment | Vercel (frontend) + Convex Cloud (backend) | |

## File Structure

```
project-name/
├── CLAUDE.md
├── package.json
├── next.config.ts
├── tailwind.config.ts
├── .env.local
├── .env.example
├── convex/
│   ├── _generated/           # Auto-generated, do not edit
│   ├── schema.ts             # Database schema
│   ├── auth.ts               # Auth configuration
│   ├── [feature].ts          # Queries, mutations, actions per feature
│   └── crons.ts              # Scheduled jobs (simple ones)
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── globals.css
│   ├── (auth)/               # Auth routes
│   │   ├── sign-in/
│   │   └── sign-up/
│   ├── (app)/                # Protected app routes
│   │   └── dashboard/
│   └── api/
│       └── [endpoint]/
│           └── route.ts
├── components/
│   ├── ui/                   # shadcn components
│   └── [feature]/            # Feature-specific components
├── lib/
│   ├── ai.ts
│   └── [integration].ts
└── trigger/
    └── [job-name].ts         # Trigger.dev jobs
```

## Frontend Skill

For building out the UI, check the `frontend` skill at https://github.com/anthropics/skills
Copy it into the project's `.claude/commands/` directory if you need it.

## Getting Started

```bash
npx create-next-app@latest project-name --typescript --tailwind --app
cd project-name
npx convex dev              # Sets up Convex project
npx shadcn@latest init
npm install ai @ai-sdk/anthropic
npm install @clerk/nextjs   # If using Clerk auth
npm install @trigger.dev/sdk  # If using background jobs
```

## Convex Schema Patterns

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from 'convex/server'
import { v } from 'convex/values'

export default defineSchema({
  projects: defineTable({
    clientId: v.string(),
    name: v.string(),
    status: v.string(),
    createdAt: v.number(),
  }).index('by_client', ['clientId']),
})
```

## Convex Query / Mutation Pattern

```typescript
// convex/projects.ts
import { query, mutation } from './_generated/server'
import { v } from 'convex/values'

export const list = query({
  args: { clientId: v.string() },
  handler: async (ctx, { clientId }) => {
    return ctx.db
      .query('projects')
      .withIndex('by_client', q => q.eq('clientId', clientId))
      .collect()
  },
})

export const create = mutation({
  args: { name: v.string(), clientId: v.string() },
  handler: async (ctx, args) => {
    return ctx.db.insert('projects', {
      ...args,
      status: 'active',
      createdAt: Date.now(),
    })
  },
})
```

## Convex Action (for external API calls)

```typescript
// convex/ai.ts
import { action } from './_generated/server'
import { generateText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

export const processDocument = action({
  args: { text: v.string() },
  handler: async (ctx, { text }) => {
    const { text: result } = await generateText({
      model: anthropic('claude-sonnet-4-6'),
      prompt: `Process this: ${text}`,
    })
    return result
  },
})
```

## Auth Setup (Clerk + Convex)

See Convex + Clerk docs. Key points:
- Clerk handles sign-in/sign-up UI
- Convex uses Clerk JWT for identity in queries/mutations
- `ctx.auth.getUserIdentity()` in Convex handlers

## Environment Variables

```
# Convex
CONVEX_DEPLOYMENT=
NEXT_PUBLIC_CONVEX_URL=

# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=

# AI
ANTHROPIC_API_KEY=

# Trigger.dev (if using)
TRIGGER_SECRET_KEY=
```

## Deployment

1. `npx convex deploy` — deploys Convex backend
2. `npx vercel` — deploys Next.js frontend
3. Set all env vars in both Vercel and Convex dashboards
4. `npx trigger.dev@latest deploy` — if using Trigger.dev jobs

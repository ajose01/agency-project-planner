# {{PROJECT_NAME}}

**Client:** {{CLIENT_NAME}}
**Purpose:** {{ONE_LINE_DESCRIPTION}}
**Stack:** Next.js + Convex (Full App)

## What This Does

{{DESCRIPTION}}

## Database Schema

Key tables in `convex/schema.ts`:

| Table | Purpose |
|-------|---------|
| {{TABLE_NAME}} | {{purpose}} |

## Key Features

{{FEATURES_LIST}}

## Integrations

{{LIST_INTEGRATIONS}}

## Auth

{{AUTH_DESCRIPTION}} — using Clerk + Convex Auth.

## Background Jobs

| Job | File | Schedule/Trigger |
|-----|------|-----------------|
| {{JOB_NAME}} | `trigger/{{job-file}}.ts` | {{schedule}} |

## Environment Variables

Copy `.env.example` to `.env.local` and fill in:

```
CONVEX_DEPLOYMENT=
NEXT_PUBLIC_CONVEX_URL=
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
ANTHROPIC_API_KEY=
{{OTHER_ENV_VARS}}
```

## Running Locally

```bash
npm install
cp .env.example .env.local
# Fill in .env.local values
npx convex dev          # terminal 1 — runs Convex backend
npm run dev             # terminal 2 — runs Next.js
```

## Deploying

```bash
npx convex deploy       # deploy Convex backend
npx vercel              # deploy Next.js frontend
npx trigger.dev@latest deploy  # deploy background jobs (if applicable)
```

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Used

{{PATTERNS_REFERENCED}}

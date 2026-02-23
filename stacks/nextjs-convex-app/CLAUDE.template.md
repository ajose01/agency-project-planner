# {{PROJECT_NAME}} — Claude Instructions

**Client:** {{CLIENT_NAME}}
**Stack:** Next.js + Convex

## What This Project Does

{{DESCRIPTION}}

## Architecture

**Database schema** defined in `convex/schema.ts`:

| Table | Purpose |
|-------|---------|
| {{TABLE_NAME}} | {{purpose}} |

**Auth:** {{AUTH_DESCRIPTION}} via Clerk + Convex Auth.

**Background jobs** in `trigger/`:

| Job | File | Schedule/Trigger |
|-----|------|-----------------|
| {{JOB_NAME}} | `trigger/{{job-file}}.ts` | {{schedule}} |

**Integrations:** {{LIST_INTEGRATIONS}}

## Conventions

- Queries and mutations go in `convex/` — one file per feature domain
- External API calls go in Convex actions (not queries/mutations) or in `lib/`
- Never call external APIs from Convex queries or mutations — use actions
- Keep Next.js route handlers thin — call Convex mutations or `lib/` helpers
- Background jobs that are heavy or long-running go in `trigger/`, not Convex crons

## Key Files

- `convex/schema.ts` — database schema (source of truth)
- `convex/auth.ts` — auth configuration
- `lib/` — external API clients (GHL, AI, etc.)
- `trigger/` — Trigger.dev background jobs

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Referenced

{{PATTERNS_REFERENCED}}

# {{PROJECT_NAME}} — Claude Instructions

**Client:** {{CLIENT_NAME}}
**Stack:** Next.js Light (Vercel)

## What This Project Does

{{DESCRIPTION}}

## Architecture

**Pages:**

| Route | File | Purpose |
|-------|------|---------|
| `/` | `app/page.tsx` | {{page_purpose}} |

**API Routes:**

| Endpoint | File | Purpose |
|----------|------|---------|
| `POST /api/{{endpoint}}` | `app/api/{{endpoint}}/route.ts` | {{purpose}} |

**Integrations:** {{LIST_INTEGRATIONS}}

## Conventions

- This is a light MVP — resist adding complexity until the client validates the concept
- No state management library — React state and server components only
- No auth unless the demo explicitly requires it
- API clients go in `lib/` — one file per external service
- Keep API routes thin — business logic in `lib/`, not in route handlers

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Referenced

{{PATTERNS_REFERENCED}}

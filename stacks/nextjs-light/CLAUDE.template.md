# {{PROJECT_NAME}}

**Client:** {{CLIENT_NAME}}
**Purpose:** {{ONE_LINE_DESCRIPTION}}
**Stack:** Next.js Light (Vercel)

## What This Does

{{DESCRIPTION}}

## Pages

| Route | File | Purpose |
|-------|------|---------|
| `/` | `app/page.tsx` | {{page_purpose}} |

## API Routes

| Endpoint | File | Purpose |
|----------|------|---------|
| `POST /api/{{endpoint}}` | `app/api/{{endpoint}}/route.ts` | {{purpose}} |

## Integrations

{{LIST_INTEGRATIONS}}

## Environment Variables

Copy `.env.example` to `.env.local` and fill in:

```
{{ENV_VARS}}
```

## Running Locally

```bash
npm install
cp .env.example .env.local
# Fill in .env.local values
npm run dev
```

## Deploying

```bash
npx vercel
```

Set env vars in the Vercel dashboard. Every push to `main` auto-deploys.

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Used

{{PATTERNS_REFERENCED}}

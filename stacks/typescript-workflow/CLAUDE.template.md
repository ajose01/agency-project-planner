# {{PROJECT_NAME}}

**Client:** {{CLIENT_NAME}}
**Purpose:** {{ONE_LINE_DESCRIPTION}}
**Stack:** TypeScript Workflow (Trigger.dev)

## What This Does

{{DESCRIPTION}}

## Trigger

{{TRIGGER_DESCRIPTION}}

## Jobs

| Job | File | Schedule/Trigger |
|-----|------|-----------------|
| {{JOB_NAME}} | `src/jobs/{{job-file}}.ts` | {{schedule or webhook}} |

## Integrations

{{LIST_INTEGRATIONS}}

See `src/lib/` for API clients.

## Environment Variables

Copy `.env.example` to `.env` and fill in:

```
{{ENV_VARS}}
```

## Running Locally

```bash
npm install
cp .env.example .env
# Fill in .env values
npm run dev    # starts Trigger.dev dev server
```

## Deploying

```bash
npm run deploy  # deploys to Trigger.dev
```

Register webhooks in external services using the URLs from the Trigger.dev dashboard.

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Used

{{PATTERNS_REFERENCED}}

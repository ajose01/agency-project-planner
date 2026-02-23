# Stack: TypeScript Workflow

Code-first automations. No visual editor. Full type safety, version control, and testability.

## When to Use This Stack

**Use this when:**
- Logic is complex enough that code is clearer than a visual graph
- You want type-safe integration with external APIs
- Long-term maintenance matters (you'll be coming back to this)
- The workflow needs custom error handling, retries, or state
- You want to test the logic
- The client will never need to modify it themselves

**Do NOT use this when:**
- A client or non-developer needs to visually understand or edit the workflow
- You need a demo running in 2 hours and the API integrations are simple
- It's truly just "call API A, POST result to API B" with no branching

## Core Stack

| Layer | Tool | Notes |
|-------|------|-------|
| Language | TypeScript | Strict mode always |
| Runtime | Node.js (via Bun or tsx) | |
| Background jobs / scheduling | Trigger.dev | For scheduled, event-driven, or long-running tasks |
| Deployment | Trigger.dev Cloud or self-hosted | Or a simple Vercel Edge Function for webhook receivers |
| Secrets | `.env` + Vercel env vars | |
| HTTP client | Native `fetch` | No axios needed |
| AI | Vercel AI SDK | `ai` package with `@ai-sdk/anthropic` |

## When to Use Trigger.dev vs a Standalone Script

**Use Trigger.dev when:**
- The job runs on a schedule (nightly, hourly)
- It's triggered by a webhook from an external service
- The job runs long (>10 seconds) or has multiple steps
- You need retry logic, failure alerts, or job history
- It will be run repeatedly in production

**Use a standalone script when:**
- It's a one-time data migration or setup task
- You're prototyping logic before wiring it to Trigger.dev
- The job is triggered manually by you (not automated)

## File Structure

```
project-name/
├── CLAUDE.md
├── package.json
├── tsconfig.json
├── trigger.config.ts          # If using Trigger.dev
├── .env
├── .env.example
├── src/
│   ├── jobs/
│   │   └── [job-name].ts      # One file per Trigger.dev job
│   ├── lib/
│   │   ├── ghl.ts             # GHL API client
│   │   ├── ai.ts              # AI SDK helpers
│   │   └── [integration].ts   # One file per external integration
│   └── index.ts               # Entry point for standalone scripts
└── tests/
    └── [job-name].test.ts
```

## Key Patterns

### Trigger.dev Job Skeleton
```typescript
import { task } from '@trigger.dev/sdk/v3'

export const myJob = task({
  id: 'my-job',
  run: async (payload: { /* ... */ }) => {
    // job logic
  },
})
```

### Scheduled Job
```typescript
import { schedules } from '@trigger.dev/sdk/v3'

export const nightlyJob = schedules.task({
  id: 'nightly-digest',
  cron: '0 6 * * *', // 6am UTC daily
  run: async () => {
    // collect, process, notify
  },
})
```

### Webhook Trigger
```typescript
// src/jobs/on-form-submit.ts
export const onFormSubmit = task({
  id: 'on-form-submit',
  run: async (payload: FormSubmitPayload) => {
    // handle webhook payload
  },
})
```

## Deployment

1. `npx trigger.dev@latest init` to connect to Trigger.dev
2. `npx trigger.dev@latest deploy` to deploy jobs
3. Set env vars in Trigger.dev dashboard
4. Register webhook URLs from Trigger.dev in external services

## package.json Starter

```json
{
  "name": "project-name",
  "version": "1.0.0",
  "scripts": {
    "dev": "npx trigger.dev@latest dev",
    "deploy": "npx trigger.dev@latest deploy",
    "script": "tsx src/index.ts"
  },
  "dependencies": {
    "@trigger.dev/sdk": "^3.0.0",
    "ai": "^4.0.0",
    "@ai-sdk/anthropic": "^1.0.0"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "tsx": "^4.0.0",
    "@types/node": "^20.0.0"
  }
}
```

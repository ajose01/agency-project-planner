# Pattern: Scheduled Jobs

Nightly batch processing, morning digests, periodic syncs.

## Stack Options

| Option | Best When |
|--------|-----------|
| **Trigger.dev schedules** | Job is part of a code-first project, needs retry logic |
| **Convex crons** | Simple recurring task inside a Convex app |
| **Vercel Cron** | Simple HTTP endpoint you want to hit on a schedule |
| **n8n schedule trigger** | Workflow already lives in n8n |

## Trigger.dev Scheduled Task

```typescript
// src/jobs/nightly-digest.ts
import { schedules } from '@trigger.dev/sdk/v3'

export const nightlyDigest = schedules.task({
  id: 'nightly-digest',
  // Cron expression: minute hour day month weekday
  cron: '0 22 * * *',  // 10pm UTC every night
  run: async (payload, { ctx }) => {
    console.log('Running nightly digest for', payload.timestamp)

    // 1. Collect data
    const updates = await collectStatusEmails()

    // 2. Process
    const summary = await generateDigest(updates)

    // 3. Send
    await sendToRecipients(summary)

    return { processed: updates.length }
  },
})
```

### Timezone Handling

Cron in Trigger.dev runs in UTC. Convert for US-based clients:

| US Timezone | UTC offset (standard) | UTC offset (DST) |
|-------------|----------------------|------------------|
| Eastern | UTC-5 | UTC-4 |
| Central | UTC-6 | UTC-5 |
| Mountain | UTC-7 | UTC-6 |
| Pacific | UTC-8 | UTC-7 |

For a 7am Eastern digest:
- Standard time: `0 12 * * *` (noon UTC = 7am EST)
- DST: `0 11 * * *` (11am UTC = 7am EDT)

Consider using a library like `date-fns-tz` for precise timezone handling.

## Convex Cron (Inside Convex App)

```typescript
// convex/crons.ts
import { cronJobs } from 'convex/server'
import { internal } from './_generated/api'

const crons = cronJobs()

crons.daily(
  'nightly-dashboard-update',
  { hourUTC: 22, minuteUTC: 0 },
  internal.dashboard.updateNightly
)

export default crons
```

The Convex function:
```typescript
// convex/dashboard.ts
import { internalAction } from './_generated/server'

export const updateNightly = internalAction({
  handler: async (ctx) => {
    // query data, update records, etc.
  },
})
```

## Vercel Cron (Simple HTTP Trigger)

In `vercel.json`:
```json
{
  "crons": [
    {
      "path": "/api/cron/nightly",
      "schedule": "0 22 * * *"
    }
  ]
}
```

In your Next.js route:
```typescript
// app/api/cron/nightly/route.ts
export async function GET(req: Request) {
  // Verify it's actually from Vercel
  const authHeader = req.headers.get('authorization')
  if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response('Unauthorized', { status: 401 })
  }

  await runNightlyJob()
  return Response.json({ success: true })
}
```

Note: Vercel Cron is free on all plans but limited to 1 invocation per day on free tier.

## Handling Long-Running Jobs

If the job takes more than a few seconds:
- Don't use Vercel Cron directly (10s timeout on hobby, 5min on pro)
- Use Trigger.dev (no timeout on jobs)
- Or: Vercel Cron hits a webhook → Trigger.dev job runs

## Job Monitoring and Alerts

For production scheduled jobs, always have visibility:

**Trigger.dev**: Dashboard shows every run, status, logs, and errors. Set up email alerts for failures in project settings.

**Convex**: Built-in logging in the Convex dashboard.

**Dead man's switch** for critical jobs: Use a service like [healthchecks.io](https://healthchecks.io) (free tier available). Your job pings it on success; if it doesn't ping within the expected window, you get an alert.

```typescript
// At end of successful job run
await fetch(`https://hc-ping.com/${process.env.HEALTHCHECK_UUID}`)
```

## Common Cron Expressions

```
0 22 * * *      — 10pm UTC every night
0 6 * * 1-5     — 6am UTC weekdays only
0 */6 * * *     — Every 6 hours
0 9 * * 1       — 9am UTC every Monday
*/15 * * * *    — Every 15 minutes
```

## Environment Variables

```
CRON_SECRET=            # For Vercel Cron authentication
HEALTHCHECK_UUID=       # healthchecks.io UUID (optional but recommended for prod)
```

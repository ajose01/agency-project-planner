# Pattern: Webhook Triggers

Receiving webhooks from external services (GHL, forms, payment processors, etc.)
and routing them to the right handler.

## Webhook Receiver Options by Stack

| Stack | How to receive |
|-------|---------------|
| TypeScript + Trigger.dev | Trigger.dev webhook trigger (built-in URL) |
| Next.js | `app/api/webhook/[source]/route.ts` |
| n8n | Webhook node (n8n provides the URL) |
| Standalone | Simple Express/Hono server on Railway or Fly |

## Trigger.dev Webhook (Recommended for code-first)

```typescript
// src/jobs/on-ghl-form.ts
import { task } from '@trigger.dev/sdk/v3'

export const onGHLFormSubmit = task({
  id: 'on-ghl-form-submit',
  run: async (payload: GHLFormPayload) => {
    // handle the form submission
  },
})
```

Register the webhook URL in the source system — Trigger.dev provides it in the dashboard.

## Next.js Webhook Route

```typescript
// app/api/webhook/ghl/route.ts
import { NextRequest, NextResponse } from 'next/server'

export async function POST(req: NextRequest) {
  // 1. Verify signature (if the service sends one)
  const signature = req.headers.get('x-ghl-signature')
  if (!verifySignature(await req.text(), signature)) {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 401 })
  }

  const payload = await req.json()

  // 2. Respond immediately (most webhooks expect < 5s response)
  // Kick off async processing
  processWebhook(payload).catch(console.error)

  return NextResponse.json({ received: true })
}
```

Important: **always respond quickly** to webhooks (< 5 seconds). If processing takes longer,
acknowledge receipt first, then process async.

## Webhook Signature Verification

Most services send a signature to prove the webhook is real. Always verify in production.

**GHL** (HMAC-SHA256):
```typescript
import { createHmac } from 'crypto'

function verifyGHLSignature(body: string, signature: string | null): boolean {
  if (!signature) return false
  const expected = createHmac('sha256', process.env.GHL_WEBHOOK_SECRET!)
    .update(body)
    .digest('hex')
  return signature === expected
}
```

**Generic HMAC pattern** (works for most services):
```typescript
function verifyHmac(
  body: string,
  signature: string,
  secret: string,
  algorithm = 'sha256'
): boolean {
  const expected = createHmac(algorithm, secret).update(body).digest('hex')
  // Use timingSafeEqual to prevent timing attacks
  return expected.length === signature.length &&
    timingSafeEqual(Buffer.from(expected), Buffer.from(signature))
}
```

## Webhook Payload Typing

Define TypeScript types for each webhook source:

```typescript
// lib/webhook-types.ts

type GHLContactCreate = {
  type: 'ContactCreate'
  locationId: string
  contactId: string
  contact: {
    id: string
    email: string
    firstName: string
    lastName: string
    phone: string
    tags: string[]
  }
}

type GHLFormSubmission = {
  type: 'FormSubmission'
  locationId: string
  formId: string
  contactId: string
  formData: Record<string, string>
}
```

## Idempotency

Webhooks can be delivered more than once. Protect against duplicate processing:

```typescript
// Use the webhook event ID to deduplicate
async function processWebhook(payload: WebhookPayload, eventId: string) {
  // Check if already processed (in DB or cache)
  const alreadyProcessed = await db.webhookEvents.findUnique({ where: { id: eventId } })
  if (alreadyProcessed) return

  // Mark as processing
  await db.webhookEvents.create({ data: { id: eventId, status: 'processing' } })

  try {
    // ... process ...
    await db.webhookEvents.update({ where: { id: eventId }, data: { status: 'done' } })
  } catch (err) {
    await db.webhookEvents.update({ where: { id: eventId }, data: { status: 'failed' } })
    throw err
  }
}
```

## Testing Webhooks Locally

Use **ngrok** or **Cloudflare Tunnel** to expose your local server:

```bash
# ngrok
ngrok http 3000
# Copy the https URL and paste into the source system as your webhook URL

# Cloudflare Tunnel (more stable, free)
cloudflared tunnel --url http://localhost:3000
```

For Trigger.dev, use `npx trigger.dev@latest dev` which handles this automatically.

## Environment Variables

```
GHL_WEBHOOK_SECRET=     # Shared secret for verifying GHL webhooks
WEBHOOK_SECRET=         # Generic webhook secret (if single secret)
```

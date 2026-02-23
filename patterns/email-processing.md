# Pattern: Email Processing

Ingesting emails, extracting structured data, and triggering actions.
Common use case: nightly collection of project status emails → dashboard update → COO digest.

## Approaches by Use Case

| Use Case | Approach |
|----------|----------|
| Receive inbound email → trigger action | Use an email routing service (Postmark, Resend, Mailgun inbound) |
| Poll a mailbox on a schedule | IMAP polling job (Trigger.dev scheduled task) |
| Parse and extract data from emails | Claude (AI SDK) for structured extraction |
| Send outbound notifications | Resend (simplest) or Nodemailer |

## Inbound Email Routing (Recommended for Triggers)

Use **Postmark** or **Resend** inbound routing. They forward emails to your webhook as JSON.
This is far simpler than polling IMAP.

**Postmark Inbound Setup:**
1. Create a Postmark server, get your inbound address (e.g., `abc123@inbound.postmarkapp.com`)
2. Set your webhook URL in Postmark
3. Postmark POSTs parsed email JSON to your webhook

```typescript
// Postmark inbound payload (simplified)
type PostmarkInbound = {
  From: string
  To: string
  Subject: string
  TextBody: string
  HtmlBody: string
  Date: string
  Attachments: Array<{
    Name: string
    Content: string  // base64
    ContentType: string
  }>
}
```

Inbound webhook handler:
```typescript
// app/api/webhook/email/route.ts
export async function POST(req: NextRequest) {
  const email: PostmarkInbound = await req.json()

  // Kick off async processing
  processStatusEmail(email).catch(console.error)

  return NextResponse.json({ received: true })
}
```

## IMAP Polling (Scheduled Job)

When you can't change where emails are sent (e.g., a Gmail inbox someone already uses).

```bash
npm install imap-simple mailparser
```

```typescript
// src/jobs/email-poller.ts
import imaps from 'imap-simple'
import { simpleParser } from 'mailparser'

export const pollStatusEmails = schedules.task({
  id: 'poll-status-emails',
  cron: '0 22 * * *',  // 10pm nightly
  run: async () => {
    const connection = await imaps.connect({
      imap: {
        user: process.env.EMAIL_USER!,
        password: process.env.EMAIL_APP_PASSWORD!,
        host: 'imap.gmail.com',
        port: 993,
        tls: true,
      },
    })

    await connection.openBox('INBOX')

    // Fetch unread emails from today
    const today = new Date()
    today.setHours(0, 0, 0, 0)
    const messages = await connection.search(
      ['UNSEEN', ['SINCE', today.toISOString()]],
      { bodies: [''], markSeen: true }
    )

    const emails = await Promise.all(
      messages.map(async (msg) => {
        const all = msg.parts.find(p => p.which === '')
        const parsed = await simpleParser(all.body)
        return {
          from: parsed.from?.text,
          subject: parsed.subject,
          text: parsed.text,
          date: parsed.date,
        }
      })
    )

    connection.end()
    return emails
  },
})
```

## AI Extraction from Email Body

Use Claude to extract structured data from unstructured email text:

```typescript
import { generateObject } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
import { z } from 'zod'

const StatusUpdateSchema = z.object({
  projectName: z.string(),
  status: z.enum(['on_track', 'delayed', 'blocked', 'complete']),
  completedTasks: z.array(z.string()),
  blockers: z.array(z.string()),
  nextSteps: z.array(z.string()),
  estimatedCompletion: z.string().optional(),
})

async function extractStatusUpdate(emailText: string) {
  const { object } = await generateObject({
    model: anthropic('claude-sonnet-4-6'),
    schema: StatusUpdateSchema,
    prompt: `Extract a structured project status update from this email:

${emailText}

If a field is not mentioned in the email, use an empty array or null.`,
  })
  return object
}
```

## Morning Digest (COO Notification)

After collecting and parsing all status emails:

```typescript
async function sendMorningDigest(updates: StatusUpdate[], recipientEmail: string) {
  // 1. Generate summary with AI
  const { text: summary } = await generateText({
    model: anthropic('claude-sonnet-4-6'),
    prompt: `Write a concise morning briefing for the COO summarizing these project updates:

${JSON.stringify(updates, null, 2)}

Format: short paragraph overview, then bullet points for any projects that are delayed or blocked.`,
  })

  // 2. Send via Resend
  await resend.emails.send({
    from: 'digest@yourdomain.com',
    to: recipientEmail,
    subject: `Morning Project Digest — ${new Date().toLocaleDateString()}`,
    text: summary,
  })
}
```

## Sending Outbound Email (Resend)

```bash
npm install resend
```

```typescript
import { Resend } from 'resend'
const resend = new Resend(process.env.RESEND_API_KEY)

await resend.emails.send({
  from: 'noreply@yourdomain.com',
  to: 'coo@clientcompany.com',
  subject: 'Subject here',
  text: 'Plain text body',
  // html: '<p>Or HTML body</p>',
})
```

## Environment Variables

```
# IMAP polling
EMAIL_USER=             # Gmail address or IMAP user
EMAIL_APP_PASSWORD=     # Gmail app password (not your real password)

# Inbound routing
POSTMARK_WEBHOOK_TOKEN= # For verifying Postmark webhooks

# Outbound
RESEND_API_KEY=         # resend.com API key
FROM_EMAIL=             # Verified sender address

# AI
ANTHROPIC_API_KEY=
```

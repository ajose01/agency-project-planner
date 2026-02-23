# Pattern: GoHighLevel (GHL) Integration

GoHighLevel is the primary CRM for most clients. These patterns cover the most common
operations across any stack.

## Authentication

GHL uses two auth models:

**Private Integration Key** (simpler, for server-side use):
- Generate in GHL Settings → Integrations → API Keys
- Pass as `Authorization: Bearer {key}` header
- Scoped to one sub-account

**OAuth 2.0** (for multi-account or agency-level):
- Required if your tool works across multiple GHL sub-accounts
- More complex setup — only use if needed

For single-client automations, use the Private Integration Key.

## Base Setup (TypeScript)

```typescript
// lib/ghl.ts
const GHL_BASE = 'https://services.leadconnectorhq.com'
const GHL_API_KEY = process.env.GHL_API_KEY!
const GHL_LOCATION_ID = process.env.GHL_LOCATION_ID!

async function ghlFetch(path: string, options?: RequestInit) {
  const res = await fetch(`${GHL_BASE}${path}`, {
    ...options,
    headers: {
      'Authorization': `Bearer ${GHL_API_KEY}`,
      'Content-Type': 'application/json',
      'Version': '2021-07-28',
      ...options?.headers,
    },
  })
  if (!res.ok) {
    throw new Error(`GHL ${options?.method ?? 'GET'} ${path} failed: ${res.status}`)
  }
  return res.json()
}
```

## Common Operations

### Create a Contact

```typescript
async function createContact(data: {
  firstName: string
  lastName: string
  email?: string
  phone?: string
  tags?: string[]
}) {
  return ghlFetch('/contacts/', {
    method: 'POST',
    body: JSON.stringify({
      ...data,
      locationId: GHL_LOCATION_ID,
    }),
  })
}
```

### Create an Opportunity (Project in Pipeline)

```typescript
async function createOpportunity(data: {
  name: string
  pipelineId: string
  pipelineStageId: string
  contactId: string
  status?: 'open' | 'won' | 'lost' | 'abandoned'
  monetaryValue?: number
}) {
  return ghlFetch('/opportunities/', {
    method: 'POST',
    body: JSON.stringify({
      ...data,
      locationId: GHL_LOCATION_ID,
    }),
  })
}
```

### Add a Note to a Contact

```typescript
async function addNote(contactId: string, body: string) {
  return ghlFetch(`/contacts/${contactId}/notes`, {
    method: 'POST',
    body: JSON.stringify({ body, userId: GHL_LOCATION_ID }),
  })
}
```

### Update Opportunity Stage

```typescript
async function updateOpportunityStage(opportunityId: string, pipelineStageId: string) {
  return ghlFetch(`/opportunities/${opportunityId}`, {
    method: 'PUT',
    body: JSON.stringify({ pipelineStageId }),
  })
}
```

### Add Custom Field Value to Contact

```typescript
async function setCustomField(contactId: string, fieldId: string, value: string) {
  return ghlFetch(`/contacts/${contactId}`, {
    method: 'PUT',
    body: JSON.stringify({
      customFields: [{ id: fieldId, field_value: value }],
    }),
  })
}
```

## Inbound Webhooks from GHL

GHL can send webhooks when contacts, opportunities, or form submissions change.

1. Go to GHL Settings → Integrations → Webhooks
2. Add your endpoint URL (Trigger.dev webhook URL or Next.js API route)
3. Select events to subscribe to

Webhook payload shape:
```typescript
type GHLWebhookPayload = {
  type: 'ContactCreate' | 'ContactUpdate' | 'OpportunityCreate' | 'FormSubmission' | string
  locationId: string
  contactId?: string
  contact?: {
    id: string
    email: string
    firstName: string
    lastName: string
    phone: string
    tags: string[]
  }
}
```

## Environment Variables

```
GHL_API_KEY=            # Private integration key
GHL_LOCATION_ID=        # Sub-account location ID
GHL_PIPELINE_ID=        # Pipeline ID (get from GHL UI URL)
GHL_STAGE_ID=           # Stage ID (get from GHL UI URL)
```

To find Pipeline and Stage IDs: open the pipeline in GHL, the IDs are in the URL.

## n8n Notes

- Use the **GoHighLevel** node if available, or HTTP Request node with the above patterns
- Store API key as a credential in n8n (not hardcoded)
- The location ID is required on most write operations

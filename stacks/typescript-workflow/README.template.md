# {{PROJECT_NAME}}

{{ONE_LINE_DESCRIPTION}}

**Client:** {{CLIENT_NAME}}

## Setup

```bash
npm install
cp .env.example .env
```

Fill in `.env`:

```
{{ENV_VARS}}
```

## Running Locally

```bash
npm run dev    # starts Trigger.dev dev server
```

## Deploying

```bash
npm run deploy
```

After deploying, copy the webhook URLs from the Trigger.dev dashboard and register them in:

{{WEBHOOK_REGISTRATION_STEPS}}

## Jobs

| Job | Schedule/Trigger |
|-----|-----------------|
| {{JOB_NAME}} | {{schedule or webhook}} |

## Integrations

{{LIST_INTEGRATIONS}}

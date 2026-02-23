# {{PROJECT_NAME}}

{{ONE_LINE_DESCRIPTION}}

**Client:** {{CLIENT_NAME}}

## Setup

1. Import workflow files from `workflows/` into n8n
2. Configure credentials for each integration in n8n Settings → Credentials
3. Register webhook URLs in external services (see below)
4. Activate workflows

## Webhook Registration

{{WEBHOOK_REGISTRATION_STEPS}}

## Required Credentials

Configure these in the n8n UI:

{{CREDENTIALS_LIST}}

## Workflows

| Workflow | File | Trigger |
|----------|------|---------|
| {{WORKFLOW_NAME}} | `workflows/{{workflow-file}}.json` | {{trigger}} |

## Updating Workflows

After making changes in n8n, export the workflow JSON and commit it:

1. Open workflow in n8n
2. Menu → Download
3. Save to `workflows/{{workflow-file}}.json`
4. Commit the change

# {{PROJECT_NAME}}

**Client:** {{CLIENT_NAME}}
**Purpose:** {{ONE_LINE_DESCRIPTION}}
**Stack:** n8n Workflow

## What This Does

{{DESCRIPTION}}

## Workflows

| Workflow | File | Trigger |
|----------|------|---------|
| {{WORKFLOW_NAME}} | `workflows/{{workflow-file}}.json` | {{trigger}} |

## Integrations

{{LIST_INTEGRATIONS}}

All credentials are configured in the n8n UI (not in code).

## Setup

1. Import workflow JSON files from `workflows/` into n8n
2. Configure credentials for each integration in n8n settings
3. Set webhook URLs in external services (see below)
4. Activate workflows

## Webhook URLs

After importing, copy webhook URLs from n8n and register them in:

{{WEBHOOK_REGISTRATION_STEPS}}

## Required Credentials (configure in n8n)

{{CREDENTIALS_LIST}}

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Versioning

Export updated workflows to `workflows/` and commit after every meaningful change.
Always export before sharing with clients or before making major changes.

## Patterns Used

{{PATTERNS_REFERENCED}}

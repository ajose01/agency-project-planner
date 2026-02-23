# {{PROJECT_NAME}} — Claude Instructions

**Client:** {{CLIENT_NAME}}
**Stack:** n8n Workflow

## What This Project Does

{{DESCRIPTION}}

## Workflows

| Workflow | File | Trigger |
|----------|------|---------|
| {{WORKFLOW_NAME}} | `workflows/{{workflow-file}}.json` | {{trigger}} |

## Conventions

- One workflow = one trigger — don't fan multiple unrelated processes from one workflow
- Name nodes clearly — the visual graph is the documentation
- Always include error handling nodes — n8n silently fails without them
- Use sub-workflows for any logic reused across workflows
- Export workflows to `workflows/` and commit after every meaningful change

## Integrations

{{LIST_INTEGRATIONS}}

All credentials are stored in the n8n UI, not in code.

## Architecture Notes

{{ARCHITECTURE_NOTES}}

## Patterns Referenced

{{PATTERNS_REFERENCED}}

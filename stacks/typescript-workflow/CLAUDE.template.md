# {{PROJECT_NAME}} — Claude Instructions

**Client:** {{CLIENT_NAME}}
**Stack:** TypeScript Workflow (Trigger.dev)

## What This Project Does

{{DESCRIPTION}}

## Architecture

**Trigger:** {{TRIGGER_DESCRIPTION}}

**Jobs:**

| Job | File | Schedule/Trigger |
|-----|------|-----------------|
| {{JOB_NAME}} | `src/jobs/{{job-file}}.ts` | {{schedule or webhook}} |

**Integrations:** {{LIST_INTEGRATIONS}}

API clients live in `src/lib/` — one file per external service.

## Conventions

- One job per file in `src/lib/`
- Keep API clients in `src/lib/` — no direct `fetch` calls in job files
- Jobs should be idempotent — safe to re-run on failure
- All secrets via environment variables, never hardcoded

## Patterns Referenced

{{PATTERNS_REFERENCED}}

## Architecture Notes

{{ARCHITECTURE_NOTES}}

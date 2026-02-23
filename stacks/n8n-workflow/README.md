# Stack: n8n Workflow

Visual orchestration. Best when the workflow is relatively linear, uses well-supported integrations,
and visibility matters (for you or the client).

## When to Use This Stack

**Use this when:**
- The client needs to see or understand the workflow visually
- The client (or a non-developer) may need to modify it in the future
- You need a working demo in hours, not days
- The workflow is mostly "call API A → transform → call API B" with minimal branching
- The integrations you need have built-in n8n nodes (Google, Slack, email, webhooks)
- You're prototyping before deciding if a code-based solution is worth building

**Do NOT use this when:**
- Logic is complex enough that the visual graph becomes unreadable
- You need deep type safety or custom error handling strategies
- The workflow will be actively developed alongside a larger codebase
- You need unit tests or CI/CD for the workflow logic
- Cost of n8n cloud is a concern for a simple one-integration workflow

## Hosting Options

| Option | Best For | Notes |
|--------|----------|-------|
| n8n Cloud | Quick start, no infra | Paid, easy |
| Self-hosted (Railway, Render, Fly.io) | Cost control, client ownership | ~$5–10/mo |
| Self-hosted (client's server) | Client owns everything | More setup |

## Workflow Design Principles

- **One workflow = one trigger.** Don't fan out into unrelated processes from a single workflow.
- **Use sub-workflows** for reusable logic (e.g., "create GHL contact" used in 3 workflows).
- **Always add error handling nodes** — n8n silently fails without them.
- **Name your nodes clearly** — the visual graph is the documentation.
- **Pin test data** to nodes during development, remove before production.

## AI in n8n

- Use the built-in **AI Agent node** or **OpenAI/Anthropic nodes** for simple AI calls
- For complex AI logic (multi-step, tool calling, structured output), consider whether a code node or a Trigger.dev job makes more sense — n8n AI nodes have limitations
- The **Code node** (JavaScript) can call external AI APIs directly if needed

## File Structure (for version-controlled workflows)

```
project-name/
├── CLAUDE.md
├── README.md                      # Setup and deployment instructions
├── workflows/
│   ├── main-workflow.json         # Exported n8n workflow JSON
│   └── sub-[name].json            # Sub-workflows
├── .env.example                   # Required credentials list
└── docs/
    └── workflow-diagram.png       # Screenshot for client documentation
```

## Export and Version Control

Always export workflows as JSON and commit them. In n8n:
- Download workflow → save to `workflows/[name].json`
- Commit after every meaningful change
- Tag versions before sharing with clients

## Credentials Needed

Document all required credentials in `.env.example` with descriptions:
```
# n8n Credentials (configure in n8n UI, not .env)
# GHL_API_KEY — GoHighLevel private integration key
# GOOGLE_OAUTH — Google OAuth app credentials
# ANTHROPIC_API_KEY — For AI nodes
```

Note: n8n stores credentials in its own encrypted store, not in `.env`.
The `.env.example` is for documentation purposes.

## Common Gotchas

- Webhook URLs change if you recreate a workflow — update them in source systems
- n8n Cloud has execution limits on lower plans
- Date/time handling is inconsistent — always use ISO 8601 and specify timezone
- Test with production credentials in a dev workflow, not the live one

# Claude Planner

A project planning system for an AI automation agency, built to run inside [Claude Code](https://claude.ai/claude-code). It guides you from client conversation to scaffolded project — without defaulting to any particular tech stack.

## What It Does

- Walks you through structured intake questions to understand the problem
- Scores complexity and recommends the right stack with tradeoffs explained
- Scaffolds a complete project directory with starter files written to disk
- Provides reusable integration patterns for common client work (GHL, AI images, webhooks, email, scheduled jobs)

## Prerequisites

- [Claude Code](https://claude.ai/claude-code) CLI installed
- Open this directory in a Claude Code session

## Usage

### 1. Analyze a new project

```
/analyze-project
```

Works through goal, trigger, output, complexity, and timeline — then recommends a stack and outputs a project brief.

### 2. Scaffold the project

```
/scaffold-project
```

Writes all starter files to disk at a path you specify. Generates a filled-in `CLAUDE.md`, config files, and directory structure for the chosen stack.

### 3. Add AI to an existing project

```
/add-ai-integration
```

Adds Vercel AI SDK integration (text generation, structured extraction, streaming, tool calling) to any stack.

---

## Stacks

| Stack | Use When |
|-------|----------|
| `typescript-workflow` | Code-first automation, complex logic, Trigger.dev jobs |
| `n8n-workflow` | Visual orchestration, client needs to see or modify the workflow |
| `nextjs-light` | Quick pitch/MVP with a UI, Vercel, no complex state |
| `nextjs-convex-app` | Full production app — auth, real-time data, background jobs |

Stack selection is driven by the `/analyze-project` intake, never assumed upfront.

## Integration Patterns

Copy-paste ready patterns for the most common agency integrations:

| Pattern | Covers |
|---------|--------|
| `patterns/ghl-integration.md` | GoHighLevel CRM — contacts, opportunities, webhooks |
| `patterns/ai-image-generation.md` | Image enhancement and generation (fal.ai, Replicate) |
| `patterns/webhook-triggers.md` | Inbound webhooks, signature verification, idempotency |
| `patterns/email-processing.md` | IMAP polling, inbound routing, AI extraction, outbound (Resend) |
| `patterns/scheduled-jobs.md` | Nightly jobs, morning digests, cron patterns |

## Project Structure

```
claude-planner/
├── CLAUDE.md                      # System instructions for Claude Code
├── README.md                      # This file
├── ROADMAP.md                     # Planned enhancements
├── analyze/
│   └── project-intake.md          # Decision framework and scoring guide
├── stacks/
│   ├── typescript-workflow/
│   ├── n8n-workflow/
│   ├── nextjs-light/
│   └── nextjs-convex-app/
├── patterns/
│   ├── ghl-integration.md
│   ├── ai-image-generation.md
│   ├── webhook-triggers.md
│   ├── email-processing.md
│   └── scheduled-jobs.md
└── .claude/
    └── commands/
        ├── analyze-project.md
        ├── scaffold-project.md
        └── add-ai-integration.md
```

## Roadmap

See [ROADMAP.md](ROADMAP.md) for planned additions including `/new-client` discovery skill, `/pre-launch` checklist, client handoff templates, and additional integration patterns.

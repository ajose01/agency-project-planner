# Claude Planner — AI Automation Agency Project System

This repo is the central planning system for new client projects. It is **stack-agnostic**.
You always start with analysis, never with a stack assumption.

## How to Use This System

### Starting a New Project
Run the intake skill:
```
/analyze-project
```
This walks through structured questions about the project goal, triggers, outputs, users,
timeline, and complexity — then recommends stack options with tradeoffs explained.

### Scaffolding After Analysis
Once you've chosen a stack:
```
/scaffold-project
```
Generates the starter project structure, CLAUDE.md, and key config files for the chosen stack.

### Adding AI to an Existing Project
```
/add-ai-integration
```
Adds AI SDK integration (streaming, tool use, multi-step) to any existing stack.

---

## Core Principle: Analyze First, Stack Second

Never default to a tool because it's familiar. Ask:
- What problem are we actually solving?
- Who triggers it and who consumes the output?
- What's the maintenance and iteration story?
- What's the timeline — pitch, MVP, or production?

A workflow that runs code is often simpler, more maintainable, and more type-safe than
a visual tool. A visual tool is often faster to demo and easier for a non-developer client
to understand. Neither is the default.

---

## Available Stacks

| Stack | Best When |
|-------|-----------|
| `typescript-workflow` | Code-first automations, complex logic, type safety matters, long-term maintenance |
| `n8n-workflow` | Visual orchestration, client needs to see/modify it, rapid demo, API glue work |
| `nextjs-light` | Quick client MVP / pitch demo, needs a UI, no complex state |
| `nextjs-convex-app` | Full app with real-time UI, auth, persistent state, AI-integrated |

See `stacks/` for detailed guides on each.

---

## Reusable Patterns

Common integration patterns that work across stacks:

- `patterns/ghl-integration.md` — GoHighLevel CRM (contacts, opportunities, webhooks)
- `patterns/ai-image-generation.md` — Image generation and enhancement workflows
- `patterns/webhook-triggers.md` — Inbound webhook handling patterns
- `patterns/email-processing.md` — Email ingestion and summarization
- `patterns/scheduled-jobs.md` — Nightly batch jobs, morning digests

---

## Additional Skills (Use When Needed)

Community-maintained skills are available at https://github.com/anthropics/skills

Only pull in a skill when the project actually calls for it. Do not add skills preemptively.

| Situation | Skill to check |
|-----------|---------------|
| Building a frontend (Next.js, React) | `frontend` skill |
| Other needs | Browse the repo for relevant skills |

To install a skill into a project, copy the relevant `.md` file into that project's `.claude/commands/` directory.

---

## Agency Context

**Client type:** Small businesses, often manual processes or disconnected apps.

**Common project patterns:**
- Connect two apps that don't talk to each other
- AI-augment an existing manual workflow (image enhancement, text extraction, summarization)
- Build a lightweight dashboard or digest for an operator/owner
- Automate CRM updates from field inputs (photos, emails, forms)

**Primary integrations encountered:**
- GoHighLevel (GHL) — primary CRM for most clients
- Google Drive / Sheets — document and data storage
- Email (IMAP/SMTP or Gmail API) — inbound triggers and outbound notifications
- AI providers (Anthropic, OpenAI, Replicate, fal.ai) — generation and processing
- Vercel — deployment for custom apps
- Convex — real-time backend for custom apps
- Trigger.dev — background jobs and scheduled tasks within code-first apps

---

## File Structure

```
claude-planner/
├── CLAUDE.md                     # This file
├── ROADMAP.md                    # Future enhancements and gaps
├── analyze/
│   └── project-intake.md         # Decision framework and scoring guide
├── stacks/
│   ├── typescript-workflow/       # Code-first automations
│   ├── n8n-workflow/              # Visual orchestration
│   ├── nextjs-light/              # Quick MVP / pitch
│   └── nextjs-convex-app/         # Full production app
├── patterns/
│   ├── ghl-integration.md
│   ├── ai-image-generation.md
│   ├── webhook-triggers.md
│   ├── email-processing.md
│   └── scheduled-jobs.md
└── .claude/
    └── commands/
        ├── analyze-project.md     # /analyze-project skill
        ├── scaffold-project.md    # /scaffold-project skill
        └── add-ai-integration.md  # /add-ai-integration skill
```

See `ROADMAP.md` for planned enhancements that would further accelerate agency work.

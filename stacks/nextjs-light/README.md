# Stack: Next.js Light (Quick MVP / Pitch)

A minimal Next.js app on Vercel. Ship a working UI fast. No complex state management,
no real-time, auth optional.

## When to Use This Stack

**Use this when:**
- The client needs to see something in a browser
- It's a pitch or proof-of-concept
- One or two people will use it (no multi-tenant auth required)
- Data doesn't need to be real-time
- You need to deploy and share a URL within a day

**Do NOT use this when:**
- Multiple clients or users need separate accounts
- Data needs to update live (use nextjs-convex-app)
- It will grow into a serious product (start with nextjs-convex-app instead)
- There's significant backend logic (use typescript-workflow + simple webhook endpoint)

## Core Stack

| Layer | Tool | Notes |
|-------|------|-------|
| Framework | Next.js 15 (App Router) | |
| Deployment | Vercel | One-command deploy |
| Styling | Tailwind CSS | |
| UI components | shadcn/ui | Add only what you need |
| Data storage | Vercel KV or JSON file | For simple state |
| Auth (optional) | Vercel Auth / Clerk | Only if needed |
| AI | Vercel AI SDK | `ai` + `@ai-sdk/anthropic` |
| Background jobs | Vercel Cron | For simple scheduled tasks |

For anything more complex than Vercel KV, upgrade to nextjs-convex-app instead.

## File Structure

```
project-name/
├── CLAUDE.md
├── package.json
├── next.config.ts
├── tailwind.config.ts
├── .env.local
├── .env.example
├── app/
│   ├── layout.tsx
│   ├── page.tsx              # Landing / main view
│   ├── globals.css
│   └── api/
│       └── [endpoint]/
│           └── route.ts      # API routes
├── components/
│   └── ui/                   # shadcn components
└── lib/
    ├── ai.ts                 # AI SDK helpers
    └── [integration].ts      # External API clients
```

## Frontend Skill

For building out the UI, check the `frontend` skill at https://github.com/anthropics/skills
Copy it into the project's `.claude/commands/` directory if you need it.

## Getting Started Fast

```bash
npx create-next-app@latest project-name --typescript --tailwind --app --no-src-dir
cd project-name
npx shadcn@latest init
npm install ai @ai-sdk/anthropic
```

Then deploy immediately:
```bash
npx vercel
```

## Keep It Light

For an MVP, resist the urge to add:
- Complex state management (useState is fine)
- A database (Vercel KV or static JSON first)
- Auth (unless the demo literally requires it)
- Tests (get it working, validate with client first)

These can be added when the concept is validated.

## Environment Variables

```
ANTHROPIC_API_KEY=
# Add others as needed
```

## Vercel Deployment

1. `npx vercel` for first deploy
2. Set env vars in Vercel dashboard
3. Every `git push` to main auto-deploys

## Upgrading to Full App

When the MVP gets approved and needs to grow:
- Add Convex for real-time data and auth: see `stacks/nextjs-convex-app/`
- Add Trigger.dev for background jobs: `npm install @trigger.dev/sdk`
- Keep the same Next.js app structure — just add layers

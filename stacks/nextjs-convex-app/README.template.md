# {{PROJECT_NAME}}

{{ONE_LINE_DESCRIPTION}}

**Client:** {{CLIENT_NAME}}

## Setup

```bash
npm install
cp .env.example .env.local
```

Fill in `.env.local`:

```
CONVEX_DEPLOYMENT=
NEXT_PUBLIC_CONVEX_URL=
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
ANTHROPIC_API_KEY=
{{OTHER_ENV_VARS}}
```

## Running Locally

Two terminals:

```bash
# Terminal 1 — Convex backend
npx convex dev

# Terminal 2 — Next.js frontend
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Deploying

```bash
npx convex deploy          # deploy Convex backend
npx vercel                 # deploy Next.js frontend
npx trigger.dev@latest deploy  # deploy background jobs (if applicable)
```

Set all environment variables in both the Vercel dashboard and the Convex dashboard.

## Integrations

{{LIST_INTEGRATIONS}}

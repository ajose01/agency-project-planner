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
{{ENV_VARS}}
```

## Running Locally

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Deploying

```bash
npx vercel
```

Set environment variables in the Vercel dashboard. Every push to `main` auto-deploys.

## Integrations

{{LIST_INTEGRATIONS}}

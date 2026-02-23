Scaffold a new project by writing all starter files directly to disk.

## Step 1 — Gather info

Ask me for all of the following in one message:
1. Which stack we're using (if not already established from a `/analyze-project` session):
   - `typescript-workflow` — code-first automation with Trigger.dev
   - `n8n-workflow` — visual orchestration
   - `nextjs-light` — quick MVP / pitch demo
   - `nextjs-convex-app` — full production app
2. Project name (will become the directory name and package name, use kebab-case)
3. Client name
4. One-sentence description of what it does
5. Which integrations are needed
6. Parent directory where the project should be created (e.g. `~/projects/clients` or `/Users/angeljose/projects/automations`). Default to `/Users/angeljose/projects` if not specified.

## Step 2 — Read the stack guide

Read `stacks/[chosen-stack]/README.md` and `stacks/[chosen-stack]/CLAUDE.template.md`.

## Step 3 — Write all files to disk

The project root will be `[parent-directory]/[project-name]/`.

Use the Write tool to create every file. Do not just print file contents — actually write them.

Files to create per stack:

**typescript-workflow:**
- `CLAUDE.md` (filled-in template)
- `package.json`
- `tsconfig.json`
- `trigger.config.ts`
- `src/jobs/.gitkeep`
- `src/lib/.gitkeep`
- `.env.example`
- `.gitignore`

**n8n-workflow:**
- `CLAUDE.md` (filled-in template)
- `README.md` with setup steps
- `workflows/.gitkeep`
- `.env.example`

**nextjs-light:**
- `CLAUDE.md` (filled-in template)
- `package.json`
- `next.config.ts`
- `tailwind.config.ts`
- `tsconfig.json`
- `app/layout.tsx`
- `app/page.tsx`
- `app/globals.css`
- `lib/.gitkeep`
- `.env.example`
- `.gitignore`

**nextjs-convex-app:**
- `CLAUDE.md` (filled-in template)
- `package.json`
- `next.config.ts`
- `tailwind.config.ts`
- `tsconfig.json`
- `app/layout.tsx`
- `app/page.tsx`
- `app/globals.css`
- `convex/schema.ts`
- `lib/.gitkeep`
- `trigger/.gitkeep`
- `.env.example`
- `.gitignore`

Keep all generated code minimal — skeleton and config only, no business logic.

## Step 4 — Confirm and next steps

After writing all files, output:
1. The full path to the created project
2. Which pattern files from `patterns/` are relevant for this project
3. A short numbered checklist of next steps to get running locally (install deps, fill .env, start dev server)

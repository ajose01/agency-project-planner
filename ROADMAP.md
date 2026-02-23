# Roadmap — Future Enhancements

This document tracks gaps identified after initial system launch. These are features and patterns
that would accelerate agency work but aren't critical for the current analyze → scaffold → build cycle.

## High Priority — Directly Accelerates Client Work

### 1. `/new-client` Skill
**Problem:** System starts at project analysis, but first you have a client discovery conversation.
No structured way to ask discovery questions, identify the real problem, and produce a proposal-ready brief.

**What it would do:**
- Walk through pre-project discovery questions (what do you do today? what's broken? why now?)
- Identify the core problem vs. symptoms
- Estimate rough complexity and timeline
- Output a one-page project brief ready for quoting
- Distinguish between "quick automation" and "needs to scope further"

**Acceptance criteria:**
- Takes a messy conversation and produces structured brief
- Outputs a client-facing summary and internal notes separately
- Suggests which `/analyze-project` stack recommendation makes sense given the discovery

---

### 2. `/pre-launch` Checklist Skill
**Problem:** Before handing off to client, you need to verify setup is production-ready.
Currently you have to remember all of this manually.

**What it would do:**
- Walks through per-stack launch checklist (env vars, monitoring, error handling, webhooks, etc.)
- Verifies credentials are actually valid (test the API key, webhook URL, etc.)
- Confirms error handling is in place (logging, alerts, retries)
- Checks that monitoring/alerting is wired up
- Verifies client documentation is complete
- Generates a sign-off report for the client

**Acceptance criteria:**
- Catches common pre-launch mistakes (missing env var, webhook not registered, no error handling)
- Can be run from any project directory
- Outputs a checklist and a "ready to deploy" confirmation

---

### 3. Client Handoff Document Template
**Problem:** When you hand off a completed project, the client needs to know how to run it,
who to contact, and what to do when it breaks. No template exists.

**What it would produce:**
- One-page operations guide per stack
- Links to relevant dashboards (Trigger.dev, Convex, Vercel, etc.)
- Common troubleshooting (what do I do if...?)
- Who owns what (credentials, admin access, support)
- Regular maintenance tasks (if any)
- Escalation path (who do I call?)
- Data backup and recovery procedures

**Format:** Markdown template with stack-specific variations

---

## Medium Priority — Fills Common Integration Gaps

### 4. Google Sheets / Google Drive Integration Pattern
**Problem:** Listed as primary integration in `CLAUDE.md` but no pattern file exists.
Very common across small business clients (data collection, reporting, file storage).

**What it would cover:**
- OAuth setup and refresh token handling
- Reading/writing Google Sheets (append rows, query, update)
- Google Drive folder/file operations (upload, download, move)
- Handling quota limits and rate limits
- Permission management and sharing
- Error handling for auth failures
- Cost estimation (free API limits vs. paid)

**Reference:** `patterns/google-integration.md`

---

### 5. Slack Integration Pattern
**Problem:** Slack is the most common notification output (job alerts, morning digests, status updates)
but no pattern for it.

**What it would cover:**
- Posting messages and threads
- Using Slack blocks for rich formatting
- Handling Slack rate limits
- Slash commands and message shortcuts
- Error handling (channel not found, permissions)
- Testing webhooks locally

**Reference:** `patterns/slack-integration.md`

---

## Lower Priority — Long-Term Operational Needs

### 6. Error Handling & Failure Recovery Pattern
**Problem:** Happy-path code exists but nothing on resilience.
What happens when an API fails? How do you retry? How does the client know?

**What it would cover:**
- Retry strategies (exponential backoff, max attempts, timing)
- Dead letter queues (what happens to failed jobs?)
- Alerting thresholds (when to notify vs. silently retry)
- Monitoring dashboards (Sentry, LogRocket, Vercel dashboards)
- Incident playbooks (what to do when something breaks)
- Health check patterns (how clients know the system is alive)

**Reference:** `patterns/error-handling.md`

---

### 7. `/cost-calculator` Skill or Guide
**Problem:** Clients always ask "what will this cost to run monthly?"
No way to estimate infrastructure costs for a given project.

**What it would estimate:**
- Trigger.dev execution minutes and pricing tier
- Convex storage/bandwidth tier
- Vercel usage (if applicable)
- AI API costs (Claude, fal.ai, Replicate) based on estimated volume
- n8n Cloud or self-hosted costs
- Total monthly + per-operation unit costs

**Output:** Cost breakdown and monthly projection clients can understand

---

### 8. Multi-Environment Setup Guide
**Problem:** No guidance on managing dev/staging/prod safely.

**What it would cover:**
- Separate Trigger.dev projects per environment
- n8n dev vs. production instance strategy
- Convex staging deployments
- Credential management across environments
- Testing against production-like data without breaking prod
- Rollback procedures per stack

**Reference:** Add to `stacks/[name]/README.md` or create `patterns/environments.md`

---

## Nice to Have — Quality & Sustainability

### 9. Testing Patterns & Templates
**Problem:** Skeleton code exists but no guidance on testing automation code.

**What it would provide:**
- Unit test patterns for API clients (mock the API responses)
- Integration test patterns for Trigger.dev jobs
- End-to-end test patterns (test against actual APIs in staging)
- Convex mutation testing examples
- CI/CD pipeline templates (.github/workflows)
- Coverage targets per stack

---

### 10. Data Compliance & Audit Template
**Problem:** Some clients care about data governance (GDPR, CCPA, audit trails).

**What it would cover:**
- Data retention policies per integration
- Audit logging (who accessed what, when)
- Access controls (who can change the workflow)
- Incident response (what to do if credentials leak)
- Compliance checklist (what to ask about during discovery)

---

## How to Prioritize

**Build next (highest ROI):**
1. `/new-client` — every engagement starts with this
2. `/pre-launch` — prevents embarrassing launch failures
3. Client Handoff Template — scales your agency (clients self-service)
4. Google Sheets pattern — fills a gap on almost every project
5. Slack pattern — completes the "notification outputs" picture

**Build later (once volume justifies):**
- Error handling pattern
- Cost calculator
- Testing patterns
- Compliance template
- Multi-env guide

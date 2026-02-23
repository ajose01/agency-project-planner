# Project Intake — Decision Framework

Use this framework during the `/analyze-project` skill or manually when planning a new project.
Work through each section in order. The scoring at the end drives the stack recommendation.

---

## Section 1: Project Goal

Answer in plain language first, then classify.

**Questions:**
1. What is the client doing manually today that this will replace or augment?
2. What does success look like — what changes in their day-to-day?
3. Who are the humans in the loop, if any?

**Classification:**
- [ ] Pure automation — runs without human interaction
- [ ] Human-triggered automation — a person kicks it off, then it runs
- [ ] Tool for a human — someone uses an interface regularly
- [ ] Internal ops tool — team-facing dashboard, digest, report

---

## Section 2: Trigger

What starts the process?

- [ ] **Inbound webhook** — another system calls us (form submit, CRM event, payment)
- [ ] **Schedule** — runs at a fixed time (nightly, hourly, weekly)
- [ ] **File/upload event** — user drops a file, photo is uploaded
- [ ] **Email received** — inbound email triggers a workflow
- [ ] **Manual / button** — someone clicks something in a UI
- [ ] **Message / chat** — Slack, SMS, WhatsApp trigger
- [ ] **Polling** — we check an external source periodically

---

## Section 3: Output

What does the system produce?

- [ ] **Data written somewhere** — CRM updated, spreadsheet row added, DB record created
- [ ] **Notification sent** — Slack message, email, SMS to a person
- [ ] **File created** — PDF, image, document generated or transformed
- [ ] **UI state updated** — dashboard refreshes, status changes live
- [ ] **AI-generated content** — text, image, structured data produced by model

---

## Section 4: Complexity Axes

Rate each axis 1–3:

| Axis | 1 (Simple) | 2 (Moderate) | 3 (Complex) |
|------|-----------|--------------|-------------|
| **Logic** | Linear, no branching | Some conditions/retries | Complex branching, state machine |
| **Integrations** | 1–2 APIs | 3–4 APIs | 5+ or deep integration with one |
| **AI involvement** | None or single call | Single call with processing | Chained, agentic, or multi-modal |
| **UI needed** | None | Simple read-only dashboard | Interactive, forms, auth |
| **Users** | Zero (fully automated) | 1–2 internal users | Multi-user or client-facing |
| **Real-time** | Batch is fine | Near real-time OK | Live updates required |
| **Iteration speed** | Set and forget | Occasional changes | Active ongoing development |
| **Client visibility** | Internal only | Client may review | Client will operate/modify |

**Total score:** _____ / 24

---

## Section 5: Timeline & Scope

- [ ] **Pitch / proof of concept** — need something working in 1–3 days to demonstrate value
- [ ] **Client MVP** — working solution in 1–2 weeks, may evolve
- [ ] **Production** — needs auth, error handling, monitoring, maintainability

---

## Section 6: Stack Recommendation Guide

### Score 8–12 + Pitch/MVP timeline
**Start with:** Consider n8n or a simple TypeScript script.
- If the client needs to see a visual workflow: **n8n**
- If you'll own it forever and want type safety: **TypeScript + Trigger.dev** (or standalone script)
- If it's 90% API calls with simple logic: either works — pick what's faster

### Score 8–12 + Production timeline
**Start with:** TypeScript workflow on **Trigger.dev** or a standalone service.
- Better error handling, retries, observability than n8n long-term
- Easier to version control and test

### Score 13–18 + No UI needed
**Start with:** **TypeScript + Trigger.dev**
- Complex logic benefits from code, not a visual editor
- Type safety catches integration issues early

### Score 13–18 + UI needed (simple)
**Start with:** **Next.js Light** (Next.js + Vercel + minimal backend)
- Ship a dashboard or simple form quickly
- Add Trigger.dev for background jobs if needed

### Score 19–24 OR multi-user OR real-time
**Start with:** **Next.js + Convex** (full app)
- Real-time reactivity built in
- Auth, file storage, background jobs all available
- Add AI SDK for AI features, Trigger.dev for heavy background work

---

## Section 7: Integration Inventory

Check off integrations needed and reference the patterns:

- [ ] GoHighLevel CRM → `patterns/ghl-integration.md`
- [ ] Google Drive / Sheets → standard Google API
- [ ] Email (inbound or outbound) → `patterns/email-processing.md`
- [ ] AI image generation → `patterns/ai-image-generation.md`
- [ ] AI text generation → `patterns/ai-image-generation.md` (same AI SDK patterns)
- [ ] Inbound webhooks → `patterns/webhook-triggers.md`
- [ ] Scheduled/nightly jobs → `patterns/scheduled-jobs.md`
- [ ] Other: _______________

---

## Section 8: Output — Project Brief

After working through the above, produce a brief with:

```
Project Name:
Client:
Classification: [automation | human-triggered | tool | ops-tool]
Trigger:
Output:
Complexity Score: /24
Timeline: [pitch | mvp | production]
Recommended Stack:
Alternative Stack (if applicable):
Tradeoff reason:
Integrations needed:
Reference patterns:
Next step: /scaffold-project
```

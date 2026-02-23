Add AI SDK integration to the current project.

First, ask me:
1. What stack is this project on? (Next.js, TypeScript script, n8n, other)
2. What do I need the AI to do? (text generation, image generation, structured extraction, streaming chat, tool/function calling, multi-step/agentic)
3. Which AI provider am I using or prefer? (Anthropic Claude, OpenAI, other — or "help me choose")
4. Is this a one-shot call, a streaming response, or a multi-step agent?

Then:

**If they need help choosing a provider**, recommend:
- Anthropic Claude (claude-sonnet-4-6 or claude-opus-4-6) for complex reasoning, long context, tool use
- Default to Claude for text tasks — it's what we know best

For image generation, ask whether they are anchoring on an existing photo or generating from scratch:

- **Anchoring on a real photo** (enhance a job site photo, add landscaping to a property) → needs img2img:
  - First choice: **fal.ai Flux img2img** — fast, low cost, preserves original structure well
  - Alternative: **OpenAI GPT-4o image edit** — good instruction-following, especially if client already uses OpenAI
  - Replicate if a specific model (ControlNet, inpainting) is needed
- **Generating a concept from scratch** (no source photo) → text-to-image:
  - **Google Imagen 3 Pro** — strong photorealistic renders
  - **OpenAI GPT-4o image gen** — good instruction-following
- **Google Imagen 3 Pro cannot do img2img** — do not suggest it for anchored enhancement workflows

See `patterns/ai-image-generation.md` for full setup code for each provider.

**For the integration itself**, use the Vercel AI SDK (`ai` package) for all custom app integrations. It provides:
- Unified interface across providers
- Built-in streaming
- Tool calling / function calling
- `generateText`, `streamText`, `generateObject` primitives

Do NOT use raw provider SDKs in custom apps unless there's a specific feature the AI SDK doesn't support.

**Generate the integration code:**

For `generateText` / `streamText`:
```typescript
import { generateText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

const { text } = await generateText({
  model: anthropic('claude-sonnet-4-6'),
  prompt: '...',
})
```

For `generateObject` (structured extraction):
```typescript
import { generateObject } from 'ai'
import { z } from 'zod'

const { object } = await generateObject({
  model: anthropic('claude-sonnet-4-6'),
  schema: z.object({ ... }),
  prompt: '...',
})
```

For streaming in a Next.js API route:
```typescript
import { streamText } from 'ai'
export async function POST(req: Request) {
  const { messages } = await req.json()
  const result = streamText({
    model: anthropic('claude-sonnet-4-6'),
    messages,
  })
  return result.toDataStreamResponse()
}
```

**Always output:**
1. The packages to install (`npm install ai @ai-sdk/anthropic`)
2. The `.env.example` additions needed (`ANTHROPIC_API_KEY=`)
3. The integration code for the specific use case
4. A note on error handling and rate limits if this is production

**For image generation specifically**, reference `patterns/ai-image-generation.md` — image providers (Imagen, GPT-4o image gen, fal.ai, Replicate) all use their own SDKs separate from the Vercel AI SDK.

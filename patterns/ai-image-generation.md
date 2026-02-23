# Pattern: AI Image Generation & Enhancement

Covers image generation and image-to-image enhancement. Common use case:
take a photo from a job site and generate an enhanced/visualized version
(e.g., adding landscaping, showing a finished result, proposing design options).

## The Primary Use Case: Anchored Enhancement

The most common pattern in this agency is **anchored image-to-image (img2img)**:
take the client's real photo (job site, property, before state) and enhance it with
AI visualizations — adding landscaping, showing a finished result, proposing design options —
while preserving the original composition and perspective.

This requires **img2img support**. Not all providers offer this.

## Choosing a Provider

| Provider | img2img? | Best For | Notes |
|----------|----------|----------|-------|
| **fal.ai (Flux img2img)** | ✅ Yes | Anchored enhancement of real photos | Primary choice for the anchor-and-enhance workflow. Fast, low cost, strong at preserving structure. |
| **OpenAI GPT-4o image edit** | ✅ Yes | Instruction-following edits on existing images | Good alternative, especially if client already uses OpenAI. |
| **Replicate** | ✅ Varies | Specific models (ControlNet, inpainting, etc.) | Use when you need precise control not available elsewhere. |
| **Google Imagen 3 Pro** | ❌ No | Text-to-image concept renders only | Good for generating standalone concepts, NOT for anchoring on a real photo. |

**Decision guide:**
- Enhancing/augmenting a real client photo → **fal.ai** (first choice) or **OpenAI image edit**
- Generating a standalone concept render from a text description → **Imagen 3 Pro** or **GPT-4o**
- Need ControlNet, inpainting, or a specific model → **Replicate**
- Client already paying for OpenAI → **GPT-4o image edit**

---

## Google Imagen 3 Pro (via Gemini API)

```bash
npm install @google/generative-ai
```

```typescript
// lib/imagen.ts
import { GoogleGenerativeAI } from '@google/generative-ai'

const genAI = new GoogleGenerativeAI(process.env.GOOGLE_API_KEY!)

async function generateImage(prompt: string): Promise<Buffer> {
  const model = genAI.getGenerativeModel({ model: 'imagen-3.0-generate-002' })

  const result = await model.generateImages({
    prompt,
    numberOfImages: 1,
    aspectRatio: '4:3',
  })

  const base64 = result.generatedImages[0].image.imageBytes
  return Buffer.from(base64, 'base64')
}
```

Example prompts for project visualization:
```
"Professional landscape design for a residential backyard, manicured lawn, stone pathway,
flower beds, photorealistic, daytime, natural lighting"

"Finished exterior renovation, fresh paint, updated landscaping, professional real estate photography"
```

Note: Imagen 3 does not support image-to-image. If you need to modify an existing photo,
use fal.ai instead.

---

## OpenAI GPT-4o Image Generation & Editing

```bash
npm install openai
```

### Text-to-Image (concept generation)

```typescript
// lib/openai-images.ts
import OpenAI from 'openai'

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY })

async function generateImage(prompt: string): Promise<string> {
  const response = await openai.images.generate({
    model: 'gpt-image-1',
    prompt,
    n: 1,
    size: '1536x1024',  // landscape
    quality: 'high',
  })

  return response.data[0].url!
}
```

### Image-to-Image (anchored enhancement)

```typescript
import fs from 'fs'

async function enhanceImage(imagePath: string, prompt: string): Promise<string> {
  const response = await openai.images.edit({
    model: 'gpt-image-1',
    image: fs.createReadStream(imagePath),  // original photo
    prompt,  // describe what to add/change
    n: 1,
    size: '1536x1024',
  })

  return response.data[0].url!
}
```

Note: `images.edit` expects a local file stream or a `File` object, not a URL.
Download the source image first if it comes from Google Drive or a remote URL.

---

## fal.ai (Flux) — Image-to-Image Enhancement

Best when you need to modify an existing photo (the main use case for job site photos).

```bash
npm install @fal-ai/client
```

```typescript
// lib/fal.ts
import { fal } from '@fal-ai/client'

fal.config({ credentials: process.env.FAL_KEY })

async function enhanceImage(imageUrl: string, prompt: string): Promise<string> {
  const result = await fal.subscribe('fal-ai/flux/dev/image-to-image', {
    input: {
      image_url: imageUrl,
      prompt,
      strength: 0.75,       // 0 = keep original, 1 = full generation
      num_images: 1,
      image_size: 'landscape_4_3',
    },
  })
  return result.data.images[0].url
}

// Upload a local file/buffer to fal temporary storage
async function uploadToFal(file: File | Buffer): Promise<string> {
  return fal.storage.upload(file)
}
```

---

## Replicate

```bash
npm install replicate
```

```typescript
// lib/replicate.ts
import Replicate from 'replicate'

const replicate = new Replicate({ auth: process.env.REPLICATE_API_TOKEN })

async function runModel(modelVersion: string, input: Record<string, unknown>) {
  const output = await replicate.run(modelVersion, { input })
  return output
}
```

---

## Handling Images from Google Drive / Upload

Images often come from Google Drive or a file upload. Steps:

1. Get the image as a URL or buffer
2. If sending to Imagen or GPT-4o: pass as base64 or URL directly
3. If sending to fal.ai: upload to fal temporary storage first

```typescript
// Download any image URL to a buffer
async function fetchImageBuffer(url: string): Promise<Buffer> {
  const res = await fetch(url)
  return Buffer.from(await res.arrayBuffer())
}
```

---

## Storing Results

Generated image URLs are **temporary** (expire within hours). Always:
1. Download immediately after generation
2. Store permanently in Convex file storage, Google Drive, or Cloudflare R2
3. Save the permanent URL to your database

```typescript
async function downloadAndStore(tempUrl: string): Promise<Buffer> {
  const res = await fetch(tempUrl)
  return Buffer.from(await res.arrayBuffer())
}
```

---

## Full Workflow: Photo → Enhanced Image → Store → Notify

```typescript
async function processProjectPhoto(
  photoUrl: string,
  projectId: string,
  enhancementType: 'landscape' | 'interior' | 'exterior'
) {
  const prompts = {
    landscape: 'Professional landscape design with lush greenery, stone pathways, photorealistic',
    interior: 'Modern interior design, clean lines, natural lighting, photorealistic',
    exterior: 'Freshly painted exterior, manicured lawn, professional photography',
  }

  // If modifying an existing photo, use fal img2img
  // If generating a concept from scratch, use Imagen 3 or GPT-4o
  const enhancedUrl = await enhanceImage(photoUrl, prompts[enhancementType])

  const imageBuffer = await downloadAndStore(enhancedUrl)

  // Store permanently (example: Convex)
  const storageId = await ctx.storage.store(
    new Blob([imageBuffer], { type: 'image/jpeg' })
  )

  await ctx.db.patch(projectId, { enhancedImageId: storageId })
  return storageId
}
```

---

## Environment Variables

```
GOOGLE_API_KEY=             # Google AI Studio API key (for Imagen 3)
OPENAI_API_KEY=             # OpenAI API key (for GPT-4o image gen)
FAL_KEY=                    # fal.ai API key (fal.ai/dashboard)
REPLICATE_API_TOKEN=        # Replicate API token (if using Replicate)
```

---

## Cost Estimates (approximate)

| Provider | Cost per image |
|----------|----------------|
| Imagen 3 Pro | ~$0.04 |
| GPT-4o image gen (high quality) | ~$0.07–0.19 |
| fal.ai Flux Dev | ~$0.025–0.05 |
| Replicate (model-dependent) | ~$0.01–0.05 |

For client projects, track usage and factor into project cost.

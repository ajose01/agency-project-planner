# Pattern: AI Image Generation & Enhancement

Covers image generation and image-to-image enhancement. Common use case:
take a photo from a job site and generate an enhanced/visualized version
(e.g., adding landscaping, showing a finished result, proposing design options).

## Choosing a Provider

| Provider | Best For | Notes |
|----------|----------|-------|
| **fal.ai** | Fast image gen, Flux models, good API | Preferred for most use cases |
| **Replicate** | Wide model selection, pay-per-use | Good for specific models not on fal |
| **OpenAI DALL-E 3** | Simple generation, no img2img | Limited control, higher cost |
| **Stability AI** | SDXL, inpainting, outpainting | More setup, direct API |

For the landscape/project visualization use case: **fal.ai with Flux** is the recommended starting point.

## fal.ai Setup

```bash
npm install @fal-ai/client
```

```typescript
// lib/fal.ts
import { fal } from '@fal-ai/client'

fal.config({ credentials: process.env.FAL_KEY })
```

### Image-to-Image (Enhancement)

```typescript
import { fal } from '@fal-ai/client'

async function enhanceImage(imageUrl: string, prompt: string) {
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
```

Example prompts for landscape/project visualization:
```
"Professional landscape design with manicured lawn, flower beds, and stone pathway,
photorealistic, daytime, high quality"

"Finished backyard with pergola, outdoor furniture, and landscaping,
photorealistic rendering, natural lighting"
```

### Text-to-Image (Concept Generation)

```typescript
async function generateConcept(prompt: string) {
  const result = await fal.subscribe('fal-ai/flux/dev', {
    input: {
      prompt,
      num_images: 3,           // Generate multiple options
      image_size: 'landscape_4_3',
    },
  })
  return result.data.images.map(img => img.url)
}
```

## Replicate Setup

```bash
npm install replicate
```

```typescript
// lib/replicate.ts
import Replicate from 'replicate'
const replicate = new Replicate({ auth: process.env.REPLICATE_API_TOKEN })

async function enhanceImageReplicate(imageUrl: string, prompt: string) {
  const output = await replicate.run(
    'stability-ai/sdxl:...',  // pin a specific version
    {
      input: {
        image: imageUrl,
        prompt,
        strength: 0.7,
      }
    }
  )
  return output[0] // URL to generated image
}
```

## Handling Images from Google Drive / Upload

Images often come from Google Drive or a file upload. Steps:

1. Get the image as a URL or buffer
2. If it's a local file/buffer, upload to a temporary URL before sending to fal/Replicate
3. fal.ai provides a storage upload utility:

```typescript
import { fal } from '@fal-ai/client'

// Upload a local file to fal storage (returns a temporary URL)
async function uploadToFal(file: File | Buffer, fileName: string): Promise<string> {
  const url = await fal.storage.upload(file)
  return url
}
```

## Storing Results

Generated image URLs from fal/Replicate are **temporary** (expire in 24h). Always:
1. Download the image immediately after generation
2. Store it in Convex file storage, Google Drive, or S3/Cloudflare R2
3. Save the permanent URL to your database

```typescript
async function downloadAndStore(tempUrl: string): Promise<Buffer> {
  const res = await fetch(tempUrl)
  return Buffer.from(await res.arrayBuffer())
}
```

## Full Workflow: Photo → Enhanced Image → Store → Notify

```typescript
// Typical flow for project photo enhancement
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

  // 1. Enhance with AI
  const enhancedUrl = await enhanceImage(photoUrl, prompts[enhancementType])

  // 2. Download result (temp URL)
  const imageBuffer = await downloadAndStore(enhancedUrl)

  // 3. Store permanently (example: Convex)
  const storageId = await ctx.storage.store(
    new Blob([imageBuffer], { type: 'image/jpeg' })
  )

  // 4. Update project record
  await ctx.db.patch(projectId, { enhancedImageId: storageId })

  return storageId
}
```

## Environment Variables

```
FAL_KEY=                    # fal.ai API key (get at fal.ai/dashboard)
REPLICATE_API_TOKEN=        # Replicate API token (if using Replicate)
OPENAI_API_KEY=             # If using DALL-E
```

## Cost Estimates (approximate)

| Provider | Cost per image |
|----------|----------------|
| fal.ai Flux Dev | ~$0.025–0.05 |
| Replicate SDXL | ~$0.01–0.04 |
| DALL-E 3 | ~$0.04–0.08 |

For client projects, track usage and factor into project cost.

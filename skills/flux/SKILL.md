---
name: flux
description: Image generation/editing with Flux API (by Black Forest Labs) via the Pixazo API. TRIGGER when the user mentions "Flux" or "Flux API", or when the user asks to generate / make / create / edit / restyle an image and Flux is named or implied. DO NOT TRIGGER for video / music / voice / 3d / try-on — each has its own skill.
---

# Flux API

Flux is a state-of-the-art text-to-image model that generates high-quality images from text descriptions.

You can ask Flux to handle image generation/editing. Powered by Black Forest Labs via the Pixazo API gateway.

---

## How to use this skill (agent instructions)

When this skill triggers, follow this script. The user should only ever have to give you their API key once.

### Step 1 — Make sure you have a Pixazo API key

Check for the API key in this order:
1. `PIXAZO_API_KEY` environment variable
2. `~/.pixazo/api-key` file (one line, just the key)
3. The current conversation (user pasted it earlier)

If none of those have it, say to the user, verbatim:

> I'll need your Pixazo API key once. Get it free at **https://api-console.pixazo.ai/api_keys** — sign in, click "Create new key", copy it, and paste it here. I'll save it to `~/.pixazo/api-key` so you only do this once.

When they paste the key, save it to `~/.pixazo/api-key` (`chmod 600`) and proceed. Never ask twice.

### Step 2 — Pick the right operation

| Version | Operation | apiId / operationId |
|---|---|---|
| Flux 2 Pro | Image to Image (Image Editing) | `flux-2-pro-image-to-image-866` / `flux-2-pro-image-to-image-request` |
| Flux 2 Pro | Image to Image (LoRA Trainer) | `flux-2-pro-image-to-image-trainer-831` / `flux-2-pro-image-to-image-trainer-request` |
| Flux 2 Pro | Text to Image | `flux-2-pro-text-to-image-799` / `flux-2-pro-text-to-image-request` |
| Flux 2 Pro | Image to Image (LoRA Trainer) | `flux-2-pro-text-to-image-trainer-712` / `flux-2-pro-text-to-image-trainer-request` |
| Flux Kontext | Image Restoration | `flux-kontext-restore-image` / `image-restore-request` |
| Flux 2 Klein | Text to Image | `flux-2-klein-4b` / `text-to-image` |
| Flux 2 Dev | Text to Image | `flux-2-dev` / `generatet2i` |
| Flux Pro 1.1 | Text to Image | `flux-pro-1-1` / `pro1-1-ultra-generaterequest` |
| Flux 1 Schnell | Text to Image | `flux-1-schnell` / `get-image` |
| Flux 1 Schnell | Text to Image (Batch) | `flux-1-schnell` / `get-image-batch` |
| Flux Pro | Text to Image | `flux-pro` / `text-to-image` |
| Flux Dev | Image to Image (Image Editing) | `flux-dev` / `image-to-image` |
| Flux Dev | Text to Image | `flux-dev` / `text-to-image` |
| Flux Fill Dev | Image to Image (Image Editing — Inpainting) | `flux-fill-dev` / `image-generation-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/flux-2-pro-image-to-image-866/v1/flux-2-pro-image-to-image-request`
- `POST https://gateway.pixazo.ai/flux-2-pro-image-to-image-trainer-831/v1/flux-2-pro-image-to-image-trainer-request`
- `POST https://gateway.pixazo.ai/flux-2-pro-text-to-image-799/v1/flux-2-pro-text-to-image-request`
- `POST https://gateway.pixazo.ai/flux-2-pro-text-to-image-trainer-712/v1/flux-2-pro-text-to-image-trainer-request`
- `POST https://gateway.pixazo.ai/flux-kontext-restore-image/v1/flux-kontext-restore-image/generate`
- `POST https://gateway.pixazo.ai/flux-2-klein-4b/v1/generateImage`
- `POST https://gateway.pixazo.ai/generateT2I`
- `POST https://gateway.pixazo.ai/pro1.1/v1/pro1.1ultra/generateRequest`
- `POST https://gateway.pixazo.ai/flux-1-schnell/v1/getData`
- `POST https://gateway.pixazo.ai/flux-1-schnell/v1/getDataBatch`
- `POST https://gateway.pixazo.ai/flux-1-schnell/v1/checkStatus`
- `POST https://gateway.pixazo.ai/flux-pro/v1/pro/textToImage`
- `POST https://gateway.pixazo.ai/flux-dev/v1/dev/imageToImage`
- `POST https://gateway.pixazo.ai/flux-dev/v1/dev/textToImage`
- `POST https://gateway.pixazo.ai/flux-fill-dev/v1/flux-fill/generate`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/flux-2-pro-text-to-image-799/v1/flux-2-pro-text-to-image-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "Place realistic flames emerging from the top of the coffee cup, dancing above the rim",
  "image_size": "auto",
  "safety_tolerance": "2",
  "enable_safety_checker": true,
  "output_format": "jpeg",
  "image_urls": [
    "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/nano-banana/nano-banana-a382a80b-f8df-4de1-a0c1-a5dcfd42dae4-1758783383399.jpg"
  ]
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/flux-2-pro-text-to-image-799/v1/flux-2-pro-text-to-image-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "Place realistic flames emerging from the top of the coffee cup, dancing above the rim",
  "image_size": "auto",
  "safety_tolerance": "2",
  "enable_safety_checker": true,
  "output_format": "jpeg",
  "image_urls": [
    "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/nano-banana/nano-banana-a382a80b-f8df-4de1-a0c1-a5dcfd42dae4-1758783383399.jpg"
  ]
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/flux-2-pro-text-to-image-799/v1/flux-2-pro-text-to-image-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "Place realistic flames emerging from the top of the coffee cup, dancing above the rim",
  "image_size": "auto",
  "safety_tolerance": "2",
  "enable_safety_checker": true,
  "output_format": "jpeg",
  "image_urls": [
    "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/nano-banana/nano-banana-a382a80b-f8df-4de1-a0c1-a5dcfd42dae4-1758783383399.jpg"
  ]
}),
});
console.log(await res.json());
```

### Step 4 — Show the user the result

image generation/editing via this model is **synchronous** — no polling. The response is JSON, e.g.:

```json
{ "images": [{ "url": "https://…" }] }
```

Pull the URL out and show it to the user (in chat, render inline if your environment supports it). Offer to: download it, edit it further, or generate variations.


---

### Inputs the user might give you

- **Prompt only** — a description. Build the request from Step 3.
- **A reference image** — passed as a URL or base64 data URL. Use the edit endpoint.
- **Image size** — translate user phrases to the API's `image_size` enum:
  - "square / Instagram" → `square_hd`
  - "portrait / vertical / 9:16" → `portrait_16_9`
  - "landscape / horizontal / 16:9" → `landscape_16_9`
- **Number of variations** — `num_images` (1–4). Default 1.
- **Seed** — for reproducibility. Default 42, or pass through if the user says "same seed".


---

## When something goes wrong

| Status | Meaning | Tell the user |
|---|---|---|
| 401 | API key missing/wrong | "Your Pixazo API key looks invalid. Get a fresh one at https://api-console.pixazo.ai/api_keys" |
| 402 | Out of credits | "Your Pixazo account is out of credits. Top up at https://api-console.pixazo.ai/dashboard" |
| 429 | Rate limited | "Pixazo is rate-limiting — let me wait 5s and retry." (do it once) |
| 4xx with `"error"` in body | Validation issue (e.g. bad prompt or missing field) | Show the error message verbatim and suggest a fix |
| 5xx | Transient | "Pixazo had a hiccup — let me retry." (one retry, then surface the error) |

---

## Cost

Per-call cost varies by model and resolution. The user can check their balance and per-call rate at https://api-console.pixazo.ai/dashboard.

---

## Full reference

For complete schemas, every parameter, error codes, and per-version differences:

> **Fetch:** `https://www.pixazo.ai/models/flux.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/flux`.

---

## Related Pixazo skills

- **Other image generation/editing models:** `seedream`, `gpt-image`, `ideogram`, `longcat-image`, `nano-banana`, `pixelforge`, `qwen-image`, `recraft`, `reve-image`, `studio-ghibli`, `auraflow`, `z-image`, `bria`, `sdxl`, `firered-image-edit`, `codeformer`, `gfpgan`, `smart-resize`, `nucleus`, `glm-image`, `hidream`, `ernie-image`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

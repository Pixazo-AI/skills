---
name: nano-banana
description: Image generation/editing with Nano Banana API (by Google) via the Pixazo API. TRIGGER when the user mentions "Nano Banana" or "Nano Banana API", or when the user asks to generate / make / create / edit / restyle an image and Nano Banana is named or implied. DO NOT TRIGGER for video / music / voice / 3d / try-on — each has its own skill.
---

# Nano Banana API

Advanced image generation and editing by Google.

You can ask Nano Banana to handle image generation/editing. Powered by Google via the Pixazo API gateway.

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
| Nano Banana 2 | Image to Image (Image Editing) | `nano-banana-2` / `image-request` |
| Nano Banana Standard | Image to Image (Image Editing) | `nano-banana` / `edit-image-request` |
| Nano Banana Standard | Text to Image | `nano-banana` / `text-to-image-request` |
| Nano Banana Standard | Image to Image (Image Editing — Batch) | `nano-banana-async-api` / `image-to-image-edit-image` |
| Nano Banana Pro | Image to Image (Image Editing) | `nano-banana-pro-770` / `nano-banana-pro-request` |
| Nano Banana Pro | Image to Image (Image Editing — Batch) | `nano-banana-pro-async-api` / `image-to-image` |
| Nano Banana Pro | Text to Image (Batch) | `nano-banana-pro-async-api` / `text-to-image` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/nano-banana-2/v1/nano-banana-2/generate`
- `POST https://gateway.pixazo.ai/nano-banana/v1/nano-banana/generateEditImageRequest`
- `POST https://gateway.pixazo.ai/nano-banana/v1/nano-banana/generateTextToImageRequest`
- `POST https://gateway.pixazo.ai/nano-banana-async/v1/nano-banana-image-to-image`
- `POST https://gateway.pixazo.ai/nano-banana-pro-770/v1/nano-banana-pro-request`
- `POST https://gateway.pixazo.ai/nano-banana-pro-async/v1/nano-banana-pro-image-to-image`
- `POST https://gateway.pixazo.ai/nano-banana-pro-async/v1/nano-banana-pro-text-to-image`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/nano-banana-pro-async/v1/nano-banana-pro-text-to-image' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "Photorealistic DSLR portrait of a man wearing a black cap, standing in a lush green park with trees and soft sunlight, natural lighting, realistic skin texture, shallow depth of field, ultra-detailed, no stylization.",
  "aspect_ratio": "1:1"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/nano-banana-pro-async/v1/nano-banana-pro-text-to-image",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "Photorealistic DSLR portrait of a man wearing a black cap, standing in a lush green park with trees and soft sunlight, natural lighting, realistic skin texture, shallow depth of field, ultra-detailed, no stylization.",
  "aspect_ratio": "1:1"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/nano-banana-pro-async/v1/nano-banana-pro-text-to-image', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "Photorealistic DSLR portrait of a man wearing a black cap, standing in a lush green park with trees and soft sunlight, natural lighting, realistic skin texture, shallow depth of field, ultra-detailed, no stylization.",
  "aspect_ratio": "1:1"
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

> **Fetch:** `https://www.pixazo.ai/models/nano-banana.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/nano-banana`.

---

## Related Pixazo skills

- **Other image generation/editing models:** `seedream`, `flux`, `gpt-image`, `ideogram`, `longcat-image`, `pixelforge`, `qwen-image`, `recraft`, `reve-image`, `studio-ghibli`, `auraflow`, `z-image`, `bria`, `sdxl`, `firered-image-edit`, `codeformer`, `gfpgan`, `smart-resize`, `nucleus`, `glm-image`, `hidream`, `ernie-image`, `mirelo`, `real-esrgan`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

---
name: image-dpi-api
description: Image generation/editing with Image DPI API (by Pixazo) via the Pixazo API. TRIGGER when the user mentions "Image DPI" or "Image DPI API", or when the user asks to generate / make / create / edit / restyle an image and Image DPI is named or implied. DO NOT TRIGGER for video / music / voice / 3d / try-on — each has its own skill.
---

# Image DPI API

Prepare an image for print. Send image_url with a dpi between 72 and 1200 (300 by default) and the file is re-tagged at its own pixel size, keeping the source format for PNG and JPEG — PNG is re-tagged by copying the metadata chunk, so the pixels are never decoded and never re-compressed. Add a size preset (4x6, 5x7, 8x10, 11x14, 12x18, 18x24, 24x36, a5, a4, a3, a2, letter, tabloid) or width_in / height_in to target a physical size instead, and allow_upsample to permit enlarging. Anything other than the native re-tag comes back as TIFF unless you pass format. An image with too few pixels for the size you asked for is rejected rather than silently stretched.

You can ask Image DPI to handle image generation/editing. Powered by Pixazo via the Pixazo API gateway.

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
| Image DPI 1.0 | Prepare for Print | `media-image-dpi` / `media-image-dpi-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/media-tools/v1/image-dpi`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/media-tools/v1/image-dpi' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "image_url": "https://api-assets.pixazo.ai/media-api-test/sample.png",
  "dpi": 300,
  "size": "8x10",
  "format": "tiff"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/media-tools/v1/image-dpi",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "image_url": "https://api-assets.pixazo.ai/media-api-test/sample.png",
  "dpi": 300,
  "size": "8x10",
  "format": "tiff"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/media-tools/v1/image-dpi', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "image_url": "https://api-assets.pixazo.ai/media-api-test/sample.png",
  "dpi": 300,
  "size": "8x10",
  "format": "tiff"
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

> **Fetch:** `https://www.pixazo.ai/models/image-dpi-api.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/image-dpi-api`.

---

## Related Pixazo skills

- **Other image generation/editing models:** `seedream`, `gpt-image`, `grok-imagine-image`, `ideogram`, `longcat-image`, `nano-banana`, `pixelforge`, `qwen-image`, `recraft`, `reve-image`, `stable-diffusion`, `studio-ghibli`, `auraflow`, `z-image`, `bria`, `sdxl`, `firered-image-edit`, `codeformer`, `gfpgan`, `smart-resize`, `nucleus`, `glm-image`, `hidream`, `ernie-image`, `mirelo`, `real-esrgan`, `mai-image`, `pixelcut`, `krea`, `boogu-image`, `whisper`, `assemblyai`, `separate-stems-api`, `diarize-api`, `video-convert-api`, `video-crop-api`, `video-resize-api`, `video-speed-api`, `video-trim-api`, `video-cut-api`, `video-merge-api`, `video-transition-api`, `video-compress-api`, `video-gif-api`, `video-frame-api`, `video-audio-remover-api`, `audio-normalize-api`, `audio-denoise-api`, `audio-slice-api`, `audio-extract-api`, `video-replace-audio-api`, `media-probe-api`, `image-convert-api`, `image-vectorize-api`, `image-extender-api`, `content-safety-api`, `muse-image`, `muse-voice`, `image-to-video-api`, `image-compose-api`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

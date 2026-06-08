---
name: ideogram
description: Image generation/editing with Ideogram AI API (by Ideogram) via the Pixazo API. TRIGGER when the user mentions "Ideogram" or "Ideogram AI API", or when the user asks to generate / make / create / edit / restyle an image and Ideogram is named or implied. DO NOT TRIGGER for video / music / voice / 3d / try-on — each has its own skill.
---

# Ideogram AI API

Advanced image generation with text rendering capabilities.

You can ask Ideogram to handle image generation/editing. Powered by Ideogram via the Pixazo API gateway.

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
| Ideogram v4 | Text to Image | `ideogram-v4` / `ideogram-v4-request` |
| Ideogram Remove Background | Image to Image (Image Background Removal) | `ideogram-remove-background` / `ideogram-remove-background-request` |
| Ideogram v2 | Text to Image | `ideogram-generate` / `get-image` |
| Ideogram v2 | Utility (Image to Text — Captioning) | `ideogram-generate` / `describe-image` |
| Ideogram v2 | Image to Image (Image Editing) | `ideogram-generate` / `edit-image` |
| Ideogram v2 | Image to Image (Image Editing — Remix) | `ideogram-generate` / `remix-image` |
| Ideogram Turbo | Text to Image | `ideogram-image-turbo` / `create-image` |
| Ideogram Turbo | Utility (Image to Text — Captioning) | `ideogram-image-turbo` / `describe-image` |
| Ideogram Turbo | Image to Image (Image Editing) | `ideogram-image-turbo` / `edit-image` |
| Ideogram Turbo | Image to Image (Image Editing — Remix) | `ideogram-image-turbo` / `remix-image` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/ideogram-v4/v1/ideogram-v4-request`
- `POST https://gateway.pixazo.ai/ideogram-remove-background/v1/ideogram-remove-background-request`
- `POST https://gateway.pixazo.ai/v2/requests/status/ideogram-remove-background_019dxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
- `POST https://gateway.pixazo.ai/ideogramV_2/v1/generate`
- `POST https://gateway.pixazo.ai/ideogramV_2/v1/describe`
- `POST https://gateway.pixazo.ai/ideogramV_2/v1/edit`
- `POST https://gateway.pixazo.ai/ideogramV_2/v1/remix`
- `POST https://gateway.pixazo.ai/ideogramV_2_Turbo/v1/generate`
- `POST https://gateway.pixazo.ai/ideogramV_2_Turbo/v1/describe`
- `POST https://gateway.pixazo.ai/ideogramV_2_Turbo/v1/edit`
- `POST https://gateway.pixazo.ai/ideogramV_2_Turbo/v1/remix`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/ideogram-v4/v1/ideogram-v4-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "A vintage travel poster of Kyoto in autumn, bold hand-painted lettering reading KYOTO across the top",
  "num_images": 1,
  "image_size": "square_hd",
  "output_format": "jpeg",
  "rendering_speed": "BALANCED",
  "acceleration": "none",
  "enable_prompt_expansion": true,
  "enable_safety_checker": true,
  "sync_mode": false
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/ideogram-v4/v1/ideogram-v4-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "A vintage travel poster of Kyoto in autumn, bold hand-painted lettering reading KYOTO across the top",
  "num_images": 1,
  "image_size": "square_hd",
  "output_format": "jpeg",
  "rendering_speed": "BALANCED",
  "acceleration": "none",
  "enable_prompt_expansion": true,
  "enable_safety_checker": true,
  "sync_mode": false
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/ideogram-v4/v1/ideogram-v4-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "A vintage travel poster of Kyoto in autumn, bold hand-painted lettering reading KYOTO across the top",
  "num_images": 1,
  "image_size": "square_hd",
  "output_format": "jpeg",
  "rendering_speed": "BALANCED",
  "acceleration": "none",
  "enable_prompt_expansion": true,
  "enable_safety_checker": true,
  "sync_mode": false
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

> **Fetch:** `https://www.pixazo.ai/models/ideogram.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/ideogram`.

---

## Related Pixazo skills

- **Other image generation/editing models:** `seedream`, `gpt-image`, `grok-imagine-image`, `longcat-image`, `nano-banana`, `pixelforge`, `qwen-image`, `recraft`, `reve-image`, `stable-diffusion`, `studio-ghibli`, `auraflow`, `z-image`, `bria`, `sdxl`, `firered-image-edit`, `codeformer`, `gfpgan`, `smart-resize`, `nucleus`, `glm-image`, `hidream`, `ernie-image`, `mirelo`, `real-esrgan`, `mai-image`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

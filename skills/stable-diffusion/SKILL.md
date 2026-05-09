---
name: stable-diffusion
description: Video generation with Stable Diffusion API (by Stability AI) via the Pixazo API. TRIGGER when the user mentions "Stable Diffusion" or "Stable Diffusion API", or when the user asks to generate / make / create a video / clip / animation and Stable Diffusion is named or implied. DO NOT TRIGGER for image / music / voice / 3d / try-on — each has its own skill.
---

# Stable Diffusion API

Industry-leading image and video generation models.

You can ask Stable Diffusion to handle video generation. Powered by Stability AI via the Pixazo API gateway.

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
| Stable Diffusion v3 | Text to Image | `stable-diffusion-3` / `get-data` |
| Stable Diffusion v3.5 | Text to Image | `stable-diffusion-3-5-large` / `get-image` |
| Stable Diffusion XL v1.0 | Text to Image | `stable-diffusion-xl-base-10` / `get-image` |
| Stable Diffusion XL Lightning | Text to Image | `stable-diffusion-xl-lightning` / `get-image` |
| Stable Diffusion XL Lightning | Text to Image (Stream) | `stable-diffusion-xl-lightning` / `get-image-stream` |
| Stable Diffusion Inpainting | Image to Image (Inpainting — Ref Image + Ref Mask to Image) | `stable-diffusion-v1-5-inpainting` / `get-image` |
| Stable Diffusion Inpainting | Text to Image (Stream) | `stable-diffusion-v1-5-inpainting` / `get-image-stream` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/sd3/v1/getData`
- `POST https://gateway.pixazo.ai/sd3-5/v1/r-sd-3-5-large`
- `POST https://gateway.pixazo.ai/getImage/v1/getSDXLImage`
- `POST https://gateway.pixazo.ai/sdxl_lightning/getImage/v1/getSDXLImage`
- `POST https://gateway.pixazo.ai/sdxl_lightning/getImage/v1/getSDXLImageStream`
- `POST https://gateway.pixazo.ai/inpainting/v1/getImage`
- `POST https://gateway.pixazo.ai/inpainting/v1/getImageStream`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/sd3/v1/getData' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "Picture a sleek, futuristic car racing through a neon-lit cityscape, its engine humming efficiently as it blurs past digital billboards. The driver skillfully navigates the glowing streets, aiming for victory in this high-tech, adrenaline-fueled race of tomorrow.",
  "negativePrompt": "dark, blurry",
  "steps": 28,
  "cfg": 4.0,
  "aspect_ratio": "3:2",
  "output_format": "jpg",
  "output_quality": 90,
  "prompt_strength": 0.85
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/sd3/v1/getData",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "Picture a sleek, futuristic car racing through a neon-lit cityscape, its engine humming efficiently as it blurs past digital billboards. The driver skillfully navigates the glowing streets, aiming for victory in this high-tech, adrenaline-fueled race of tomorrow.",
  "negativePrompt": "dark, blurry",
  "steps": 28,
  "cfg": 4.0,
  "aspect_ratio": "3:2",
  "output_format": "jpg",
  "output_quality": 90,
  "prompt_strength": 0.85
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/sd3/v1/getData', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "Picture a sleek, futuristic car racing through a neon-lit cityscape, its engine humming efficiently as it blurs past digital billboards. The driver skillfully navigates the glowing streets, aiming for victory in this high-tech, adrenaline-fueled race of tomorrow.",
  "negativePrompt": "dark, blurry",
  "steps": 28,
  "cfg": 4.0,
  "aspect_ratio": "3:2",
  "output_format": "jpg",
  "output_quality": 90,
  "prompt_strength": 0.85
}),
});
console.log(await res.json());
```

### Step 4 — Poll until ready, then show the user

Video generation is **asynchronous**. The first response returns a `task_id` (or `request_id`). Then poll a status endpoint until the video is ready.

Typical loop:

```python
import time, requests, os

KEY = os.environ["PIXAZO_API_KEY"]
HEADERS = {"Ocp-Apim-Subscription-Key": KEY, "Content-Type": "application/json"}

# 1) Submit
submit = requests.post("https://gateway.pixazo.ai/sd3/v1/getData", headers=HEADERS, json={...}).json()
task_id = submit.get("task_id") or submit.get("request_id") or submit.get("id")

# 2) Poll (every 5–10s; total cap ~10 min for video, ~3 min for music)
while True:
    status = requests.get(f"https://gateway.pixazo.ai/sd3/v1/result/{task_id}", headers=HEADERS).json()
    if status.get("status") in ("completed", "succeeded", "ready", "done"):
        break
    if status.get("status") in ("failed", "error"):
        raise RuntimeError(status.get("error") or "generation failed")
    time.sleep(8)

# 3) Pull the result URL out (field varies — usually output_url, video_url, audio_url, or url)
result_url = status.get("output_url") or status.get("video_url") or status.get("audio_url") or status.get("url")
```

The exact polling endpoint and "done" status string vary by model — fetch the full reference for this model's polling shape:

> **Fetch:** `https://www.pixazo.ai/models/stable-diffusion.md`

Show the result URL to the user when ready (offer to download, share, or generate variations).



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

> **Fetch:** `https://www.pixazo.ai/models/stable-diffusion.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/stable-diffusion`.

---

## Related Pixazo skills

- **Other video generation models:** `happy-horse`, `p-video`, `seedance`, `sora`, `veo`, `runway`, `kling`, `pika`, `higgsfield`, `genflare`, `omnihuman`, `lucy-edit`, `grok-imagine`, `ltx`, `luma`, `hailuo`, `mochi`, `veed`, `vidu`, `wan`, `pixverse`, `kandinsky`, `hunyuan-video`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

---
name: kling
description: Video generation with Kling AI API (by Kuaishou) via the Pixazo API. TRIGGER when the user mentions "Kling" or "Kling AI API", or when the user asks to generate / make / create a video / clip / animation and Kling is named or implied. DO NOT TRIGGER for image / music / voice / 3d / try-on — each has its own skill.
---

# Kling AI API

Advanced video generation and AI avatar capabilities.

You can ask Kling to handle video generation. Powered by Kuaishou via the Pixazo API gateway.

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
| Kling Image O3 | Text to Image | `kling-image` / `kling-image-request` |
| Kling Image O3 | Image to Image (Image Editing) | `kling-image-o3-i2i` / `kling-image-o3-i2i-request` |
| Kling Image V3 t2i | Text to Image | `kling-image-t2i` / `kling-image-t2i-request` |
| Kling Image V3 t2i | Text To Image | `kling-image-t2i` / `kling-image-t2i-request` |
| Kling 3.0 | Text to Video | `kling-3-0-text-to-video-standard` / `kling-3-0-text-to-video-standard-request` |
| Kling 3.0 | Image to Video | `kling-3-0-image-to-video-standard` / `kling-3-0-image-to-video-standard-request` |
| Kling Avatar v2 Pro | Audio to Video (Ref Image + Ref Audio to Video — Avatar) | `kling-ai-avatar-v2-pro-789` / `kling-ai-avatar-v2-pro-request` |
| Kling O1 | Video to Video (Video Editing) | `kling-o1-edit-video-video-to-video-634` / `kling-o1-edit-video-video-to-video-request` |
| Kling O1 | Image to Video (First/Last Frame to Video) | `kling-o1-first-frame-last-frame-to-video-857` / `kling-o1-first-frame-last-frame-to-video-request` |
| Kling O1 | Image to Image | `kling-o1-image-208` / `kling-o1-image-request` |
| Kling O1 | Reference to Video (Ref Images to Video) | `kling-o1-reference-image-to-video-382` / `kling-o1-reference-image-to-video-request` |
| Kling O1 | Reference to Video (Ref Video to Video) | `kling-o1-reference-video-to-video-315` / `kling-o1-reference-video-to-video-request` |
| Kling v2.6 Standard | Video to Video (Video Editing — Motion Control) | `kling-video-v2-6-standard-motion-control` / `kling-video-v2-6-standard-motion-control-request` |
| Kling v1 | Image to Image (Virtual Try On) | `kling-virtual-try-on` / `generate-image-request` |
| Kling v1.6 | Image to Video | `klingai-video` / `image-to-video-request` |
| Kling v1.6 | Text to Video | `klingai-video` / `video-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/kling-image/v1/kling-image-request`
- `POST https://gateway.pixazo.ai/kling-image-o3-i2i/v1/kling-image-o3-i2i-request`
- `POST https://gateway.pixazo.ai/kling-image-t2i/v1/kling-image-t2i-request`
- `POST https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request`
- `POST https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request-result`
- `POST https://gateway.pixazo.ai/kling-3-0-image-to-video-standard/v1/kling-3-0-image-to-video-standard-request`
- `POST https://gateway.pixazo.ai/kling-3-0-image-to-video-standard/v1/kling-3-0-image-to-video-standard-request-result`
- `POST https://gateway.pixazo.ai/kling-ai-avatar-v2-pro-789/v1/kling-ai-avatar-v2-pro-request`
- `POST https://gateway.pixazo.ai/kling-o1-edit-video-video-to-video-634/v1/kling-o1-edit-video-video-to-video-request`
- `POST https://gateway.pixazo.ai/kling-o1-first-frame-last-frame-to-video-857/v1/kling-o1-first-frame-last-frame-to-video-request`
- `POST https://gateway.pixazo.ai/kling-o1-image-208/v1/kling-o1-image-request`
- `POST https://gateway.pixazo.ai/kling-o1-reference-image-to-video-382/v1/kling-o1-reference-image-to-video-request`
- `POST https://gateway.pixazo.ai/kling-o1-reference-video-to-video-315/v1/kling-o1-reference-video-to-video-request`
- `POST https://gateway.pixazo.ai/kling-video-v2-6-standard-motion-control/v1/kling-video-v2-6-standard-motion-control-request`
- `POST https://gateway.pixazo.ai/kling-ai-vton/v1/getVirtualTryOnTask`
- `POST https://gateway.pixazo.ai/kling-ai-video/v1/getImageToVideoTask`
- `POST https://gateway.pixazo.ai/kling-ai-video/v1/generateVideoTask`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "A serene mountain lake at sunset with golden reflections, painterly style",
  "resolution": "1K",
  "aspect_ratio": "auto",
  "num_images": 1,
  "output_format": "png"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "A serene mountain lake at sunset with golden reflections, painterly style",
  "resolution": "1K",
  "aspect_ratio": "auto",
  "num_images": 1,
  "output_format": "png"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "A serene mountain lake at sunset with golden reflections, painterly style",
  "resolution": "1K",
  "aspect_ratio": "auto",
  "num_images": 1,
  "output_format": "png"
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
submit = requests.post("https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/kling-3-0-text-to-video-standard-request", headers=HEADERS, json={...}).json()
task_id = submit.get("task_id") or submit.get("request_id") or submit.get("id")

# 2) Poll (every 5–10s; total cap ~10 min for video, ~3 min for music)
while True:
    status = requests.get(f"https://gateway.pixazo.ai/kling-3-0-text-to-video-standard/v1/result/{task_id}", headers=HEADERS).json()
    if status.get("status") in ("completed", "succeeded", "ready", "done"):
        break
    if status.get("status") in ("failed", "error"):
        raise RuntimeError(status.get("error") or "generation failed")
    time.sleep(8)

# 3) Pull the result URL out (field varies — usually output_url, video_url, audio_url, or url)
result_url = status.get("output_url") or status.get("video_url") or status.get("audio_url") or status.get("url")
```

The exact polling endpoint and "done" status string vary by model — fetch the full reference for this model's polling shape:

> **Fetch:** `https://www.pixazo.ai/models/kling.md`

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

> **Fetch:** `https://www.pixazo.ai/models/kling.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/kling`.

---

## Related Pixazo skills

- **Other video generation models:** `happy-horse`, `p-video`, `seedance`, `sora`, `veo`, `runway`, `pika`, `higgsfield`, `genflare`, `omnihuman`, `lucy-edit`, `ltx`, `luma`, `hailuo`, `mochi`, `stable-diffusion`, `veed`, `vidu`, `wan`, `pixverse`, `kandinsky`, `hunyuan-video`, `heygen`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

---
name: flux-3-video
description: Video generation with FLUX 3 Video API (by Black Forest Labs) via the Pixazo API. TRIGGER when the user mentions "FLUX 3 Video" or "FLUX 3 Video API", or when the user asks to generate / make / create a video / clip / animation and FLUX 3 Video is named or implied. DO NOT TRIGGER for image / music / voice / 3d / try-on — each has its own skill.
---

# FLUX 3 Video API

FLUX.3 Video from Black Forest Labs — text-to-video, image-to-video and video continuation, with synchronised audio, clips up to 20 seconds and HD or FHD output.

You can ask FLUX 3 Video to handle video generation. Powered by Black Forest Labs via the Pixazo API gateway.

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
| FLUX 3 Video | Text to Video | `flux-3-video-text-to-video` / `text-to-video` |
| FLUX 3 Video | Image to Video | `flux-3-video-image-to-video` / `image-to-video` |
| FLUX 3 Video | Video to Video | `flux-3-video-video-to-video` / `video-to-video` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/text-to-video`
- `POST https://gateway.pixazo.ai/flux-3-video-image-to-video/v1/image-to-video`
- `POST https://gateway.pixazo.ai/flux-3-video-video-to-video/v1/video-to-video`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/text-to-video' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "A cozy ramen shop on a rainy Tokyo night, steam rising from the broth. Rain patter and quiet kitchen sounds.",
  "resolution": "hd",
  "duration": 5
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/text-to-video",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "A cozy ramen shop on a rainy Tokyo night, steam rising from the broth. Rain patter and quiet kitchen sounds.",
  "resolution": "hd",
  "duration": 5
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/text-to-video', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "A cozy ramen shop on a rainy Tokyo night, steam rising from the broth. Rain patter and quiet kitchen sounds.",
  "resolution": "hd",
  "duration": 5
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
submit = requests.post("https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/text-to-video", headers=HEADERS, json={...}).json()
task_id = submit.get("task_id") or submit.get("request_id") or submit.get("id")

# 2) Poll (every 5–10s; total cap ~10 min for video, ~3 min for music)
while True:
    status = requests.get(f"https://gateway.pixazo.ai/flux-3-video-text-to-video/v1/result/{task_id}", headers=HEADERS).json()
    if status.get("status") in ("completed", "succeeded", "ready", "done"):
        break
    if status.get("status") in ("failed", "error"):
        raise RuntimeError(status.get("error") or "generation failed")
    time.sleep(8)

# 3) Pull the result URL out (field varies — usually output_url, video_url, audio_url, or url)
result_url = status.get("output_url") or status.get("video_url") or status.get("audio_url") or status.get("url")
```

The exact polling endpoint and "done" status string vary by model — fetch the full reference for this model's polling shape:

> **Fetch:** `https://www.pixazo.ai/models/flux-3-video.md`

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

> **Fetch:** `https://www.pixazo.ai/models/flux-3-video.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/flux-3-video`.

---

## Related Pixazo skills

- **Other video generation models:** `sync-lipsync`, `happy-horse`, `p-video`, `seedance`, `sora`, `veo`, `runway`, `kling`, `pika`, `higgsfield`, `genflare`, `omnihuman`, `lucy-edit`, `ltx`, `luma`, `mochi`, `veed`, `vidu`, `wan`, `minimax`, `pixverse`, `kandinsky`, `hunyuan-video`, `heygen`, `grok-imagine-video`, `gemini-omni`, `cosmos`, `video-to-previs`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

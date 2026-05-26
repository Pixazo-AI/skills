---
name: wan
description: Video generation with Wan AI API (by Alibaba) via the Pixazo API. TRIGGER when the user mentions "Wan" or "Wan AI API", or when the user asks to generate / make / create a video / clip / animation and Wan is named or implied. DO NOT TRIGGER for image / music / voice / 3d / try-on — each has its own skill.
---

# Wan AI API

Comprehensive video and image generation by Alibaba.

You can ask Wan to handle video generation. Powered by Alibaba via the Pixazo API gateway.

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
| Wan 2.2 | Audio to Video (Ref Audio to Video) | `wan-2-2-14b-speech-to-video` / `speech-to-video-request` |
| Wan 2.2 | Audio to Video (Ref Image + Ref Audio to Video — Animate) | `wan-2-2-animate-api-524` / `wan-2-2-animate-api-request` |
| Wan 2.2 | Image to Video (First Frame to Video) | `wan-image-to-video` / `wan-image-to-video-first-frame` |
| Wan 2.2 | Image to Video (Keyframe to Video) | `wan-image-to-video` / `wan-keyframe-to-video` |
| Wan 2.2 | Image to Image (Image Editing) | `wan-text-to-image` / `edit-image-request` |
| Wan 2.2 | Text to Image | `wan-text-to-image` / `text-to-image-request` |
| Wan 2.2 | Text to Video | `wan-text-to-video` / `generate-text-to-viedo-request` |
| Wan 2.5 | Image to Image (Image Editing) | `wan-2-5-image-generation` / `generate-image-to-image` |
| Wan 2.5 | Text to Image | `wan-2-5-image-generation` / `generate-text-to-image` |
| Wan 2.5 | Image to Video | `wan-image-to-video-2-5` / `generate-image-to-video` |
| Wan 2.5 | Text to Video | `wan-image-to-video-2-5` / `generate-text-to-viedo-request` |
| Wan 2.5 | Image to Video | `pixazo-wan-image-to-video-1763709522` / `pixazo-wan-image-to-video-request` |
| Wan 2.6 | Image to Video | `wan-2-6-image-to-video-477` / `wan-2-6-image-to-video-request` |
| Wan 2.6 | Text to Video | `wan-2-6-text-to-video-569` / `wan-2-6-text-to-video-request` |
| Wan 2.6 | Image to Video (Flash) | `wan-2-6-image-to-video-flash-api-353` / `wan-2-6-image-to-video-flash-api-request` |
| Wan 2.7 Pro | Text to Image | `wan-2-7-pro-api` / `text-to-image-request` |
| Wan 2.7 Pro | Image to Image (Image Editing) | `wan-2-7-pro-api` / `edit-image-request` |
| Wan 2.7 | Text to Image | `wan-2-7-api` / `text-to-image-request` |
| Wan 2.7 | Image to Image (Image Editing) | `wan-2-7-api` / `edit-image-request` |
| Wan 2.7 | Video to Video (Video Editing) | `wan-2-7-video-api` / `edit-video-video-to-video` |
| Wan 2.7 | Reference to Video (Ref Image + Ref Video to Video) | `wan-2-7-video-api` / `edit-video-video-to-video-by-reference` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/wan2.2-s2v/v1/generateSpeechToVideoRequest`
- `POST https://gateway.pixazo.ai/wan-i2v/v1/generateImageToVideoRequest`
- `POST https://gateway.pixazo.ai/wan-t2i/v1/generateEditImageRequest`
- `POST https://gateway.pixazo.ai/wan-t2i/v1/generateTextToImageRequest`
- `POST https://gateway.pixazo.ai/wan-video/v1/generateTextToVideoRequest`
- `POST https://gateway.pixazo.ai/wan-image-2-5/v1/generateEditImage2-5Request`
- `POST https://gateway.pixazo.ai/wan-image-2-5/v1/generateTextToImage2-5Request`
- `POST https://gateway.pixazo.ai/wan-video-2-5/v1/generateImageToVideo2-5Request`
- `POST https://gateway.pixazo.ai/wan-video-2-5/v1/generateTextToVideo2-5Request`
- `POST https://gateway.pixazo.ai/pixazo-wan-image-to-video-1763709522/v1/pixazo-wan-image-to-video-request`
- `POST https://gateway.pixazo.ai/wan-2-6-image-to-video-477/v1/wan-2-6-image-to-video-request`
- `POST https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/wan-2-6-text-to-video-request`
- `POST https://gateway.pixazo.ai/wan-2-6-image-to-video-flash-api-353/v1/wan-2-6-image-to-video-flash-api-request`
- `POST https://gateway.pixazo.ai/wan-2-7-pro-api/v1/generateWan27ProTextToImageRequest`
- `POST https://gateway.pixazo.ai/wan-2-7-pro-api/v1/generateWan27ProEditImageRequest`
- `POST https://gateway.pixazo.ai/wan-2-7-api/v1/generateWan27TextToImageRequest`
- `POST https://gateway.pixazo.ai/wan-2-7-api/v1/generateWan27EditImageRequest`
- `POST https://gateway.pixazo.ai/wan-2-7-video-api/v1/generateWan27VideoStyleRequest`
- `POST https://gateway.pixazo.ai/wan-2-7-video-api/v1/generateWan27VideoEditRequest`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/wan-2-6-text-to-video-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "Summer beach vacation style, a man wearing sunglasses Blue Tshirt.",
  "image_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_model.png",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_music.mp3"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/wan-2-6-text-to-video-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "Summer beach vacation style, a man wearing sunglasses Blue Tshirt.",
  "image_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_model.png",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_music.mp3"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/wan-2-6-text-to-video-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "Summer beach vacation style, a man wearing sunglasses Blue Tshirt.",
  "image_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_model.png",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/input_music.mp3"
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
submit = requests.post("https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/wan-2-6-text-to-video-request", headers=HEADERS, json={...}).json()
task_id = submit.get("task_id") or submit.get("request_id") or submit.get("id")

# 2) Poll (every 5–10s; total cap ~10 min for video, ~3 min for music)
while True:
    status = requests.get(f"https://gateway.pixazo.ai/wan-2-6-text-to-video-569/v1/result/{task_id}", headers=HEADERS).json()
    if status.get("status") in ("completed", "succeeded", "ready", "done"):
        break
    if status.get("status") in ("failed", "error"):
        raise RuntimeError(status.get("error") or "generation failed")
    time.sleep(8)

# 3) Pull the result URL out (field varies — usually output_url, video_url, audio_url, or url)
result_url = status.get("output_url") or status.get("video_url") or status.get("audio_url") or status.get("url")
```

The exact polling endpoint and "done" status string vary by model — fetch the full reference for this model's polling shape:

> **Fetch:** `https://www.pixazo.ai/models/wan.md`

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

> **Fetch:** `https://www.pixazo.ai/models/wan.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/wan`.

---

## Related Pixazo skills

- **Other video generation models:** `happy-horse`, `p-video`, `seedance`, `sora`, `veo`, `runway`, `kling`, `pika`, `higgsfield`, `genflare`, `omnihuman`, `lucy-edit`, `ltx`, `luma`, `hailuo`, `mochi`, `veed`, `vidu`, `pixverse`, `kandinsky`, `hunyuan-video`, `heygen`, `grok-imagine-video`, `gemini-omni`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

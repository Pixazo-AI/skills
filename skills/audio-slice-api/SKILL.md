---
name: audio-slice-api
description: Image generation/editing with Audio Trimmer API (by Pixazo) via the Pixazo API. TRIGGER when the user mentions "Audio Trimmer" or "Audio Trimmer API", or when the user asks to generate / make / create / edit / restyle an image and Audio Trimmer is named or implied. DO NOT TRIGGER for video / music / voice / 3d / try-on — each has its own skill.
---

# Audio Trimmer API

Cut a section out of an audio track by start and length. Give start_seconds with either duration_seconds or end_seconds; duration_seconds wins if you send both. The cut is re-encoded to MP3 so it lands exactly where you asked instead of on a frame boundary.

You can ask Audio Trimmer to handle image generation/editing. Powered by Pixazo via the Pixazo API gateway.

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
| Audio Trimmer 1.0 | Slice | `media-slice-audio` / `media-slice-audio-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/media-tools/v1/audio-slice`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/media-tools/v1/audio-slice' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/audio/speech-17s.mp3",
    "start_seconds": 30,
    "duration_seconds": 15
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/media-tools/v1/audio-slice",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/audio/speech-17s.mp3",
    "start_seconds": 30,
    "duration_seconds": 15
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/media-tools/v1/audio-slice', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/audio/speech-17s.mp3",
    "start_seconds": 30,
    "duration_seconds": 15
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

> **Fetch:** `https://www.pixazo.ai/models/audio-slice-api.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/audio-slice-api`.

---

## Related Pixazo skills

- **Other image generation/editing models:** `seedream`, `gpt-image`, `grok-imagine-image`, `ideogram`, `longcat-image`, `nano-banana`, `pixelforge`, `qwen-image`, `recraft`, `reve-image`, `stable-diffusion`, `studio-ghibli`, `auraflow`, `z-image`, `bria`, `sdxl`, `firered-image-edit`, `codeformer`, `gfpgan`, `smart-resize`, `nucleus`, `glm-image`, `hidream`, `ernie-image`, `mirelo`, `real-esrgan`, `gemini-voice`, `mai-image`, `pixelcut`, `krea`, `boogu-image`, `whisper`, `assemblyai`, `separate-stems-api`, `diarize-api`, `video-convert-api`, `video-crop-api`, `video-resize-api`, `video-speed-api`, `video-trim-api`, `video-cut-api`, `video-merge-api`, `video-transition-api`, `video-compress-api`, `video-gif-api`, `video-frame-api`, `video-audio-remover-api`, `audio-normalize-api`, `audio-denoise-api`, `audio-extract-api`, `video-replace-audio-api`, `media-probe-api`, `image-convert-api`, `image-vectorize-api`, `image-extender-api`, `content-safety-api`, `muse-image`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

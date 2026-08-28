---
name: sync-lipsync
description: Video generation with Sync Lipsync API (by Sync.so) via the Pixazo API. TRIGGER when the user mentions "Sync" or "Sync Lipsync API", or when the user asks to generate / make / create a video / clip / animation and Sync is named or implied. DO NOT TRIGGER for image / music / voice / 3d / try-on — each has its own skill.
---

# Sync Lipsync API

Lip-sync any talking-head video to a separate audio track with Sync.so v3. Submit a video URL + audio URL; the model re-syncs the speaker's mouth to the audio.

You can ask Sync to handle video generation. Powered by Sync.so via the Pixazo API gateway.

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
| Sync Lipsync v3 | Video to Video (Ref Video + Ref Audio to Video - Lipsync) | `sync-lipsync-v3` / `sync-lipsync-v3-request` |
| Sync Lipsync v2 Pro | Video to Video (Ref Video + Ref Audio to Video - Lipsync) | `sync-lipsync-v2-pro` / `sync-lipsync-v2-pro-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/lip-sync`
- `POST https://gateway.pixazo.ai/sync-lipsync-v2-pro/v1/video-to-video/lip-sync`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/lip-sync' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "video_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/lipsync/talking-head-17s.mp4",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/test-audio/hg_audio17.mp3",
  "sync_mode": "cut_off"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/lip-sync",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "video_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/lipsync/talking-head-17s.mp4",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/test-audio/hg_audio17.mp3",
  "sync_mode": "cut_off"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/lip-sync', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "video_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/doc-assets/lipsync/talking-head-17s.mp4",
  "audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/test-audio/hg_audio17.mp3",
  "sync_mode": "cut_off"
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
submit = requests.post("https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/lip-sync", headers=HEADERS, json={...}).json()
task_id = submit.get("task_id") or submit.get("request_id") or submit.get("id")

# 2) Poll (every 5–10s; total cap ~10 min for video, ~3 min for music)
while True:
    status = requests.get(f"https://gateway.pixazo.ai/sync-lipsync-v3/v1/video-to-video/result/{task_id}", headers=HEADERS).json()
    if status.get("status") in ("completed", "succeeded", "ready", "done"):
        break
    if status.get("status") in ("failed", "error"):
        raise RuntimeError(status.get("error") or "generation failed")
    time.sleep(8)

# 3) Pull the result URL out (field varies — usually output_url, video_url, audio_url, or url)
result_url = status.get("output_url") or status.get("video_url") or status.get("audio_url") or status.get("url")
```

The exact polling endpoint and "done" status string vary by model — fetch the full reference for this model's polling shape:

> **Fetch:** `https://www.pixazo.ai/models/sync-lipsync.md`

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

> **Fetch:** `https://www.pixazo.ai/models/sync-lipsync.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/sync-lipsync`.

---

## Related Pixazo skills

- **Other video generation models:** `happy-horse`, `p-video`, `seedance`, `sora`, `veo`, `runway`, `kling`, `pika`, `higgsfield`, `genflare`, `omnihuman`, `lucy-edit`, `ltx`, `luma`, `mochi`, `veed`, `vidu`, `wan`, `minimax`, `pixverse`, `kandinsky`, `hunyuan-video`, `heygen`, `grok-imagine-video`, `gemini-omni`, `gemini-omni-1-1`, `cosmos`, `video-to-previs`, `magi`, `vibeo`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

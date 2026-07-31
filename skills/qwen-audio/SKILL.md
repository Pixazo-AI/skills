---
name: qwen-audio
description: Text-to-speech / voice synthesis with Qwen Audio API (by Alibaba) via the Pixazo API. TRIGGER when the user mentions "Qwen Audio" or "Qwen Audio API", or when the user asks to speak / read aloud / convert text to speech / generate voice and Qwen Audio is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# Qwen Audio API

Speech models by Alibaba's Qwen: text-to-speech synthesis, and Qwen 3.0 ASR Flash speech-to-text for short clips or long-form audio and video up to 12 hours, with automatic language and emotion detection.

You can ask Qwen Audio to handle text-to-speech / voice synthesis. Powered by Alibaba via the Pixazo API gateway.

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
| Qwen Audio 3.0 TTS Plus | Text to Speech(Audio) | `qwen-audio-3-0-tts-plus` / `text-to-speech` |
| Qwen Audio 3.0 TTS Flash | Text to Speech(Audio) | `qwen-audio-3-0-tts-flash` / `text-to-speech` |
| Qwen 3.0 TTS | Text to Speech | `qwen3-tts-1-7b-api-401` / `qwen3-tts-1-7b-api-request` |
| Qwen 3.0 ASR Flash | Speech to Text | `qwen3-asr-flash` / `qwen3-asr-flash-request` |
| Qwen 3.0 ASR Flash Filetrans | Speech to Text (Long-form) | `qwen3-asr-flash-filetrans` / `qwen3-asr-flash-filetrans-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/qwen-audio-3-0-tts-plus/v1/text-to-speech`
- `POST https://gateway.pixazo.ai/qwen-audio-3-0-tts-flash/v1/text-to-speech`
- `POST https://gateway.pixazo.ai/qwen3-tts-1-7b-api-401/v1/qwen3-tts-1-7b-api-request-result`
- `POST https://gateway.pixazo.ai/qwen3-asr-flash/v1/speech-to-text`
- `POST https://gateway.pixazo.ai/qwen3-asr-flash-filetrans/v1/speech-to-text`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/qwen-audio-3-0-tts-plus/v1/text-to-speech' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "text": "Welcome to the show. Today we explore the mysteries of deep space and the stories hidden among the stars.",
  "voice": "longanlingxin",
  "format": "mp3",
  "sample_rate": 22050
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/qwen-audio-3-0-tts-plus/v1/text-to-speech",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "text": "Welcome to the show. Today we explore the mysteries of deep space and the stories hidden among the stars.",
  "voice": "longanlingxin",
  "format": "mp3",
  "sample_rate": 22050
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/qwen-audio-3-0-tts-plus/v1/text-to-speech', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "text": "Welcome to the show. Today we explore the mysteries of deep space and the stories hidden among the stars.",
  "voice": "longanlingxin",
  "format": "mp3",
  "sample_rate": 22050
}),
});
console.log(await res.json());
```

### Step 4 — Show the user the result

text-to-speech / voice synthesis via this model is **synchronous** — no polling. The response is JSON, e.g.:

```json
{ "audio": [{ "url": "https://…" }] }
```

Pull the URL out and show it to the user (in chat, render inline if your environment supports it). Offer to: download it, edit it further, or generate variations.



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

> **Fetch:** `https://www.pixazo.ai/models/qwen-audio.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/qwen-audio`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `elevenlabs`, `gemini`, `voxcpm`, `zonos`, `fish-audio`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

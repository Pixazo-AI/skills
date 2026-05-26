---
name: elevenlabs
description: Text-to-speech / voice synthesis with ElevenLabs Audio API (by ElevenLabs) via the Pixazo API. TRIGGER when the user mentions "ElevenLabs" or "ElevenLabs Audio API", or when the user asks to speak / read aloud / convert text to speech / generate voice and ElevenLabs is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# ElevenLabs Audio API

Advanced audio and music generation.

You can ask ElevenLabs to handle text-to-speech / voice synthesis. Powered by ElevenLabs via the Pixazo API gateway.

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
| ElevenLabs v1 | Text to Music | `elevenlabs-music-api-368` / `elevenlabs-music-api-request` |
| ElevenLabs v3 | Text to Speech | `eleven-v3-alpha-954` / `eleven-v3-alpha-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/elevenlabs-music-api-368/v1/elevenlabs-music-api-request`
- `POST https://gateway.pixazo.ai/eleven-v3-alpha-954/v1/eleven-v3-alpha-request`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/elevenlabs-music-api-368/v1/elevenlabs-music-api-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "Mysterious original soundtrack, themes of jungle, rainforest, nature, woodwinds, busy rhythmic tribal percussion.",
  "respect_sections_durations": true,
  "output_format": "mp3_44100_128"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/elevenlabs-music-api-368/v1/elevenlabs-music-api-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "Mysterious original soundtrack, themes of jungle, rainforest, nature, woodwinds, busy rhythmic tribal percussion.",
  "respect_sections_durations": true,
  "output_format": "mp3_44100_128"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/elevenlabs-music-api-368/v1/elevenlabs-music-api-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "Mysterious original soundtrack, themes of jungle, rainforest, nature, woodwinds, busy rhythmic tribal percussion.",
  "respect_sections_durations": true,
  "output_format": "mp3_44100_128"
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

> **Fetch:** `https://www.pixazo.ai/models/elevenlabs.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/elevenlabs`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `gemini`, `qwen-tts`, `openbmb-voxcpm2`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

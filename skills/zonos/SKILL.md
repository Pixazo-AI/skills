---
name: zonos
description: Text-to-speech / voice synthesis with Zonos API (by Zyphra) via the Pixazo API. TRIGGER when the user mentions "Zonos2" or "Zonos API", or when the user asks to speak / read aloud / convert text to speech / generate voice and Zonos2 is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# Zonos API

AI-powered text-to-speech and voice cloning by Zonos.

You can ask Zonos2 to handle text-to-speech / voice synthesis. Powered by Zyphra via the Pixazo API gateway.

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
| Zonos 2 | Text to Speech (Voice Cloning) | `zonos-2` / `zonos-2-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/zonos-2/v1/zonos-2-request`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/zonos-2/v1/zonos-2-request' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "reference_audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/Reference.wav",
  "text": "Hello, this is a sample of my cloned voice speaking naturally.",
  "language": "en_us",
  "accurate_mode": true,
  "clean_speaker_background": false,
  "temperature": 1.15,
  "top_p": 0,
  "min_p": 0.18,
  "top_k": 106
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/zonos-2/v1/zonos-2-request",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "reference_audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/Reference.wav",
  "text": "Hello, this is a sample of my cloned voice speaking naturally.",
  "language": "en_us",
  "accurate_mode": true,
  "clean_speaker_background": false,
  "temperature": 1.15,
  "top_p": 0,
  "min_p": 0.18,
  "top_k": 106
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/zonos-2/v1/zonos-2-request', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "reference_audio_url": "https://pub-582b7213209642b9b995c96c95a30381.r2.dev/Reference.wav",
  "text": "Hello, this is a sample of my cloned voice speaking naturally.",
  "language": "en_us",
  "accurate_mode": true,
  "clean_speaker_background": false,
  "temperature": 1.15,
  "top_p": 0,
  "min_p": 0.18,
  "top_k": 106
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

> **Fetch:** `https://www.pixazo.ai/models/zonos.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/zonos`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `elevenlabs`, `gemini`, `qwen-audio`, `voxcpm`, `fish-audio`, `deepgram`, `inworld`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

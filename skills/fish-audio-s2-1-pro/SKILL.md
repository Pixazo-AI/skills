---
name: fish-audio-s2-1-pro
description: Text-to-speech / voice synthesis with Fish Audio S2.1 Pro TTS (by Fish Audio) via the Pixazo API. TRIGGER when the user mentions "Fish Audio S2.1 Pro" or "Fish Audio S2.1 Pro TTS", or when the user asks to speak / read aloud / convert text to speech / generate voice and Fish Audio S2.1 Pro is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# Fish Audio S2.1 Pro TTS

Fish Audio S2.1 Pro text-to-speech. Natural, expressive speech in 83 languages with free-form emotion markers and a library of 2.5M+ community voices.

You can ask Fish Audio S2.1 Pro to handle text-to-speech / voice synthesis. Powered by Fish Audio via the Pixazo API gateway.

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
| Fish Audio S2.1 Pro TTS | Text to Speech | `fish-audio-s2-1-pro` / `fish-audio-s2-1-pro-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/fish-audio-s2-1-pro/v1/text-to-speech`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/fish-audio-s2-1-pro/v1/text-to-speech' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "text": "[whispering] When you are building something new, there is this mix of wonder and fear. [chuckling] But that is what makes it worth building.",
  "reference_id": "933563129e564b19a115bedd57b7406a",
  "format": "mp3",
  "mp3_bitrate": 128
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/fish-audio-s2-1-pro/v1/text-to-speech",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "text": "[whispering] When you are building something new, there is this mix of wonder and fear. [chuckling] But that is what makes it worth building.",
  "reference_id": "933563129e564b19a115bedd57b7406a",
  "format": "mp3",
  "mp3_bitrate": 128
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/fish-audio-s2-1-pro/v1/text-to-speech', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "text": "[whispering] When you are building something new, there is this mix of wonder and fear. [chuckling] But that is what makes it worth building.",
  "reference_id": "933563129e564b19a115bedd57b7406a",
  "format": "mp3",
  "mp3_bitrate": 128
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

> **Fetch:** `https://www.pixazo.ai/models/fish-audio-s2-1-pro.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/fish-audio-s2-1-pro`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `elevenlabs`, `gemini`, `qwen-tts`, `voxcpm`, `zonos`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

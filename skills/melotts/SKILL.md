---
name: melotts
description: Text-to-speech / voice synthesis with MeloTTS API (by MyShell) via the Pixazo API. TRIGGER when the user mentions "MeloTTS" or "MeloTTS API", or when the user asks to speak / read aloud / convert text to speech / generate voice and MeloTTS is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# MeloTTS API

An open multi-lingual text-to-speech model from MyShell, and by a wide margin the cheapest voice in the catalogue. Billed per minute of audio produced rather than per character.

You can ask MeloTTS to handle text-to-speech / voice synthesis. Powered by MyShell via the Pixazo API gateway.

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
| MeloTTS | Text to Speech | `melotts` / `text-to-speech` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/melotts/v1/text-to-speech`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/melotts/v1/text-to-speech' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "text": "Hello from Pixazo. This is a text to speech demo."
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/melotts/v1/text-to-speech",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "text": "Hello from Pixazo. This is a text to speech demo."
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/melotts/v1/text-to-speech', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "text": "Hello from Pixazo. This is a text to speech demo."
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

> **Fetch:** `https://www.pixazo.ai/models/melotts.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/melotts`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `elevenlabs`, `gemini`, `qwen-audio`, `gemini-voice`, `voxcpm`, `zonos`, `fish-audio`, `deepgram`, `inworld`, `xai-tts`, `lux-tts`, `tada`, `gpt-4o`, `seed-audio`, `mai-voice`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

---
name: openbmb-voxcpm2
description: Text-to-speech / voice synthesis with Openbmb VoxCPM2 API (by Openbmb) via the Pixazo API. TRIGGER when the user mentions "Openbmb VoxCPM2" or "Openbmb VoxCPM2 API", or when the user asks to speak / read aloud / convert text to speech / generate voice and Openbmb VoxCPM2 is named or implied. DO NOT TRIGGER for image / video / music / 3d / try-on — each has its own skill.
---

# Openbmb VoxCPM2 API

Openbmb VoxCPM2 delivers natural-sounding text-to-speech synthesis with voice-design and voice-cloning capabilities for custom voice generation.

You can ask Openbmb VoxCPM2 to handle text-to-speech / voice synthesis. Powered by Openbmb via the Pixazo API gateway.

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
| Openbmb VoxCPM2 v1.0 | Text to Speech | `openbmb-voxcpm2` / `text-to-speech` |
| Openbmb VoxCPM2 v1.0 | Text to Speech (Voice Design) | `openbmb-voxcpm2` / `voice-cloning` |

### Step 3 — Make the API call

**Endpoints**

_See the full reference for endpoint URLs._

**Sample request (primary operation)**

_The full reference includes ready-to-paste curl, Python, and JavaScript examples for each operation._

**Python**

```python
import os, requests
r = requests.post(
    "<endpoint>",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('<endpoint>', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({}),
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

> **Fetch:** `https://www.pixazo.ai/models/openbmb-voxcpm2.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/openbmb-voxcpm2`.

---

## Related Pixazo skills

- **Other text-to-speech / voice synthesis models:** `chatterbox`, `vibevoice`, `xtts`, `elevenlabs`, `gemini`, `qwen-tts`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

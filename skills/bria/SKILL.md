---
name: bria
description: Virtual try-on with Bria API (by Bria) via the Pixazo API. TRIGGER when the user mentions "Bria" or "Bria API", or when the user asks to virtually try on clothing / outfit / virtual fitting and Bria is named or implied. DO NOT TRIGGER for image / video / music / voice / 3d — each has its own skill.
---

# Bria API

Bria's commercially safe image models, trained only on licensed content: Fibo Gen 1.5 for text-to-image with accurate typography, Fibo Edit 1.5 for instruction-driven editing, virtual try-on and product holding, plus background removal for images and video. A safer choice for commercial and brand work where you need to avoid copyright trouble.

You can ask Bria to handle virtual try-on. Powered by Bria via the Pixazo API gateway.

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
| Fibo Gen 1.5 | Text to Image | `fibo-gen-1-5` / `text-to-image` |
| Fibo Edit 1.5 | Image to Image (Image Editing) | `fibo-edit-1-5` / `edit` |
| Fibo Edit 1.5 | Image to Image (Virtual Try On) | `fibo-edit-1-5` / `virtual-try-on` |
| Fibo Edit 1.5 | Image to Image (Product Holding) | `fibo-edit-1-5` / `product-holding` |
| BRIA FIBO Preview | Text to Image | `bria-fibo-bbq-preview` / `bria-fibo-bbq-preview-request` |
| Bria RMBG 2.0 | Image to Image (Image Background Removal) | `bria-rmbg-2-0-682` / `bria-rmbg-2-0-request` |
| Bria VRMBG 3.0 | Video to Video (Background Removal) | `bria-video-background-removal-v3` / `bria-video-background-removal-v3-request` |
| Bria VRMBG 3.0 | Video to Video (Green Screen Despill) | `bria-video-background-removal-green-screen-despill` / `bria-video-background-removal-green-screen-despill-request` |

### Step 3 — Make the API call

**Endpoints**

- `POST https://gateway.pixazo.ai/fibo-gen-1-5/v1/text-to-image`
- `POST https://gateway.pixazo.ai/fibo-edit-1-5/v1/edit`
- `POST https://gateway.pixazo.ai/fibo-edit-1-5/v1/virtual-try-on`
- `POST https://gateway.pixazo.ai/fibo-edit-1-5/v1/product-holding`
- `POST https://gateway.pixazo.ai/bria-fibo-bbq-preview/v1/bria-fibo-bbq-preview-request`
- `POST https://gateway.pixazo.ai/bria-rmbg-2-0-682/v1/bria-rmbg-2-0-request`
- `POST https://gateway.pixazo.ai/bria-video-background-removal-v3/v1/bria-video-background-removal-v3-request`
- `POST https://gateway.pixazo.ai/bria-video-background-removal-green-screen-despill/v1/bria-video-background-removal-green-screen-despill-request`

**Sample request (primary operation)**

```bash
curl -X POST 'https://gateway.pixazo.ai/fibo-edit-1-5/v1/virtual-try-on' \
  -H 'Content-Type: application/json' \
  -H "Ocp-Apim-Subscription-Key: $PIXAZO_API_KEY" \
  -d '{
  "prompt": "A weathered brown leather armchair in a sunlit study, cracked grain texture, soft afternoon light raking across the surface.",
  "aspect_ratio": "1:1",
  "resolution": "1MP"
}'
```

**Python**

```python
import os, requests
r = requests.post(
    "https://gateway.pixazo.ai/fibo-edit-1-5/v1/virtual-try-on",
    headers={
        "Ocp-Apim-Subscription-Key": os.environ["PIXAZO_API_KEY"],
        "Content-Type": "application/json",
    },
    json={
  "prompt": "A weathered brown leather armchair in a sunlit study, cracked grain texture, soft afternoon light raking across the surface.",
  "aspect_ratio": "1:1",
  "resolution": "1MP"
},
    timeout=300,
)
r.raise_for_status()
print(r.json())
```

**Node.js**

```js
const res = await fetch('https://gateway.pixazo.ai/fibo-edit-1-5/v1/virtual-try-on', {
  method: 'POST',
  headers: {
    'Ocp-Apim-Subscription-Key': process.env.PIXAZO_API_KEY,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
  "prompt": "A weathered brown leather armchair in a sunlit study, cracked grain texture, soft afternoon light raking across the surface.",
  "aspect_ratio": "1:1",
  "resolution": "1MP"
}),
});
console.log(await res.json());
```

### Step 4 — Show the user the result

virtual try-on via this model is **synchronous** — no polling. The response is JSON, e.g.:

```json
{ "output": [{ "url": "https://…" }] }
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

> **Fetch:** `https://www.pixazo.ai/models/bria.md`

Load that URL when you need exact parameter names, accepted values, or aren't sure about a field. The HTML version is at `https://www.pixazo.ai/models/bria`.

---

## Related Pixazo skills

- **Other virtual try-on models:** `fashn-vton`, `flux`, `idm-vton`, `google-virtual-try-on`
- **Want everything?** `npx skills add Pixazo-AI/skills --skill '*'`

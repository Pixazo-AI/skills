# Pixazo Agent Skills

One install command and your coding agent (Claude Code, Codex, Cursor, etc.) can call Pixazo APIs to generate images, video, music, voice, 3D models, and more — using the universal [`skills` CLI](https://github.com/vercel-labs/skills).

## Install one skill

```bash
npx skills add Pixazo-AI/skills --skill seedream
```

Replace `seedream` with any model name from the catalog below.

## Install everything

```bash
npx skills add Pixazo-AI/skills --skill '*'
```

## Browse before installing

```bash
npx skills add Pixazo-AI/skills --list
```

## What you need

A Pixazo API key. Get one free at **https://api-console.pixazo.ai/api_keys** — sign in, click "Create new key", copy. The skill itself walks the agent through saving it to `~/.pixazo/api-key` so you only paste once.

## Catalog

### Image generation & editing

`auraflow` · `bria` · `dalle` · `firered-image-edit` · `flux` · `genflare` · `gpt-image` · `grok-imagine` · `hunyuan` · `ideogram` · `longcat-image` · `nano-banana` · `p-image` · `pixelforge` · `qwen-image` · `recraft` · `reve-image` · `sdxl` · `seedream` · `stable-diffusion` · `studio-ghibli` · `tracks` · `z-image` · `kandinsky` · `gemini` · `mochi`

### Video generation

`hailuo` · `higgsfield` · `kling` · `ltx` · `lucy-edit` · `luma` · `minimax` · `omnihuman` · `p-video` · `pika` · `pixverse` · `runway` · `seedance` · `sora` · `veed` · `veo` · `vidu` · `wan`

### Music & audio

`ace-step` · `lyria`

### Text-to-speech / voice

`chatterbox` · `elevenlabs` · `qwen-tts` · `vibevoice` · `xtts`

### 3D model generation

`hyper3d` · `trellis3d` · `tripo3d`

### Virtual try-on

`fashn-vton` · `idm-vton`

### Image / video upscaling & enhancement

`crystal-upscaler` · `seedvr` · `topaz`

## How each skill works

Each `skills/<model>/SKILL.md` teaches the agent:

1. **Auth** — how to acquire and store the user's API key (env var → `~/.pixazo/api-key` → ask once, save, never ask again).
2. **Operation routing** — map user intent to the right endpoint (text-to-image vs image-to-image vs multi-image edit, etc.).
3. **Request shape** — copy-paste-ready curl, Python, and Node.js examples with all fields filled.
4. **Response handling** — synchronous (image, voice, try-on) or async polling (video, music, 3D).
5. **Error handling** — 401 / 402 / 429 / 4xx / 5xx → user-friendly messages.
6. **Full reference** — every skill points to `https://www.pixazo.ai/models/<slug>.md` for complete schemas and per-version differences.

## How skills are generated

These files are emitted by [`scripts/build-skills.mjs`](https://github.com/Appypie-Testing1/pixazo-api-ui/blob/main/scripts/build-skills.mjs) in the [pixazo-api-ui](https://github.com/Appypie-Testing1/pixazo-api-ui) repo, driven by:

- `data/models.json` — model catalog (ID, brand, provider, categories)
- `data/model-docs.json` — per-feature operation list (apiId / operationId)
- `public/models/<slug>.md` — full HTML→Markdown reference (auto-generated from the live API)

Adding a new model = one entry in `models.json` + a docs row, regenerate, push.

## Pixazo

- Web: https://www.pixazo.ai
- Model catalog: https://www.pixazo.ai/models
- API console: https://api-console.pixazo.ai

## License

MIT

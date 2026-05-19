# Leoda.io

> Premium AI marketing pipeline · Visual flow editor · Conversational workflow inside Claude Cowork  
> Pipeline de marketing visual con IA generativa premium · Editor visual de flows · Workflow conversacional dentro de Claude Cowork

[![Version](https://img.shields.io/badge/version-0.4.0-blue)](https://github.com/elianmh/leoda-io/releases)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Cowork Plugin](https://img.shields.io/badge/Cowork-plugin-purple)](https://claude.com/cowork)
[![Lang](https://img.shields.io/badge/lang-EN%20%2F%20ES-amber)](#-en-español)

**From a product idea to a cinematic reel ready to publish, in 5 minutes, for $4 USD.**  
**De una idea de producto a un reel cinematográfico listo para subir, en 5 minutos, por $4 USD.**

---

## 🇺🇸 In English

### What it does

Turns text into complete visual marketing packages:

- **Hero images** in any aspect ratio (Nano Banana 2 / Gemini 2.5 Flash Image)
- **Cinematic videos** up to 15 seconds in one shot, no cuts (Seedance 2.0)
- **Native audio + lip-sync** generated in the same call
- **Voice cloning** from 10s audio sample (MiniMax)
- **Sound effects** — snaps, whooshes, ambient, sparks, explosions (Stable Audio)
- **Bilingual captions** optimized for IG, TikTok, YouTube, Facebook, LinkedIn, WhatsApp
- **Publishing calendar** with optimal times per platform

All in one conversational workflow inside Claude Cowork.

### NEW in v0.4.0 — Flow Editor

Visual node-based canvas like ElevenLabs Flows / n8n / ComfyUI, embedded right in Cowork:

- Toolbar dock with 11 node types (drag-and-drop)
- Bezier connections with type validation
- × button on cable middle to disconnect
- Run individual node / Run from here / Run All
- Double-click rename, right-click context menu
- Click on Image/Audio/Text Ref preview = file picker
- Flows Agent chat integrated (conversational graph editing)
- Rich preview per output type: thumbnail / video with play overlay / waveform / voice card / timeline
- localStorage persistence
- USD cost tracker real-time
- Scroll zoom + drag pan
- **Bilingual UI (EN/ES toggle)**

### Quick install (3 minutes)

**Step 1 — Install Cowork**  
Download **Claude Cowork** at [claude.com/cowork](https://claude.com/cowork) (free).

**Step 2 — Install the plugin** (3 options)

**A. From this repo (1-click marketplace)**

In Cowork: `Settings → Plugins → Add Marketplace` → paste:
```
https://github.com/elianmh/leoda-io
```

**B. Manual with `.plugin` file**

Download [`leoda-io-v0.4.0.plugin`](https://github.com/elianmh/leoda-io/releases/download/v0.4.0/leoda-io-v0.4.0.plugin) and drag it to the Cowork chat window.

**C. Clone the repo**
```bash
git clone https://github.com/elianmh/leoda-io.git
```

**Step 3 — Configure your fal.ai API key**
1. Create account at [fal.ai](https://fal.ai/) (free, add $5–10 USD to start)
2. Get your key from `fal.ai/dashboard/keys`
3. In Cowork chat: `/setup-leoda` and paste your key

You only pay fal.ai for actual inference. No markup, no subscriptions.

### Skills included (13)

| Skill | What it does |
|---|---|
| `/setup-leoda` | First-time setup, FAL_KEY config |
| `/brief-creativo` | Bilingual creative brief (ES + EN) |
| `/imagen-nano-banana` | Image gen with character consistency |
| `/video-seedance` | Cinematic video with native audio + lip-sync |
| `/voz-clon` | Voice cloning from sample audio |
| `/sfx-generar` | Sound effects from text |
| `/captions-multiplataforma` | Bilingual captions per platform |
| `/calendario-publicaciones` | Weekly publishing schedule |
| `/variantes-ab` | A/B variations and multi-aspect reframes |
| `/pipeline-completo` | End-to-end campaign automation |
| `/trailer-cinematografico` | Cinematic trailers with xfade |
| `/storyboard-trailer` | Step-by-step approval flow |
| `/flow-editor` | **NEW** Visual node-based editor |

### Typical campaign costs

| Asset | Cost |
|---|---|
| 1 hero image (Nano Banana 2) | $0.04 |
| 1 cinematic 5s video @ 1080p with native audio | $1.51 |
| 1 OneShot 15s reel @ 1080p | $4.54 |
| Voice clone + 30s TTS narration | $0.40 |
| Sound design | $0.05–0.15 |
| Full bilingual brand film | $6–10 |

The plugin is **MIT-licensed and 100% free**. You pay fal.ai directly per inference.

### Real cases

- **Time-stop "Quicksilver" reel for remodeling company** — 15s, $4.58
- **Brand film for premium nail studio** — 14s, $4.40
- **Cinematic logo reveal** — $0.40
- **Painter + Mona Lisa trailer** — 18s with xfade, $4.62

### Tech stack

- Claude Cowork (plugin runtime)
- fal.ai (model inference)
- ffmpeg (local post-processing, no extra cost)
- Python + PIL (text overlays)

### License

MIT. Use it, fork it, modify it, sell services on top of it.

---

## 🇪🇸 En español

### ¿Qué hace?

Convierte texto en paquetes completos de marketing visual:

- **Imágenes hero** premium en cualquier aspect ratio (Nano Banana 2)
- **Videos cinematográficos** hasta 15 segundos en un solo plano, sin cortes (Seedance 2.0)
- **Audio nativo + lip-sync** generado en la misma llamada
- **Voice cloning** desde 10s de muestra de audio (MiniMax)
- **Sound effects** — snaps, whooshes, ambient, sparks, explosiones (Stable Audio)
- **Captions bilingües** optimizados para IG, TikTok, YouTube, Facebook, LinkedIn, WhatsApp
- **Calendario de publicación** con horarios óptimos por plataforma

Todo en un solo workflow conversacional dentro de Claude Cowork.

### NUEVO en v0.4.0 — Flow Editor

Canvas visual de nodos estilo ElevenLabs Flows / n8n / ComfyUI, embebido en Cowork:

- Toolbar dock con 11 tipos de nodos (drag-and-drop)
- Conexiones Bezier con validación de tipos
- Bo
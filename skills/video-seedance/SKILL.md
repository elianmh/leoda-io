---
name: video-seedance
description: >
  Animar imágenes a video cinematográfico con Seedance 2.0 (ByteDance) vía fal.ai.
  Use cuando el usuario diga "anima esta imagen", "haz un video corto", "convierte a Reel",
  "video para TikTok", "image-to-video", "/video-seedance", o cuando provea una imagen
  de salida de /imagen-nano-banana y quiera convertirla en clip vertical/cuadrado/horizontal
  para redes. Seedance 2.0 incluye AUDIO NATIVO (música, SFX, diálogo lip-sync) sin costo extra.
metadata:
  version: "2.0.0"
---

# Video con Seedance 2.0

ByteDance's most advanced image-to-video model. Anima imágenes estáticas en video cinematográfico con **audio sincronizado**, control de start/end frames, y movimientos cinematográficos avanzados.

## Pre-requisitos

1. `FAL_KEY` configurada → si no, `/setup-leoda`.
2. Una imagen base (idealmente de `/imagen-nano-banana`) o subida por el usuario.

## Endpoints

**Standard (con 1080p):**
```
https://fal.run/bytedance/seedance-2.0/image-to-video
```

**Fast (max 720p, más rápido y barato):**
```
https://fal.run/bytedance/seedance-2.0/fast/image-to-video
```

> **Decisión Standard vs Fast**: ambos tienen la MISMA capacidad. La única diferencia es que Fast no soporta 1080p (solo 480p/720p) y es ~20% más barato y rápido. **Usa Fast por defecto a menos que se pida explícitamente 1080p.**

## Parámetros principales

| Parámetro | Required | Tipo | Descripción |
|---|---|---|---|
| `prompt` | Sí | string | Descripción del movimiento, acción y atmósfera |
| `image_url` | Sí | string | Frame inicial. JPG/PNG/WebP, max 30 MB. Acepta data URLs base64 |
| `end_image_url` | No | string | Frame final opcional — el video transiciona de start a end |
| `resolution` | No | enum | `480p`, `720p` (default), `1080p` (solo Standard) |
| `duration` | No | enum | `auto` o entero entre `4` y `15` segundos |
| `aspect_ratio` | No | enum | `auto`, `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16` |
| `generate_audio` | No | bool | `true` (default) — incluye SFX, ambient y voz lip-sync **sin costo extra** |
| `seed` | No | int | Para reproducibilidad |

## Llamada típica (curl + queue async)

```bash
curl -s -X POST "https://queue.fal.run/bytedance/seedance-2.0/image-to-video" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Slow cinematic dolly forward as the man walks confidently through the scene, soft golden hour light, premium documentary feel",
    "image_url": "https://...",
    "resolution": "1080p",
    "duration": "10",
    "aspect_ratio": "9:16",
    "generate_audio": true
  }'
```

Devuelve `request_id`. Polling con:
```
https://queue.fal.run/bytedance/seedance-2.0/requests/{request_id}/status
https://queue.fal.run/bytedance/seedance-2.0/requests/{request_id}
```

## Capacidades clave de Seedance 2.0

### 1. Audio nativo (game-changer)
Genera SFX ambient, música, **y diálogo con lip-sync REAL** sincronizado a los movimientos del personaje, todo en una sola llamada. Incluido en el precio. Esto elimina la necesidad de mezclar audio aparte en post.

**Para diálogo lip-sync**: incluye en el prompt frases como `"He says with a Cuban accent: 'Tranquilo asere, aquí estoy yo'"`. El modelo intenta hacer lip-sync con esas palabras.

### 2. Director-level camera control
Prompts cinematográficos que entiende:
- `dolly zoom` / `dolly forward` / `dolly back`
- `rack focus` / `pull focus`
- `tracking shot` / `following shot`
- `POV` / `over-shoulder`
- `handheld` / `gimbal smooth`
- `crane up` / `crane down`
- `orbit around subject`
- `slow push-in` / `slow pull-back`

### 3. Realistic physics
Maneja colisiones, comportamiento de tela, movimiento de personaje, partículas, líquidos, fuego con realismo. Ideal para escenas de acción, productos en movimiento, transformaciones.

### 4. Multi-shot editing
Hasta 15 segundos en un solo clip con cortes naturales si el prompt los pide ("camera cuts to..."), o un OneShot continuo si solo se describe un movimiento.

### 5. Start + End frame control
Si pasas `end_image_url`, Seedance genera la transición fluida entre los dos frames durante la duración indicada. Útil para morphs, before/after, secuencias específicas.

## Estructura de respuesta

```json
{
  "video": {
    "url": "https://v3b.fal.media/files/...",
    "content_type": "video/mp4",
    "file_size": 10765042
  },
  "seed": 1234567890
}
```

El MP4 incluye stream de audio AAC 44.1kHz si `generate_audio: true`.

## Cómo escribir buenos prompts

Estructura: **Camera Move + Subject Action + Atmosphere + Audio cue (si aplica)**

### Ejemplos de calidad

❌ Vago: `"man walks"`
✅ Detallado: `"Slow cinematic dolly forward following the man walking confidently through the construction site, soft golden hour light, dust particles drifting in air, ambient construction sounds in background"`

❌ Sin atmósfera: `"sparks fly"`
✅ Cinemático: `"Bright orange welding sparks shower down from above with crackling sound, dust suspended in warm afternoon light, premium documentary feel"`

**Para OneShot continuo (sin cortes)**:
- Empieza con `"Single continuous unbroken cinematic shot, no cuts"`
- Describe TODA la secuencia de acción en orden
- Cierra con duración total esperada implícita

**Para audio nativo específico**:
- `"with ambient construction sounds"`
- `"the man says in Spanish: 'palabras aquí'"`
- `"with cinematic orchestral build-up music"`
- `"silent atmosphere except for footsteps on concrete"`

## Pricing

| Tier | Rate | 5s | 10s | 15s |
|---|---|---|---|---|
| **Standard** (image-to-video) | $0.3024 / sec | $1.51 | $3.02 | $4.54 |
| **Fast** (fast/image-to-video) | $0.2419 / sec | $1.21 | $2.42 | $3.63 |

Audio nativo **incluido sin costo extra**. Token-based billing también disponible: `tokens = (height × width × duration × 24) / 1024` a $0.014/1000 tokens — usualmente más caro que el por-segundo.

**Comparado con V1 Lite** ($0.18 / 5s = $0.036/sec): Seedance 2.0 cuesta 6-8x más pero entrega:
- Calidad cinematográfica superior
- Audio nativo incluido
- Hasta 15s en un clip (vs 5s o 10s en V1)
- Movimientos de cámara avanzados
- Lip-sync de diálogo

## Manejo de errores

| Error | Causa | Fix |
|---|---|---|
| 401 Unauthorized | `FAL_KEY` mal/falta | `/setup-leoda` |
| 402 Insufficient balance | Sin saldo | Recargar `https://fal.ai/dashboard/billing` |
| 422 duration validation | Valor fuera de rango | Usar entero 4-15 o `auto` |
| Path not found | Endpoint mal escrito | Confirmar `bytedance/seedance-2.0/image-to-video` (sin prefijo `fal-ai/`) |
| Video con artefactos | Prompt muy complejo o conflictivo | Simplificar, usar `camera_fixed` style en prompt, regenerar con otro seed |

## Procesar la respuesta

1. Extrae `video.url` del JSON
2. Descarga con `curl -sL --retry 3 -o nombre.mp4 "URL"` (retry para evitar archivos parciales)
3. Verifica integridad: `ffprobe -v error ... -of csv=p=0 archivo.mp4` debe retornar `width,height,duration` sin errores
4. Comparte con `computer://...` link al usuario
5. Guarda prompt + seed + URL en `outputs/videos/<campana>-prompts.json`

## Costos aproximados por campaña

| Campaña | Composición | USD |
|---|---|---|
| 1 OneShot 15s Fast 720p | 1 clip | $3.63 |
| 1 OneShot 15s Standard 1080p | 1 clip | $4.54 |
| Reel 30s = 2 clips 15s Standard | 2 clips | $9.08 |
| Brand film 10 escenas × 5s Fast 720p | 10 clips | $12.10 |

## Recursos adicionales

- `references/motion-recipes.md` — recetas de movimiento por categoría
- [Documentación oficial fal.ai](https://fal.ai/models/bytedance/seedance-2.0/image-to-video)

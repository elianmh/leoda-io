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

## PRE-FLIGHT CHECKLIST (OBLIGATORIO antes de cada llamada)

Antes de mandar CUALQUIER request a fal.ai, Claude DEBE completar este checklist en su scratchpad. Esto evita el desperdicio más común: re-enviar requests por endpoint malo o regenerar assets que ya existen.

```
PRE-FLIGHT antes de generar video:
[ ] ¿El usuario ya proveyó imágenes/videos en el chat o en su workspace? 
    → Si SÍ: úsalos. NO regeneres.
[ ] ¿Endpoint correcto? bytedance/seedance-2.0/image-to-video
    NUNCA fal-ai/bytedance/... (ese endpoint NO existe)
[ ] ¿Costo estimado calculado y aceptable?
    5s @ 1080p Standard = $1.51
    5s @ 720p Fast      = $1.21
    15s @ 1080p Standard = $4.54
[ ] ¿Cuántos clips voy a enviar en paralelo? Total estimado: $___
[ ] ¿Es la PRIMERA vez que envío este prompt? 
    → Si ya hay un request_id en mi scratchpad para este prompt, NO re-enviar
[ ] ¿El prompt especifica EXPLÍCITAMENTE el encuadre de cámara?
    Sin esto, Seedance puede mover la cámara fuera del frame deseado
    (ej: "framing the subject from waist-up, never showing feet")
```

**Si el checklist no pasa, NO envíes el request. Reporta al usuario qué falta.**

## Reuso de assets existentes (regla #1 para ahorrar créditos)

Antes de generar un nuevo clip, Claude DEBE buscar en el workspace si ya hay material reusable:

```bash
# Buscar imágenes y videos previos
ls -la /workspace/path/to/project/*.{jpg,png,mp4}

# Si el usuario dijo "haz un trailer de un pintor" y ya hay scene1.jpg en disco,
# úsalo directamente como image_url. NO regeneres.
```

Reglas de reuso:
- Imagen del usuario subida o ya generada → USARLA (no regenerar)
- Clip de Seedance previo de la misma escena → REUSARLO (no regenerar)
- Solo regenerar si el usuario pide explícitamente "cámbiala" o "regenera"

## Idempotencia y prevención de doble cobro

**El bug más caro:** mandar el mismo prompt 2 veces porque el primer status URL devolvió error.

```bash
# MAL — devuelve "Path not found" pero el request SÍ se procesó y se cobró
curl https://queue.fal.run/fal-ai/bytedance/seedance-2.0/image-to-video/requests/$ID/status

# BIEN — usa el status_url que devuelve la respuesta inicial
curl $STATUS_URL_FROM_INITIAL_RESPONSE
```

**Regla:** siempre extraer `status_url`, `response_url` directamente del JSON de respuesta del submit. NO construirlos por tu cuenta.

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
- Cierra con
---
name: imagen-nano-banana
description: >
  Generar, editar o componer imágenes para marketing con Nano Banana (Gemini 2.5
  Flash Image) vía fal.ai. Use cuando el usuario diga "genera una imagen", "crea
  un visual", "edita esta foto", "compón a esta persona en esta escena", "usa esta
  foto como referencia", "mockup de producto", "/imagen-nano-banana", o cuando provea
  un prompt visual o una imagen de referencia. Soporta text-to-image, image-to-image
  edit, y referencias múltiples para preservar identidad de personajes/marcas.
metadata:
  version: "2.0.0"
---

# Imagen con Nano Banana

Generar imágenes de marketing llamando a Nano Banana en fal.ai. Tres modos:

1. **text-to-image** — prompt → imagen nueva
2. **edit / reference** — prompt + 1+ imágenes de referencia → imagen consistente con las refs
3. **image upload** — imagen del usuario → URL pública en fal.media

## Pre-requisitos

1. `FAL_KEY` configurada → si no, `/setup-leoda`
2. Si vienes de `/brief-creativo`, los prompts visuales del brief son punto de partida

## Modo 1 — Text-to-image

**Endpoint**: `https://fal.run/fal-ai/nano-banana`

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Premium ceramic mug filled with steaming espresso, warm morning window light, hero product shot, vertical 9:16 composition",
    "num_images": 1,
    "output_format": "jpeg",
    "aspect_ratio": "9:16"
  }'
```

| Parámetro | Valores | Para qué |
|---|---|---|
| `prompt` | string en inglés | Descripción visual detallada |
| `num_images` | 1–4 | Variantes por llamada |
| `output_format` | `jpeg` \| `png` | jpeg pesa menos, png mantiene transparencia |
| `aspect_ratio` | `1:1`, `9:16`, `16:9`, `4:5`, `3:4` | Marco final |

**Aspect ratios por plataforma**:
- Instagram feed → `1:1` o `4:5`
- Reels / TikTok / Shorts / WhatsApp Status → `9:16`
- YouTube thumbnail / LinkedIn / Facebook ads → `16:9`

## Modo 2 — Edit con referencias (CRÍTICO para consistencia)

Cuando necesites **mantener un personaje, producto o estilo consistente** entre escenas, usa el endpoint edit con `image_urls`. Esto es lo que permite, por ejemplo, generar 10 escenas distintas con el MISMO Ortiz manteniendo su rostro real.

**Endpoint**: `https://fal.run/fal-ai/nano-banana/edit`

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana/edit" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Place this same man (preserve face exactly from reference) walking confidently into an empty backyard...",
    "image_urls": ["data:image/jpeg;base64,...", "https://..."],
    "num_images": 1,
    "aspect_ratio": "9:16",
    "output_format": "jpeg"
  }'
```

**Parámetros adicionales del modo edit**:

| Parámetro | Tipo | Descripción |
|---|---|---|
| `image_urls` | array of strings | 1 a 5 URLs de referencia. Puede ser HTTPS URLs o data URLs base64 |
| `prompt` | string | Debe incluir frases como `"this same man"`, `"preserve face exactly"`, `"keep the same product"` para forzar consistencia |

**Cuándo usar Edit en lugar de Text-to-image**:

- Personaje recurrente (dueño de negocio, modelo, mascota)
- Producto específico (botella, packaging, dispositivo)
- Style consistency entre múltiples escenas de un reel
- Adaptar una foto real al estilo de marca generado

## Modo 3 — Subir imagen del usuario a fal storage

Si el usuario sube una foto local que necesitas pasar a otros endpoints (como Seedance o Nano Banana edit), conviértela a data URL base64:

```bash
B64=$(base64 -w 0 "/ruta/al/archivo.jpg")
DATA_URL="data:image/jpeg;base64,${B64}"
# usar $DATA_URL en image_url o image_urls
```

**Importante**: data URLs grandes (>1 MB de archivo origen ≈ >1.3 MB data URL) pueden causar timeouts. Si el archivo es grande, primero re-comprime con `ffmpeg -i in.jpg -q:v 5 out.jpg`.

## Cómo escribir buenos prompts

Nano Banana responde mejor a prompts en **inglés** con esta estructura:
**Subject + Action + Context + Style + Lighting + Composition + Technical detail**.

### Ejemplos:

❌ `nice coffee photo`

✅ `Premium ceramic mug filled with steaming black espresso, sitting on a weathered oak table next to scattered coffee beans, soft morning window light from the left, shallow depth of field, warm earthy palette, 50mm lens look, hero product shot, vertical 9:16 composition`

### Reglas:

1. **Inglés siempre** — rinde mejor que ES
2. **Especifica la iluminación** (golden hour, studio softbox, neon, overcast, raking light)
3. **Especifica el estilo** (photographic, editorial, flat illustration, 3D render, cinematic)
4. **Menciona lente/cámara** si es foto realista (`50mm lens`, `macro shot`)
5. **Menciona la paleta** de colores cuando importa
6. **Menciona el formato** (vertical / square / horizontal)
7. **Para preservar refs**: usa frases como `"this same [subject]"`, `"preserve face exactly"`, `"keep the same [feature]"`

Cuando el usuario hable en español, traduce internamente al inglés el prompt antes de enviarlo.

## ⚠️ Limitación crítica: TEXTO en imágenes

**Nano Banana NO escribe texto consistente**. Si necesitas un logo, palabra, número o letra dentro de la imagen:

1. **NO pongas la palabra "logo"** en el prompt — el modelo escribirá literalmente "LOGO" como texto en la imagen
2. Genera la imagen **SIN texto** ("no text, no signage, no writing anywhere")
3. Compón el texto **en post** con ffmpeg drawtext, PIL, o CapCut

## Procesar la respuesta

```json
{
  "images": [{"url": "https://...", "width": 768, "height": 1344}],
  "description": "...",
  "seed": 123456789
}
```

1. Extrae `url` de cada imagen
2. Descárgala a `outputs/imagenes/<campana>-<n>.jpeg` con `curl -o`
3. Comparte con `computer://...` link
4. **Guarda el seed + prompt** en `outputs/imagenes/<campana>-prompts.json` para reproducibilidad

## Manejo de errores

| Error | Causa | Fix |
|---|---|---|
| 401 Unauthorized | `FAL_KEY` falta | `/setup-leoda` |
| 402 Insufficient balance | Sin saldo | Recargar `https://fal.ai/dashboard/billing` |
| 422 validation | Prompt vacío, aspect inválido | Corregir y reintentar |
| Reached concurrent limit | >10 requests en paralelo | Esperar 5s y reintentar |
| Imagen con texto borroso | Pediste texto en el prompt | Quitar palabras como "text", "logo", "label", regenerar |
| Imagen no preserva referencia | Prompt no enfatiza "this same..." | Reforzar con `"preserve exactly"`, `"same face same features"` |

## Costos

~$0.039 USD por imagen generada (text-to-image o edit). Avisa al usuario antes de generar lotes grandes (10+ imágenes).

## Recursos adicionales

- `references/prompt-recipes.md` — recetas de prompts probados por categoría
- [Documentación oficial fal.ai](https://fal.ai/models/fal-ai/nano-banana)

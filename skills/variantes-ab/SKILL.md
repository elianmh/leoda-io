---
name: variantes-ab
description: >
  Generar variantes A/B y reformatear assets a múltiples aspect ratios (9:16, 1:1,
  16:9, 4:5) para todas las plataformas. Use cuando el usuario diga "haz variantes",
  "versiones A/B", "necesito esto en cuadrado y vertical", "adapta para todas las redes",
  "/variantes-ab", o cuando ya tenga una imagen/video base y quiera múltiples ángulos
  de testeo (mensaje, paleta, ángulo creativo) o múltiples formatos para distintas plataformas.
metadata:
  version: "0.1.0"
---

# Variantes A/B y reformateo

Tomar un asset base (imagen y/o video ya generado) y producir:

1. **Variantes creativas A/B/C** — mismo producto, distinto enfoque (paleta, mensaje, ángulo, mood) para testear cuál performa mejor.
2. **Reformateos** — el mismo asset adaptado al aspect ratio de cada plataforma destino.

## Estrategia

### Caso 1 — Variantes creativas

Usa la `seed` y el `prompt` original del asset base (guardados por `/imagen-nano-banana` y `/video-seedance` en sus `*-prompts.json`). Genera 3 variantes cambiando **una sola variable** por variante:

| Variante | Cambio |
|---|---|
| A (control) | Original |
| B | Cambia la paleta de colores (cool → warm, o viceversa) |
| C | Cambia el ángulo/composición (frontal → ¾ , top-down → eye-level) |
| D (opcional) | Cambia el mood/tono (premium → playful, calmo → enérgico) |

**Regla**: una variable por variante. Si cambias 3 cosas a la vez no sabes qué movió el resultado.

### Caso 2 — Reformateo por plataforma

| Plataforma destino | Aspect ratio | Formato |
|---|---|---|
| Instagram Reels / TikTok / YouTube Shorts / WhatsApp Status | `9:16` | vertical |
| Instagram Feed | `1:1` o `4:5` | cuadrado / retrato |
| Facebook Feed | `1:1` | cuadrado |
| LinkedIn | `1:1` o `16:9` | cuadrado / horizontal |
| YouTube long-form / banner | `16:9` | horizontal |

Dos rutas para reformatear:

**Ruta A — Regenerar** con Nano Banana en el nuevo aspect ratio (más limpio, recomendado).

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "MISMO_PROMPT_BASE",
    "seed": SEED_ORIGINAL,
    "aspect_ratio": "NUEVO_RATIO",
    "num_images": 1
  }'
```

Pasando el mismo `seed` se mantiene la coherencia visual entre formatos.

**Ruta B — Recortar / extender** con Nano Banana edit (cuando el usuario tiene el asset original y no quiere regenerar).

Para llevar un `1:1` a `9:16` se hace **outpaint** (extender por arriba y abajo):

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana/edit" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "extend the scene naturally above and below, keeping the same lighting, mood and palette, vertical 9:16 composition",
    "image_urls": ["URL_DEL_ORIGINAL"],
    "aspect_ratio": "9:16"
  }'
```

## Reformateo de video

Seedance no hace reformat post-hoc. Si el usuario necesita el mismo concepto en varios ratios:

1. Reformatea la **imagen base** primero (ruta A o B arriba) a cada ratio destino.
2. Llama a `/video-seedance` por cada imagen reformateada, usando el **mismo prompt de movimiento** y el **mismo seed** cuando sea posible.

## Flujo recomendado

1. Lee el `outputs/imagenes/<nombre-campana>-prompts.json` para recuperar prompt + seed originales.
2. Pregunta al usuario qué variables quiere variar (paleta, ángulo, mood) y a qué plataformas necesita reformatear.
3. Genera primero las **variantes creativas** (sirven para testear A/B).
4. De cada variante ganadora (o de la base si solo quiere reformat), genera los **reformateos** por plataforma.
5. Guarda todo en `outputs/imagenes/<nombre-campana>/variantes/` y `outputs/imagenes/<nombre-campana>/formatos/`.
6. Comparte un índice con `computer://...` links agrupado por variante y plataforma.

## Plantilla de variación de prompt

Cuando varíes una sola variable, mantén exactamente igual el resto. Ejemplo, dado el prompt base:

> `Premium ceramic mug filled with steaming black espresso, sitting on a weathered oak table next to scattered coffee beans, soft morning window light from the left, shallow depth of field, warm earthy palette, 50mm lens look, hero product shot, vertical 9:16 composition`

**Variante B (paleta)**: cambia solo `warm earthy palette` → `cool blue-grey moody palette`.

**Variante C (ángulo)**: cambia solo `hero product shot` → `overhead top-down flat-lay`.

**Variante D (mood)**: cambia solo `soft morning window light from the left` → `dramatic dim café evening light with single warm spotlight`.

## Costos

Cada variante o reformat genera 1 imagen nueva → ~$0.039 USD. Si haces 4 variantes × 4 formatos = 16 imágenes ≈ $0.62. Si además animas todas a video con Seedance, suma ~$2.88 más por los 16 clips de 5s a 720p. Avisa el total estimado antes de lanzar.

---
name: pipeline-completo
description: >
  Ejecutar el pipeline completo de marketing visual de extremo a extremo: brief →
  imágenes (Nano Banana) → videos (Seedance) → variantes A/B y reformateos →
  captions bilingües por plataforma → calendario semanal de publicaciones.
  Use cuando el usuario diga "haz todo", "pipeline completo", "ejecuta la campaña entera",
  "todo de una vez", "/pipeline-completo", o cuando provea un producto/servicio y
  quiera el paquete completo listo para publicar en redes sin invocar cada skill manualmente.
metadata:
  version: "0.1.0"
---

# Pipeline completo

Orquestar todas las otras skills de Leoda.io en un solo flujo guiado, de extremo a extremo. Ideal cuando el usuario quiere una campaña completa sin tener que llamar manualmente a cada skill.

## Pre-vuelo

1. Verifica `FAL_KEY`. Si no está → manda a `/setup-leoda` primero.
2. Confirma con el usuario los inputs mínimos (producto, audiencia, plataformas, presupuesto aproximado).
3. **Calcula y muestra el costo estimado total** antes de empezar (ver tabla de costos abajo). Pide confirmación explícita.

## Flujo paso a paso

Ejecuta estas fases en orden, mostrando progreso después de cada una. Después de cada fase, pausa y muestra al usuario lo generado por si quiere ajustar antes de la siguiente.

### Fase 1 — Brief

Invoca el contenido de la skill `brief-creativo`. Genera el brief bilingüe con las 10 secciones estándar. Guárdalo en `outputs/briefs/<campana>-<YYYY-MM-DD>.md`.

**Checkpoint**: muestra el brief y pregunta "¿Sigo con la generación de imágenes? / Continue with image generation?".

### Fase 2 — Imágenes hero

Toma los 3 prompts visuales sugeridos del brief (sección 8). Llama a `imagen-nano-banana` para generar **1 imagen por prompt** en el aspect ratio principal del usuario (default `9:16` si las plataformas son mayoritariamente Reels/TikTok/Shorts, sino el más común).

**3 imágenes hero**. Guarda prompt + seed en `outputs/imagenes/<campana>-prompts.json`.

**Checkpoint**: muestra las 3 imágenes con `computer://` links y pregunta "¿Sigo con los videos? / Continue with videos?". Si el usuario quiere regenerar alguna, vuelve a Fase 2 solo para esa.

### Fase 3 — Videos hero

De cada imagen hero aprobada, llama a `video-seedance` con un prompt de movimiento apropiado (saca recetas de `motion-recipes.md` según la categoría: producto → push-in, lifestyle → micro-handheld, etc.).

Configuración default: `duration: 5`, `resolution: 720p`, mismo `aspect_ratio` que la imagen.

**3 clips de 5s**. Guarda en `outputs/videos/<campana>/`.

**Checkpoint**: muestra los 3 videos. Pregunta si seguir con variantes/formatos.

### Fase 4 — Variantes y reformateos

Por cada imagen hero (3 totales), genera:

- **Variantes A/B**: 2 variantes adicionales por imagen, variando una sola variable (paleta y ángulo). Total 6 imágenes nuevas.
- **Reformateos**: cada imagen hero adaptada a los demás aspect ratios necesarios según las plataformas del brief. Si el usuario tiene IG + TikTok + LinkedIn, eso es `9:16` + `1:1` + `16:9` = 3 ratios. La imagen base ya cubre uno, faltan 2 reformateos por imagen × 3 imágenes = 6 reformateos.

Total adicional: ~12 imágenes. (Para video, solo reformatea las imágenes; no regenera todos los videos en todos los ratios por costo — pregunta al usuario qué clip clave quiere en todos los ratios y solo regenera ese).

### Fase 5 — Captions

Invoca `captions-multiplataforma`. Genera captions ES + EN para cada plataforma destino, vinculados a su asset correspondiente. Guarda en `outputs/captions/<campana>-<YYYY-MM-DD>.md`.

### Fase 6 — Calendario

Invoca `calendario-publicaciones`. Asigna cada asset+caption a un día/hora óptimo de la próxima semana, respetando la frecuencia recomendada por plataforma. Guarda CSV en `outputs/calendarios/<campana>-<YYYY-MM-DD>.csv`.

### Fase 7 — Entrega final

Genera un índice maestro `outputs/<campana>/INDEX.md` con:

- Link al brief
- Galería de imágenes (con `computer://` por cada una, agrupadas por hero / variantes / formatos)
- Lista de videos con links
- Link al archivo de captions
- Link al calendario CSV
- Resumen de costo real gastado (suma las llamadas a fal.ai)

Comparte el `INDEX.md` con el usuario y propón siguiente paso (programar publicaciones, regenerar variantes específicas, lanzar otra campaña).

## Tabla de costos por campaña típica

Asumiendo precios de fal.ai (referenciales): imagen ~$0.039 USD, video 5s/720p ~$0.18 USD.

| Concepto | Cantidad | Subtotal |
|---|---|---|
| Imágenes hero | 3 | $0.12 |
| Videos hero | 3 | $0.54 |
| Variantes A/B (imagen) | 6 | $0.23 |
| Reformateos por plataforma | 6 | $0.23 |
| Video adicional reformateado (opcional) | 1 | $0.18 |
| **Total estimado** | | **~$1.30 USD** |

Si el usuario amplía a 6 plataformas y pide variantes en video además, el total puede subir a $5–8 USD por campaña. Sé transparente con esto **antes** de ejecutar.

## Manejo de errores en cadena

Si una fase falla (timeout, error de API, prompt rechazado):

1. **No abortar todo**. Reporta qué fase falló y por qué.
2. Ofrece reintento con ajuste mínimo (otro seed, prompt simplificado).
3. Si el usuario decide saltar la fase, sigue con la siguiente y márcalo en el INDEX.md.

## Modo express (opcional)

Si el usuario dice "rápido" o "lo mínimo viable", reduce a:
- 1 imagen hero, 1 video hero
- 1 variante A/B
- 1 reformateo por plataforma destino
- Captions solo para las 2 plataformas top
- Calendario de 3 días en vez de 7

Esto baja el costo a ~$0.50 USD por campaña.

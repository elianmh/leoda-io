---
name: trailer-cinematografico
description: >
  Crear trailers cinematográficos cortos (10-20s) tipo trailer de película con continuidad
  de cámara entre clips, transiciones xfade suaves, endcard con branding, y audio nativo.
  Use cuando el usuario diga "haz un trailer", "tipo trailer de película", "trailer
  cinematográfico", "video corto promocional cinematográfico", "/trailer-cinematografico",
  o describa una historia visual de 3-5 escenas que necesite hilarse como un trailer.
  Optimiza el uso de créditos reusando imágenes existentes y validando antes de enviar a fal.ai.
metadata:
  version: "1.0.0"
---

# Trailer Cinematográfico

Skill especializado para producir trailers cortos profesionales con:
- Continuidad visual entre escenas
- Movimientos de cámara coherentes (sin saltos bruscos)
- Transiciones xfade entre clips (no cortes duros feos)
- Endcard con branding al final
- Audio sincronizado entre clips

## Cuándo NO usar este skill

- Si el usuario quiere un solo clip → usa `/video-seedance` directamente
- Si es un brand film de 10+ escenas → usa `/pipeline-completo`
- Si es contenido para redes sin narrativa → usa `/video-seedance` + caption

## Flujo de producción (5 pasos)

### Paso 1 — PLAN (antes de gastar UN CENTAVO)

Antes de cualquier llamada a fal.ai, Claude debe escribir y mostrar al usuario un plan que incluya:

```
TRAILER PLAN
============
Duración total objetivo: ___s
Aspect ratio: 9:16 / 1:1 / 16:9
Estilo: cinemático / suspense / motivacional / comedia / etc

ESCENA 1 (0-___s)
  - Imagen base: [generar | reusar de /path/file.jpg | provista por usuario]
  - Movimiento de cámara: [dolly forward / orbit / static / etc]
  - Encuadre: [waist-up / wide / close-up / etc]
  - Audio: [música suspense / ticking clock / silencio / etc]
  - Costo: $___

ESCENA 2 (___s-___s)
  - Continuidad con escena 1: [mismo encuadre / cámara sigue arriba / etc]
  - ...

ESCENA 3 ...

ENDCARD (___-___s)
  - Texto: ___
  - Branding: ___

COSTO TOTAL ESTIMADO: $___
ASSETS A REUSAR: [lista]
ASSETS A GENERAR: [lista]
```

**Solo después de mostrar este plan, proceder.**

### Paso 2 — Reuso de assets

Listar TODOS los archivos relevantes en el workspace ANTES de generar nada:

```bash
find /workspace -type f \( -name "*.jpg" -o -name "*.png" -o -name "*.mp4" \) -newer /tmp/cutoff
```

Para cada escena del plan, decidir:
- ¿Tengo ya una imagen que sirva? → reusar
- ¿Tengo ya un clip Seedance que sirva? → reusar
- ¿Necesito generar nuevo? → solo si nada existente sirve

### Paso 3 — Generar imágenes base con consistencia de personaje

Usar Nano Banana `/edit` con `image_urls` para mantener consistencia entre escenas:

```bash
# Escena 1 → text-to-image
POST /fal-ai/nano-banana
  prompt: "scene 1 description with all visual details"
  image_size: {width: 720, height: 1280}

# Escena 2 → /edit usando escena 1 como referencia
POST /fal-ai/nano-banana/edit
  prompt: "Same scene as reference but with [change]. Keep lighting, framing, palette IDENTICAL."
  image_urls: [data:image/jpeg;base64,SCENE1_B64]
```

**Regla de oro:** menciona explícitamente en el prompt qué debe mantenerse igual ("same lighting, same composition, same character").

### Paso 4 — Animar con Seedance 2.0 + continuidad de cámara

Para cada clip, especificar el encuadre con precisión militar:

```
PROMPT TEMPLATE para continuidad:
"[Continuation cue: camera continues from previous shot at waist-height] 
 [Movement: slow dolly forward / steady push-in / etc]
 [Framing constraint: keeping subject framed from waist to head, NEVER cutting off head or feet]
 [Action: what happens in the scene]
 [Atmosphere: lighting, particles, mood]
 [Audio: specific musical cue + ambient]"
```

**Errores comunes a evitar:**
- "the camera moves" sin especificar dirección → Seedance interpreta libremente, puede mostrar pies
- Cambiar el encuadre entre clips sin transición → corte feo
- No mencionar audio → Seedance puede generar música random

**Llamadas en paralelo:** SOLO si tienes presupuesto confirmado. 3 clips a 1080p = $4.50.

### Paso 5 — Composición con xfade

NUNCA usar concat duro entre clips. Siempre xfade de 0.4-0.6s:

```bash
# Concat con xfade (transición cinematográfica suave)
ffmpeg -y -i clip1.mp4 -i clip2.mp4 -i clip3.mp4 -i endcard.mp4 \
  -filter_complex "
    [0:v][1:v]xfade=transition=fade:duration=0.5:offset=4.5[v01];
    [v01][2:v]xfade=transition=fade:duration=0.5:offset=8.9[v012];
    [v012][3:v]xfade=transition=fade:duration=0.5:offset=13.3[v];
    [0:a][1:a]acrossfade=d=0.5[a01];
    [a01][2:a]acrossfade=d=0.5[a012];
    [a012][3:a]acrossfade=d=0.5[a]
  " \
  -map "[v]" -map "[a]" \
  -c:v libx264 -preset medium -crf 18 -pix_fmt yuv420p \
  -c:a aac -b:a 192k \
  -movflags +faststart \
  TRAILER_FINAL.mp4
```

**Cálculo del offset xfade:**
- offset = duración_acumulada_clip_anterior - duración_fade
- Si clip1 dura 4.92s y fade es 0.5s → offset[0,1] = 4.42s

### Paso 6 — Endcard

Crear con PIL (no Seedance, ahorra créditos):

```python
# endcard.jpg → endcard.mp4 con fade-in/fade-out
ffmpeg -y -loop 1 -i endcard.jpg -t 3 \
  -vf "fade=t=in:st=0:d=0.6,fade=t=out:st=2.5:d=0.5,format=yuv420p" \
  -f lavfi -i "anullsrc=r=48000:cl=stereo" \
  -t 3 -c:v libx264 -pix_fmt yuv420p -r 24 -c:a aac \
  endcard.mp4
```

### Paso 7 — Exportar versiones

Siempre exportar al menos 2 aspect ratios:
- `_9x16.mp4` — Reels, TikTok, Stories
- `_1x1.mp4` — Instagram Feed, Facebook
- `_16x9.mp4` — YouTube, X (opcional)

Cropping centrado:
```bash
# 9:16 → 1:1 (centered crop)
ffmpeg -y -i TRAILER_9x16.mp4 -vf "crop=1080:1080:0:420" ... TRAILER_1x1.mp4

# 9:16 → 16:9 (rotate + pillarbox NO; mejor regenerar 16:9 desde Seedance)
```

## Plantilla de prompts cinematográficos por género

### Trailer de suspense / misterio
```
Clip 1: "Cinematic low-angle dolly up from waist-height to head-height, holding subject in frame. Dark moody studio, strong rim light from behind. Wall clock ticking. Suspenseful orchestral score building tension."

Clip 2: "[Same framing as clip 1, camera locked at head-height]. Time accelerates: clock spins fast, subject's action speeds up. Particles drift in light. Tension music continues to crescendo."

Clip 3: "[Camera dollies in for close-up reveal of key object]. Bell chimes. Frame holds. Strings resolve to soft note. Fade to black."
```

### Trailer producto / tech
```
Clip 1: "Slow orbit around product on dark surface, dramatic side lighting, product spec text materializes around it. Subtle ambient electronic music."

Clip 2: "Macro detail close-ups of textures, mechanisms, screen interactions. Pacing accelerates. Synth pulses."

Clip 3: "Pull back wide reveal of product in real-world context. Music resolves. Brand glow appears."
```

### Trailer humano / lifestyle
```
Clip 1: "Wide cinematic establishing shot, golden hour, character in environment, slow camera push-in toward face. Warm ambient soundscape."

Clip 2: "Mid-shot of character in action, gimbal smooth follow. Light music swells."

Clip 3: "Close-up of character looking at camera, slight smile, freeze frame. Music peaks."
```

## Costos típicos

| Trailer | Composición | USD |
|---|---|---|
| 15s · 3 clips × 5s @ 1080p | Suspense / cinematic | $4.62 |
| 18s · 3 clips × 5s @ 1080p + endcard | Con cartel final | $4.62 |
| 12s · 3 clips × 4s @ 720p Fast | Económico, máximo ahorro | $2.90 |
| 25s · 5 clips × 5s @ 1080p | Mid-tier productions | $7.55 |

## Checklist final antes de entregar

```
[ ] Imagen base de cada escena verificada visualmente (Read tool)
[ ] Cada clip cumple con el encuadre planeado
[ ] xfade entre todos los clips (no cortes duros)
[ ] Audio cruza suavemente entre clips (acrossfade)
[ ] Endcard incluye branding (@usuario / URL / logo)
[ ] Versión 9:16 + 1:1 exportadas
[ ] Caption bilingüe (ES + EN) preparada
[ ] Archivos intermedios identificados (no contaron como costo si solo son ffmpeg local)
[ ] Total real cobrado vs total estimado → diferencia explicada al usuario
```

## Recursos

- `/video-seedance` para entender pricing y endpoints de Seedance
- `/imagen-nano-banana` para entender el `/edit` endpoint y `image_urls`
- `/sfx-generar` si necesitas audio extra (no incluido en Seedance audio nativo)

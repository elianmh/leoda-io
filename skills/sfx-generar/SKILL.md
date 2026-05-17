---
name: sfx-generar
description: >
  Generar efectos de sonido (SFX) cinematográficos desde descripción de texto usando
  Stable Audio vía fal.ai. Use cuando el usuario diga "necesito un snap sound", "agrega
  sonido de explosión", "haz el SFX de viento", "/sfx-generar", o cuando se esté armando
  sound design de un video y haya que generar snaps, whooshes, ambient, footsteps, sparks,
  etc. desde cero.
metadata:
  version: "0.1.0"
---

# Generador de SFX — Stable Audio

Generar efectos de sonido (sin música) desde descripciones de texto. Ideal para sound design de reels: snaps, whooshes, ambient, footsteps, sparks, explosions, etc.

## Pre-requisitos

1. `FAL_KEY` configurada → si no, `/setup-leoda`
2. Decisión clara de qué efecto necesitas y cuánto debe durar

## Endpoint

`https://queue.fal.run/fal-ai/stable-audio`

## Llamada típica

```bash
curl -s -X POST "https://queue.fal.run/fal-ai/stable-audio" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Single sharp crisp finger snap sound, dramatic, cinematic reverb tail, no music",
    "seconds_total": 3
  }'
```

Devuelve `request_id`. Polling con:
```
https://queue.fal.run/fal-ai/stable-audio/requests/{request_id}/status
https://queue.fal.run/fal-ai/stable-audio/requests/{request_id}
```

**Respuesta**:
```json
{
  "audio_file": {
    "url": "https://.../tmp.wav",
    "content_type": "application/octet-stream",
    "file_name": "tmp.wav",
    "file_size": 529278
  }
}
```

| Parámetro | Tipo | Descripción |
|---|---|---|
| `prompt` | string | Descripción del efecto. Incluir `"no music"` para evitar música no deseada |
| `seconds_total` | int | Duración en segundos (3-10 típicamente) |

## Catálogo de prompts probados

### Snap finger
```
Single sharp crisp finger snap sound with cinematic reverb tail, dramatic, no music
```

### Time freeze / unfreeze whoosh
```
Cinematic time-stop whoosh, supernatural deep bass swell with shimmer, magical time freezing effect, no music
Reverse cinematic time-unfreeze whoosh, magical time resuming, supernatural reversal, no music
```

### Construction ambient (loop bed)
```
Active construction site ambient: distant hammering, drilling, metal cutting, outdoor backyard atmosphere, no music
```

### Welding sparks
```
Crackling bright welding sparks shower from metal cutting saw, gradually fading, no music
```

### Small explosion / debris pop
```
Small soft controlled debris pop, particle scatter, no music
```

### Footsteps
```
Confident heavy work boot footsteps walking slowly on concrete, four steps, no music
```

### Water / nature
```
Soft running water in a stream, peaceful ambient, no music
Ocean waves crashing on a beach, ambient atmospheric, no music
```

### Office / interior ambient
```
Quiet modern office ambient with subtle keyboard typing and HVAC hum, no music
```

### Whoosh / transition
```
Cinematic transition whoosh from left to right, fast swoosh sound effect, no music
```

## Reglas para buenos prompts

1. **Inglés siempre** — rinde mejor
2. **Termina con `"no music"`** para evitar música no deseada
3. **Describe TEXTURA** (sharp, soft, deep, bright, crackling)
4. **Describe ENTORNO** (cinematic, atmospheric, dramatic)
5. **Especifica DURACIÓN aproximada** en el prompt si es importante ("4 seconds", "fading after 2s")
6. **NO menciones notas musicales o instrumentos** — eso genera música

## Procesar la respuesta

1. Descarga el WAV: `curl -s -o sfx.wav "URL"`
2. Verifica duración: `ffprobe -v error -show_entries format=duration -of default=nw=1:nk=1 sfx.wav`
3. Convierte a MP3 si el video final lo necesita: `ffmpeg -i sfx.wav -c:a libmp3lame -b:a 192k sfx.mp3`

## Mezcla en video con ffmpeg

Para mezclar múltiples SFX + voz + ambient en una pista, usar `adelay` para timing y `amix` para combinar:

```bash
ffmpeg -y \
  -i voice.mp3 \
  -i ambient.wav \
  -i snap.wav \
  -i freeze.wav \
  -filter_complex "\
[0:a]volume=1.0[voice];\
[1:a]aloop=loop=3:size=441000,atrim=duration=31,volume=0.12,highpass=f=200,lowpass=f=4000[amb];\
[2:a]adelay=2800|2800,volume=0.9[snap];\
[3:a]adelay=3000|3000,volume=0.6[freeze];\
[voice][amb][snap][freeze]amix=inputs=4:duration=longest:normalize=0[aout]" \
  -map "[aout]" final_audio.mp3
```

**Volúmenes recomendados** (con voz en 1.0):
- Ambient bed: 0.10–0.15 (muy bajo, filtrado para no competir con voz)
- Footsteps: 0.20–0.25
- Snaps/whooshes: 0.85–0.95 (impacto máximo en momentos clave)
- Sparks/crackle: 0.20–0.30 (textura sin sobreponer)
- Explosion: 0.30–0.40 (suave si está congelada)

## Costos

- ~$0.05–0.10 USD por SFX de 3-10 segundos
- Set completo de 7 SFX para un reel cinematográfico: ~$0.50 USD

## Alternativa: ElevenLabs Sound Effects

ElevenLabs también tiene un generador de SFX (`fal-ai/elevenlabs/sound-effects`), pero **al momento de escribir este skill la integración fal.ai usa un model_id obsoleto** (`eleven_text_to_sound_v0` cuando ElevenLabs espera `v2`) y falla con error 400. **Usa Stable Audio**.

Cuando fal.ai actualice la integración con ElevenLabs SFX, puede ser preferible porque ElevenLabs entrena específicamente para SFX cinematográficos. Mientras tanto, Stable Audio entrega calidad muy decente.

## Recursos

- [Stable Audio fal.ai](https://fal.ai/models/fal-ai/stable-audio)

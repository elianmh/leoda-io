---
name: voz-clon
description: >
  Clonar una voz desde una muestra de audio y generar narración con esa voz personalizada
  usando MiniMax Voice Clone + Speech vía fal.ai. Use cuando el usuario diga "clona la
  voz de X", "quiero que la narración sea con la voz real de Y", "/voz-clon", o cuando
  suba una muestra de audio (mp3, ogg, wav) y quiera narración con esa misma voz.
  La voz clonada queda guardada con un voice_id para uso futuro sin re-clonar.
metadata:
  version: "0.1.0"
---

# Voz clonada — MiniMax

Clonar la voz de una persona desde una muestra de audio y usar esa voz clonada para generar narraciones en español, inglés o multilingüe. El proceso es de **dos pasos**: (1) clonar la voz para obtener un `voice_id`, (2) usar ese `voice_id` en TTS.

## Pre-requisitos

1. `FAL_KEY` configurada → si no, `/setup-leoda`
2. **Muestra de audio mínima de 10 segundos**, idealmente 20-60 segundos
3. El audio debe ser **clara, una sola voz, poco ruido de fondo**

## Paso 1 — Clonar la voz

**Endpoint**: `https://fal.run/fal-ai/minimax/voice-clone`

```bash
# Convertir audio local a data URL si es necesario
B64=$(base64 -w 0 "/ruta/al/audio.mp3")
AUDIO_URL="data:audio/mp3;base64,${B64}"

# Solicitar el clon
curl -s -X POST "https://fal.run/fal-ai/minimax/voice-clone" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d "{
    \"audio_url\": \"${AUDIO_URL}\",
    \"noise_reduction\": true,
    \"need_volume_normalization\": true
  }"
```

| Parámetro | Tipo | Descripción |
|---|---|---|
| `audio_url` | string | URL HTTPS o data URL del audio. Min 10s |
| `noise_reduction` | bool | Limpiar ruido de fondo antes de clonar (recomendado `true`) |
| `need_volume_normalization` | bool | Normalizar volumen (recomendado `true`) |

**Respuesta**:

```json
{
  "custom_voice_id": "Voiced3d396771778967239",
  "audio": {
    "url": "https://.../preview.mp3"
  }
}
```

**¡Guarda el `custom_voice_id`!** Es la referencia permanente a la voz clonada. Lo necesitas en el Paso 2.

**Tipos de errores en clone**:
- `audio_duration_too_short` → audio menor a 10s. Pide más material al usuario
- Audio con mucho ruido → bajar calidad de clon. Mejor mandar mejor sample
- Múltiples voces en la muestra → confunde el clon. Pide audio de UNA persona

## Combinar varias muestras de audio en una sola

Si tienes 3 audios cortos del usuario (ej. 3 WhatsApp voice notes de 5-8s c/u), combínalos en uno solo de >10s con ffmpeg:

```bash
# Crear list para concat
ls audio1.mp3 audio2.mp3 audio3.mp3 | sed "s/^/file '/" | sed "s/$/'/" > concat.txt

# Concatenar (re-encoding para uniformidad)
ffmpeg -y -i audio1.mp3 -ar 22050 -ac 1 -c:a libmp3lame -b:a 128k p1.mp3
ffmpeg -y -i audio2.mp3 -ar 22050 -ac 1 -c:a libmp3lame -b:a 128k p2.mp3
ffmpeg -y -i audio3.mp3 -ar 22050 -ac 1 -c:a libmp3lame -b:a 128k p3.mp3

# Concat
echo "file 'p1.mp3'
file 'p2.mp3'
file 'p3.mp3'" > concat.txt
ffmpeg -y -f concat -safe 0 -i concat.txt -c copy combined.mp3
```

## Paso 2 — Generar narración con la voz clonada

**Endpoint**: `https://fal.run/fal-ai/minimax/speech-02-hd`

```bash
curl -s -X POST "https://fal.run/fal-ai/minimax/speech-02-hd" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d "{
    \"text\": \"En cada proyecto que entramos, todo tiene su orden.\",
    \"voice_setting\": {
      \"voice_id\": \"Voiced3d396771778967239\",
      \"speed\": 1.0,
      \"vol\": 1.0,
      \"pitch\": 0
    },
    \"audio_setting\": {
      \"sample_rate\": 32000,
      \"bitrate\": 128000,
      \"format\": \"mp3\",
      \"channel\": 1
    },
    \"language_boost\": \"Spanish\"
  }"
```

| Parámetro | Tipo | Valores |
|---|---|---|
| `text` | string | Texto a sintetizar (max ~500 chars por llamada) |
| `voice_setting.voice_id` | string | El `custom_voice_id` del Paso 1 |
| `voice_setting.speed` | float | 0.5–2.0, default 1.0 |
| `voice_setting.vol` | float | 0.0–10.0, default 1.0 |
| `voice_setting.pitch` | int | -12 a +12 semitones, default 0 |
| `audio_setting.sample_rate` | int | 8000, 16000, 22050, 24000, 32000, 44100 |
| `audio_setting.bitrate` | int | 32000, 64000, 128000, 256000 |
| `audio_setting.format` | string | `mp3`, `wav`, `pcm`, `flac` |
| `audio_setting.channel` | int | 1 (mono), 2 (stereo) |
| `language_boost` | string | `Spanish`, `English`, `Chinese`, `Japanese`, `Korean`, `French`, etc. |

**Importante**: `sample_rate`, `bitrate`, `channel` son **enteros** (no strings). El error literal_error indica string en lugar de int.

## Modelos alternativos para TTS

| Modelo | Endpoint | Cuándo usar |
|---|---|---|
| **MiniMax Speech-02 HD** | `fal-ai/minimax/speech-02-hd` | **Best quality, voice clone support** |
| MiniMax Speech-02 Turbo | `fal-ai/minimax/speech-02-turbo` | Más rápido, calidad ligeramente menor |
| ElevenLabs Multilingual v2 | `fal-ai/elevenlabs/tts/multilingual-v2` | Voces preset (Adam, Daniel, etc.) sin clone |

Para narración con voz clonada → **siempre MiniMax Speech-02 HD**.

## Procesar la respuesta TTS

```json
{
  "audio": {
    "url": "https://.../speech.mp3",
    "content_type": "audio/mpeg",
    "file_size": 49620
  },
  "duration_ms": 2988
}
```

1. Descarga el MP3
2. Verifica duración: `ffprobe -v error -show_entries format=duration -of default=nw=1:nk=1 archivo.mp3`
3. Lo mezclas en el video con ffmpeg adelay y amix

## Costos

- **Clon de voz** (Paso 1): ~$0.30 USD una vez
- **TTS por generación** (Paso 2): ~$0.10 USD por frase corta (3-5s)

Una vez clonada, **reutiliza el `voice_id`** para todas las narraciones futuras sin re-clonar.

## Tips para narración cinematográfica

1. **Frases cortas** (4-12 palabras c/u). Las largas suenan menos naturales
2. **Puntuación importa** — usa comas y puntos como pausas naturales
3. **Múltiples frases** = mejor que una sola larga (más control de timing)
4. **Tono serio profesional**: speed 0.95, vol 1.0, pitch 0
5. **Tono confiado/jocoso**: speed 1.0-1.05, pitch 0-1

## Procesamiento post-TTS para "personalidad"

Para darle weight cinematográfico a la voz clonada, procésala con ffmpeg ANTES de mezclar:

```bash
ffmpeg -y -i voz_cruda.mp3 \
  -af "highpass=f=80,lowpass=f=13000,equalizer=f=180:t=q:w=1.2:g=2,equalizer=f=3000:t=q:w=2:g=2.5,acompressor=threshold=-18dB:ratio=4:attack=8:release=180:makeup=3,volume=1.35" \
  voz_procesada.mp3
```

Esto agrega:
- Highpass/lowpass para limpiar grave/agudo digital
- EQ +2dB @ 180Hz (warmth/cuerpo)
- EQ +2.5dB @ 3kHz (presencia/claridad)
- Compresión 4:1 (consistencia)
- Volume boost +35%

**No agregues echo/reverb** — hace que la voz suene "de fondo". Si quieres espacio, usa un reverb cinematográfico de cola muy corta (50ms max).

## Recursos

- [MiniMax Voice Clone fal.ai](https://fal.ai/models/fal-ai/minimax/voice-clone)
- [MiniMax Speech-02 HD fal.ai](https://fal.ai/models/fal-ai/minimax/speech-02-hd)

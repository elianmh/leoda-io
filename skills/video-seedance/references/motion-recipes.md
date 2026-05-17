# Motion recipes — Seedance 2.0

Recetas de prompts cinematográficos optimizadas para Seedance 2.0 image-to-video. Cada receta aprovecha movimiento, audio nativo y/o end frame control.

---

## 1. OneShot continuo estilo ElevenLabs / Quicksilver

Para el efecto "tiempo congelado" con personaje moviéndose por escena estática.

```
Single continuous unbroken cinematic tracking shot, no cuts. The [SUBJECT] walks
forward and rightward through the scene. At 2-3 seconds they raise their right hand
and snap their fingers. Immediately at the snap, everything around them FREEZES in
time — workers and objects suspend mid-motion, dust particles freeze in mid-air,
subtle blue tint covers the frozen world while warm light stays on the subject. They
continue walking, interacting with [SUSPENDED ELEMENT]. They snap again — time
UNFREEZES, everything returns to normal motion. Premium documentary cinematic
continuous single take, smooth camera dolly forward, no cuts.
```

**Setup**: `duration: 10-15`, `generate_audio: true`, `aspect_ratio: 9:16`.

---

## 2. Producto hero con audio ambient

```
Slow cinematic push-in toward the product, subtle warm light pulsing on the surface,
fine dust particles drifting through warm light, gentle ambient atmospheric sound
of soft wind and distant city, premium product film aesthetic
```

**Setup**: `duration: 5-7`, `generate_audio: true`, `aspect_ratio: 1:1` or `9:16`.

---

## 3. Diálogo con lip-sync (presentación a cámara)

Para que el personaje hable EN PANTALLA con labios sincronizados.

```
Cinematic medium close-up of [SUBJECT] looking directly at camera with warm confident
expression. They say with [Cuban/Mexican/American] Spanish accent: "[FRASE EXACTA AQUÍ]".
Soft natural light on their face, subtle micro camera drift, premium documentary
interview style, lip movements perfectly synced to spoken words
```

**Setup**: `duration: 5-8`, `generate_audio: true`. La frase entre comillas se renderiza con lip-sync.

---

## 4. Transformación con start + end frame

Para "antes → después" o transición específica.

```
Smooth cinematic time-lapse transition from the starting scene to the ending scene,
soft cross-dissolve with light shifting gradually, atmospheric particles drifting
naturally, premium documentary feel
```

**Setup**: Pasar AMBOS `image_url` (start) y `end_image_url` (end), `duration: 6-10`.

---

## 5. Action sequence con física realista

```
Dynamic cinematic shot with realistic physics: [ACTION HAPPENING], dust scattering
naturally, fabric/hair moving authentically with momentum, sparks/water/particles
behaving with proper weight and gravity, premium action cinematography, with
matching SFX of the action
```

**Setup**: `duration: 5-8`, `generate_audio: true`. Seedance 2.0 genera SFX que matchean la acción.

---

## 6. Reveal hero con crane up

```
Smooth cinematic crane up movement starting at ground level looking at [SUBJECT],
slowly rising and tilting down to reveal the full majestic scene below, warm
golden hour lighting, gentle orchestral build-up audio swelling as the reveal
completes, premium cinema feel
```

**Setup**: `duration: 8-10`, `generate_audio: true`, vertical o horizontal según uso.

---

## 7. Frozen time (sin Quicksilver activo, solo estático)

```
Camera slowly drifts through the scene with very subtle parallax. The entire scene
is COMPLETELY FROZEN in time — no workers move, no dust drifts, no particles fall,
all sparks suspended motionless mid-air, supernatural blue-tinted time-stop
atmosphere, no audio except a subtle deep bass freeze drone
```

**Setup**: `duration: 5-10`, `generate_audio: true` con prompt del drone freeze.

---

## 8. Lifestyle handheld auténtico (estilo TikTok/UGC)

```
Authentic handheld smartphone-style motion with natural micro-shake, [SUBJECT] reacts
naturally to [PRODUCT/CONTEXT], raw documentary feel, ambient room sounds, no
studio polish, organic social media authenticity
```

**Setup**: `duration: 5-7`, `generate_audio: true`. Bueno para anuncios de UGC apparent.

---

## 9. Slow motion macro reveal

```
Ultra slow motion macro cinematography, [DETAIL] caught in slow time, light catching
on every surface, fine particles suspended in air, ASMR ambient audio of subtle
sound of the action, premium beauty/luxury feel
```

**Setup**: `duration: 5-8`, `generate_audio: true`, `1:1` o `9:16`.

---

## 10. Time-lapse atmospheric

```
Smooth cinematic time-lapse showing the sky transitioning from golden hour through
sunset to dusk, clouds drifting across the frame, light gradually deepening,
ambient natural sounds of wind and distant nature, premium atmospheric documentary
```

**Setup**: `duration: 6-10`, `generate_audio: true`. Bueno para B-roll de tiempo.

---

## Reglas de oro Seedance 2.0

1. **`generate_audio: true` siempre** — no cuesta más y eleva la producción
2. **Una intención por clip** — si pides 4 cosas distintas, la calidad baja
3. **Para diálogo lip-sync** — frase corta entre comillas, especifica acento
4. **Para OneShot continuo** — di "single continuous unbroken shot, no cuts" al inicio del prompt
5. **Para 1080p** usa Standard tier; para 720p Fast es 20% más barato
6. **Duration entre 4-15s** — fuera de eso da error. `auto` deja al modelo decidir (usualmente 5s)
7. **Si quieres reproducibilidad** — pasa `seed: <numero>`. Variaciones menores aún ocurren
8. **End frame** — para morphs/transformaciones, pasa `end_image_url`. El modelo interpola
9. **Negative prompts** — escribe `no [X], no [Y]` directamente en prompt, no hay campo separado
10. **Para texto sobre imagen** — Seedance NO escribe texto bien. Texto siempre va en post (CapCut/ffmpeg)

---
name: calendario-publicaciones
description: >
  Armar un calendario semanal de publicaciones con horarios óptimos por plataforma
  y agrupar los assets (imagen/video/captions) por día. Use cuando el usuario diga
  "haz el calendario", "agenda los posts", "calendario de publicaciones",
  "/calendario-publicaciones", o cuando ya tenga los assets + captions listos y
  necesite organizarlos para publicar durante la semana o el mes.
metadata:
  version: "0.1.0"
---

# Calendario de publicaciones

Armar un calendario de contenidos (semanal por defecto, mensual si el usuario lo pide) que asigne **qué asset + qué caption** se publica **dónde y a qué hora**. La salida es un archivo CSV/XLSX listo para importar a herramientas de scheduling (Later, Metricool, Hootsuite, Buffer) o para gestión manual.

## Input necesario

1. **Lista de assets generados** (imágenes y/o videos por plataforma) — usa lo que esté en `outputs/imagenes/<campana>/` y `outputs/videos/<campana>/`.
2. **Captions** correspondientes — desde `outputs/captions/<campana>-*.md`.
3. **Rango de fechas** — semana / mes específico. Si no lo dice el usuario, asume la **próxima semana calendario**.
4. **Frecuencia** por plataforma — si no la indica, usa los defaults de abajo.
5. **Zona horaria** del público objetivo — si no la dice, usa la zona del usuario (en este caso, asume Bogotá/Medellín UTC-5 a menos que el brief diga otra cosa).

## Frecuencia recomendada por plataforma (default)

| Plataforma | Frecuencia |
|---|---|
| Instagram (Reels + Feed) | 4–6 por semana |
| TikTok | 5–7 por semana |
| YouTube Shorts | 3–5 por semana |
| Facebook | 3–5 por semana |
| LinkedIn | 2–3 por semana |
| WhatsApp Status | 1 por día, máx 5 por semana |

## Horarios óptimos (referencia LATAM, ajustar por zona)

| Plataforma | Mejores horarios |
|---|---|
| Instagram Reels | 11:00–13:00 y 19:00–21:00 |
| TikTok | 18:00–22:00 (peak 19:00–21:00) |
| YouTube Shorts | 17:00–19:00 entre semana, 11:00–14:00 fin de semana |
| Facebook | 9:00–11:00 y 13:00–15:00 |
| LinkedIn | 8:00–10:00 martes a jueves |
| WhatsApp Status | 12:00–14:00 (almuerzo) y 20:00–22:00 |

Si el brief indica una zona horaria distinta o un público B2B internacional, **ajusta** estos rangos.

## Estructura del calendario (output)

Genera un archivo **CSV** con esta tabla (usa la skill `xlsx` solo si el usuario pide específicamente Excel):

| Día | Fecha | Hora | Plataforma | Tipo de post | Asset (ruta) | Caption (ruta o inline ES) | Caption EN | Hashtags | Estado |
|---|---|---|---|---|---|---|---|---|---|
| Lunes | 2026-05-18 | 11:30 | Instagram Reels | Reel 9:16 | `imagenes/cafe/reel-hero.mp4` | "Mañana de espresso..." | "Morning espresso..." | #cafe #medellin #coffee | Programado |
| Lunes | 2026-05-18 | 19:00 | TikTok | Video UGC 9:16 | `videos/cafe/tiktok-ugc.mp4` | ... | ... | ... | Programado |
| ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

## Reglas de armado

1. **Una historia por semana** — si la campaña tiene 3 mensajes clave, distribuye uno por cada 2-3 días para mantener coherencia narrativa.
2. **No clones el mismo asset en todas las redes el mismo día**. Decala publicaciones cruzadas con al menos 4 horas entre plataformas, y rota qué asset va en cuál.
3. **Días pico**: martes, miércoles y jueves suelen rendir mejor en B2B/LinkedIn. Viernes-domingo en B2C/lifestyle.
4. **WhatsApp Status**: pon máximo 1 por día. Es íntimo, satura rápido.
5. **Combina formatos** dentro de cada plataforma — alterna Reels con carruseles 1:1 en Instagram, no solo Reels.
6. **Reserva el primer slot** para el hook más fuerte de la campaña (mejor hora del lunes o del primer día del rango).

## Generación del archivo

Usa la herramienta Bash para escribir el CSV directamente:

```bash
cat > /ruta/outputs/calendarios/<nombre-campana>-<YYYY-MM-DD>.csv <<'CSV'
Dia,Fecha,Hora,Plataforma,Tipo,Asset,Caption ES,Caption EN,Hashtags,Estado
Lunes,2026-05-18,11:30,Instagram Reels,Reel 9:16,imagenes/cafe/reel-hero.mp4,"...","...",#cafe #medellin,Programado
...
CSV
```

O si el usuario pidió Excel, invoca la skill `xlsx`.

## Resumen visual

Después del CSV, presenta un resumen condensado al usuario:

```
📅 Calendario semana del 2026-05-18 — Campaña "Café de Altura"

Lunes (3 posts)    · IG Reels 11:30 · TikTok 19:00 · WA Status 13:00
Martes (4 posts)   · LinkedIn 09:00 · IG Feed 12:00 · TikTok 19:30 · YT Shorts 18:00
Miércoles (3 posts)· IG Reels 11:30 · TikTok 20:00 · FB 14:00
Jueves (2 posts)   · LinkedIn 09:00 · IG Reels 20:00
Viernes (4 posts)  · TikTok 19:00 · IG Feed 13:00 · YT Shorts 17:30 · WA Status 20:30
Sábado (2 posts)   · IG Reels 12:00 · TikTok 21:00
Domingo (1 post)   · IG Reels 20:00

Total: 19 publicaciones
```

## Después

Pregunta si el usuario quiere:
- Ajustar horarios manualmente
- Exportar también a otro formato (Excel, Google Sheets, ICS)
- Mover el calendario a su herramienta de scheduling (si tiene Metricool/Later/etc. conectado, eso requeriría un MCP separado — proponerlo si aplica)

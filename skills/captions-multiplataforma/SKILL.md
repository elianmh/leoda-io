---
name: captions-multiplataforma
description: >
  Generar captions bilingües (ES + EN) adaptados al tono y límites de cada
  plataforma (Instagram, TikTok, YouTube Shorts, Facebook, LinkedIn, WhatsApp Status).
  Use cuando el usuario diga "escribe los captions", "necesito el copy del post",
  "captions para Reels", "/captions-multiplataforma", o cuando ya tenga assets
  (imagen/video) generados y necesite el texto que los acompaña.
metadata:
  version: "0.1.0"
---

# Captions multiplataforma

Generar el copy del post para cada plataforma destino, respetando límites de caracteres, formato, tono y mejores prácticas de cada red. Siempre **bilingüe ES + EN**.

## Input necesario

Antes de escribir, necesitas:

1. El **brief** de la campaña (de `/brief-creativo`) — concepto, hooks, CTA, tono, mensajes clave.
2. La lista de **plataformas destino**.
3. (Opcional) Restricciones de marca o palabras prohibidas.

Si no hay brief, pregunta al usuario por el concepto y los puntos clave antes de escribir captions a ciegas.

## Reglas por plataforma

| Plataforma | Límite caracteres (recomendado) | Hashtags | Emojis | CTA típico |
|---|---|---|---|---|
| Instagram (Reels & Feed) | 125–150 visibles (max 2.200) | 3–7, al final | sí, moderado | "Guarda", "Comenta", "Etiqueta a..." |
| TikTok | 100–150 (max 4.000) | 3–5, integrados en el texto | sí, abundante | "Hazlo tú", "Pruébalo", trending sounds |
| YouTube Shorts | 80–120 (titulo) + descripción | 2–4 en descripción | mínimo | "Suscríbete", "Mira el largo" |
| Facebook | 80–120 visibles, prosaico | 0–2 | moderado | "Compra", "Reserva", "Más info" |
| LinkedIn | 150–250 visibles (max 3.000) | 3–5 al final, profesionales | mínimo | "¿Qué opinas?", "Comparte tu experiencia" |
| WhatsApp Status | 60–100, directo | 0 | sí | "Responde con [emoji]", "Toca el link" |

## Plantilla de output

Genera siempre este formato para que el usuario pueda copiar/pegar:

```markdown
# Captions — <Nombre campaña>

## Instagram Reels

**ES**:
<hook 1ª línea — 6-10 palabras que enganchen>

<2-3 líneas de cuerpo · valor + emoción>

<CTA + emoji>

<hashtags al final: #...>

**EN**:
<same structure in English>

---

## TikTok

**ES**:
<hook explícito + emoji al inicio>
<cuerpo: 1-2 frases máx, lenguaje hablado>
<CTA: trending phrase o pregunta>
<hashtags integrados: #foo #bar>

**EN**:
<same>

---

## YouTube Shorts

**Título ES** (max 60 chars): <...>
**Título EN** (max 60 chars): <...>
**Descripción ES**: <2-3 líneas + hashtags>
**Descripción EN**: <2-3 líneas + hashtags>

---

## Facebook

**ES**: <texto prosaico, 80-120 chars, CTA al final>
**EN**: <same>

---

## LinkedIn

**ES**:
<hook profesional, 1ª línea muy fuerte>

<2-4 líneas de insight o aprendizaje>

<pregunta abierta o CTA conversacional>

<#hashtag1 #hashtag2 #hashtag3>

**EN**:
<same structure>

---

## WhatsApp Status

**ES**: <una línea directa con emoji + CTA, max 100 chars>
**EN**: <same>
```

## Reglas de escritura

1. **Hook en la primera línea**. En IG/TikTok/LinkedIn solo se ve la primera línea antes del "ver más" — ahí va lo más fuerte.
2. **No copy-paste literal entre plataformas**. Cada red tiene tono propio: LinkedIn no usa el mismo lenguaje que TikTok.
3. **Emojis con criterio**. TikTok/IG/WA aceptan abundantes; LinkedIn casi ninguno; Facebook moderado.
4. **Hashtags relevantes, no spam**. Mezcla 1 nicho + 1 medio + 1 amplio (ej: `#cafeMedellin #cafedealtura #coffee`).
5. **Cumple con regulaciones**. Si el producto es regulado (alcohol, salud, finanzas, etc.) no hagas claims sin respaldo. Si el usuario lo pide, agrega el disclaimer pertinente.
6. **Sin "el mejor", "único", "increíble"** salvo que el brief lo justifique.
7. **CTA específico**, no genérico. ❌ "compra ya" → ✅ "Lleva 2x1 hasta el domingo, link en bio".

## Variantes A/B también para captions

Si el usuario quiere testear copy: produce 2 variantes por plataforma — una variante apela a **dolor/problema** y la otra a **aspiración/resultado**. Marca cuál es cada una.

## Guardar el output

1. Escribe a `outputs/captions/<nombre-campana>-<YYYY-MM-DD>.md`
2. Comparte el archivo con `computer://...`
3. Si las variantes de imagen/video ya están generadas, agrupa el caption junto a su asset correspondiente en una tabla maestra.

## Después

Pregunta al usuario si quiere armar el **calendario de publicaciones** con `/calendario-publicaciones` o si quiere ajustar tono/hashtags antes.

---
name: brief-creativo
description: >
  Generar el brief creativo y los copys de una campaña de marketing (e-commerce,
  redes sociales, paid ads, branding). Use cuando el usuario diga "brief",
  "brief creativo", "campaña", "necesito un concepto", "copy para mi producto",
  "/brief-creativo", o cuando describa un producto/servicio y quiera material
  publicitario. El brief es siempre BILINGÜE (ES + EN) y queda como input
  estructurado para las otras skills del plugin (imagen, video, captions, etc.).
metadata:
  version: "0.1.0"
---

# Brief creativo

Crear un brief de campaña estructurado y bilingüe (Español + English) a partir de la información que da el usuario sobre su producto, servicio o marca. La salida debe servir como input directo para las skills de imagen (`/imagen-nano-banana`), video (`/video-seedance`), variantes (`/variantes-ab`), captions (`/captions-multiplataforma`) y calendario (`/calendario-publicaciones`).

## Información mínima que necesitas del usuario

Antes de generar el brief, asegúrate de tener:

1. **Producto / servicio**: qué se promociona
2. **Audiencia objetivo**: demografía, intereses, dolor que resuelve
3. **Objetivo de la campaña**: awareness, conversión, lanzamiento, retención
4. **Tono de marca**: aspiracional, divertido, técnico, cálido, premium, casual
5. **Plataformas destino**: Instagram Reels, TikTok, YouTube Shorts, Facebook, LinkedIn, WhatsApp Status (puede ser una o varias)
6. **Restricciones**: presupuesto, claims regulados, idioma del mercado, palabras prohibidas

Si falta algo crítico, pregúntalo con `AskUserQuestion` o un formulario de elicitación. **No inventes** datos que no diste el usuario — si no sabes el tono, pregunta.

## Estructura del brief (output)

Genera siempre este formato exacto. Toda sección debe tener ambas versiones ES / EN.

```markdown
# Brief — <Nombre de la campaña>

**Fecha**: <YYYY-MM-DD>
**Producto / Product**: <...>
**Cliente / Client**: <marca o "personal">

## 1. Concepto / Concept
- **ES**: <una frase, 15–25 palabras, idea central>
- **EN**: <one sentence, 15–25 words, central idea>

## 2. Insight de audiencia / Audience insight
- **ES**: <qué siente / piensa / necesita la audiencia>
- **EN**: <same in English>

## 3. Promesa / Promise
- **ES**: <beneficio principal, claro y verificable>
- **EN**: <main benefit, clear and verifiable>

## 4. Tono / Tone
<3–5 adjetivos · 3–5 adjectives>

## 5. Mensajes clave / Key messages
1. **ES**: <mensaje 1> / **EN**: <message 1>
2. **ES**: <mensaje 2> / **EN**: <message 2>
3. **ES**: <mensaje 3> / **EN**: <message 3>

## 6. Hooks (primeros 3 segundos) / Hooks (first 3 seconds)
- **ES**: 3 ganchos de 4–8 palabras
- **EN**: 3 hooks of 4–8 words

## 7. CTA
- **ES**: <call to action breve>
- **EN**: <short call to action>

## 8. Prompts visuales sugeridos / Visual prompts
Estos prompts se usarán en /imagen-nano-banana. Escríbelos en INGLÉS aunque el resto sea bilingüe, porque los modelos rinden mejor en EN.

1. <prompt para shot principal / hero shot>
2. <prompt para shot de detalle / detail shot>
3. <prompt para shot de lifestyle / lifestyle shot>

Cada prompt debe incluir: sujeto, composición, iluminación, estilo, paleta, formato (vertical/cuadrado/horizontal).

## 9. Plataformas y formatos / Platforms & formats
| Plataforma | Aspect | Duración (si video) | Notas |
|---|---|---|---|
| <Instagram Reels> | 9:16 | 7–15s | ... |
| ... | ... | ... | ... |

## 10. Métricas de éxito / Success metrics
<2–3 KPIs concretos: CTR esperado, alcance, conversiones, etc.>
```

## Reglas de escritura

- **Concisión**: nada de relleno. Si una sección no aporta, déjala con `n/a` antes que inventarla.
- **Verificable**: no prometer cosas que el producto no cumple. Si el usuario no dio claims, no los inventes.
- **No usar adjetivos vacíos** en español ("increíble", "único", "el mejor") salvo que el usuario lo pida explícito.
- **Hooks**: deben generar curiosidad o promesa concreta, no ser slogans abstractos.
- **Prompts visuales en inglés**: aunque el resto del brief sea bilingüe, los prompts para Nano Banana van en inglés (los modelos rinden mejor así).

## Guardar el brief

Después de generar el brief:

1. Guárdalo como archivo Markdown en `outputs/briefs/<nombre-campana>-<YYYY-MM-DD>.md`
2. Comparte el archivo con un link `computer://...` al usuario
3. Pregunta si quiere continuar con: `/imagen-nano-banana` (siguiente paso natural), o si prefiere ajustar el brief primero

## Ejemplo mínimo de invocación

> Usuario: "necesito un brief para mi café de especialidad orgánico, audiencia 25-40 urbanos amantes del café, objetivo awareness en Instagram y TikTok"

→ Reúne lo faltante (tono, restricciones), luego genera el brief con todas las 10 secciones, guarda el archivo, y propone seguir con `/imagen-nano-banana`.

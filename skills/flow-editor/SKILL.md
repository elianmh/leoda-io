---
name: flow-editor
description: >
  Construir flows visuales tipo ElevenLabs Flows / n8n / ComfyUI dentro de Claude Cowork.
  Editor de nodos con canvas infinito, zoom/pan, toolbar dock inferior, Run individual por
  nodo, Run from here downstream, Flows Agent chat conversacional, multi image refs para
  consistency, composition node con timeline, drag-drop de archivos del desktop.
  Use cuando el usuario diga "abre el flow", "construye un flow", "/flow-editor",
  "haz un editor visual", "imita ElevenLabs Flows", o cuando vaya a crear un pipeline
  multi-paso con varios modelos generativos donde quiera control granular nodo por nodo.
metadata:
  version: "1.0.0"
---

# Flow Editor · Canvas visual tipo ElevenLabs Flows

Skill para abrir un artifact en el sidebar de Cowork con un editor visual completo de pipelines de IA generativa.

## Cuándo usar este skill

- Pipeline multi-paso con 3+ nodos generativos
- Necesitas iterar por nodo individual (re-run sin regenerar todo)
- Trabajos con multiple image references para consistency (character, escena)
- Quieres ver el grafo completo del flow antes de gastar créditos
- El usuario va a hacer batch variations con un mismo flow

## Cuándo NO usar

- Tarea simple de 1-2 generaciones → usa skill específico (`/imagen-nano-banana`, `/video-seedance`)
- Storyboard secuencial sin necesidad de grafo → usa `/storyboard-trailer`

## Cómo se invoca

### Flujo rápido (recomendado — usa el template embebido)

El plugin incluye un **template HTML completo** del flow editor en:
`skills/flow-editor/template/flow.html` (~80 KB)

Cuando el usuario invoca `/flow-editor`, Claude DEBE:

1. **Leer el template** desde la ruta del plugin:
   ```
   {PLUGIN_ROOT}/skills/flow-editor/template/flow.html
   ```
   Donde `{PLUGIN_ROOT}` es el directorio donde Cowork instaló el plugin (varía por usuario).

2. **Copiar el archivo** al outputs folder del usuario:
   ```bash
   cp {PLUGIN_ROOT}/skills/flow-editor/template/flow.html {OUTPUTS_DIR}/leoda-flow.html
   ```

3. **Crear el artifact** apuntando al archivo copiado:
   ```
   mcp__cowork__create_artifact(
     id: "leoda-flow-editor",
     html_path: "{OUTPUTS_DIR}/leoda-flow.html",
     description: "Editor visual de pipelines IA generativa estilo ElevenLabs Flows",
     mcp_tools: ["mcp__cowork__create_artifact", "mcp__cowork__update_artifact"]
   )
   ```

4. **Confirmar al usuario** que el artifact está disponible en el sidebar.

Total: 3 tool calls, ~2 segundos. Idéntico para cada usuario.

### Flujo interactivo (cuando Claude ejecuta nodos)

5. El usuario interactúa con el canvas (drag nodos, conecta, edita props)
6. Cuando hace Run en un nodo → el artifact dispara `window.sendPrompt()` a Claude con un JSON del nodo
7. Claude aplica PRE-FLIGHT CHECKLIST (ver `/video-seedance`) y ejecuta solo lo aprobado
8. Tras cada nodo completado, Claude llama `mcp__cowork__update_artifact` con el state actualizado (thumbnails como data URLs base64, status="done")

## Arquitectura del editor

### Tipos de nodos disponibles

| Categoría | Nodo | Modelo | Inputs | Outputs |
|---|---|---|---|---|
| **Inputs** | Image Ref | (usuario) | — | image |
| | Audio Ref | (usuario) | — | audio |
| | Text | (usuario) | — | text |
| **Image** | Nano Banana 2 | Gemini 2.5 Flash Image | image_refs (multi) | image |
| | NB /edit | Gemini /edit | image_refs (multi) | image |
| **Video** | Seedance 2.0 | bytedance/seedance-2.0 | start_frame, end_frame (opt) | video |
| **Audio** | Voice Clone | MiniMax | sample audio | voice_id |
| | TTS Multilingual | Eleven v2 / MiniMax | text, voice_id (opt) | audio |
| | Stable Audio SFX | Stable Audio | prompt (opt) | audio |
| | Eleven Music | Eleven Music | prompt (opt) | audio |
| **AI** | LLM | Claude sonnet/opus/haiku | context (opt) | text |
| **Compose** | Composition | ffmpeg local | N videos + N audios | final mp4 |

### Features de UX (parityf con ElevenLabs Flows)

1. **Toolbar dock inferior centrada** — click un icono = agregar nodo en el centro del viewport
2. **Drag-and-drop de archivos** del escritorio → crea nodo Reference Image automáticamente
3. **Zoom con scroll wheel** + botones zoom in/out/fit/reset
4. **Pan con drag** del canvas vacío
5. **Conexiones Bezier** con validación de tipos (image → image, text → text, etc.)
6. **Multi input ports** — Nano Banana acepta múltiples image_refs (consistency de personaje)
7. **Cost tracker** en topbar en tiempo real (suma sólo nodos no-done)
8. **Run individual** — botón ▶ Run dentro de cada nodo
9. **Run from here** — botón en context menu, ejecuta el nodo + todo el downstream
10. **Run All** — botón en topbar, ejecuta todo lo pending
11. **Double-click rename** o input "Nombre" en panel de propiedades
12. **Right-click context menu** con: Run / Run from here / Rename / Duplicate / Properties / Replace input / Download / Delete
13. **Panel de propiedades** deslizable desde la derecha al seleccionar un nodo
14. **Status visual por nodo**: idle / queued / running / done / failed con colores y dots animados
15. **Flows Agent chat** abajo-derecha — escribe instrucciones en lenguaje natural y el agente edita el grafo (envía sendPrompt al Claude del chat)
16. **Templates** — botón "Create Template" + Export JSON para guardar/compartir flows
17. **Persistence** — localStorage `leoda_flow_v3` guarda el state entre sesiones

## Comunicación bidireccional artifact ↔ Claude

El artifact corre sandboxed pero puede llamar a Claude vía `window.sendPrompt(text)`.

### Cuando el usuario hace Run en un nodo:

```javascript
function runNode(id) {
  const node = state.nodes[id];
  const cost = NODE_DEFS[node.type].cost(node);
  if (!confirm(`Run "${node.name}" por $${cost}?`)) return;
  node.status = "queued";
  render();
  window.sendPrompt(`Ejecutar nodo ${id} del Leoda Flow:

  ${JSON.stringify(node, null, 2)}

  Aplica PRE-FLIGHT CHECKLIST. Cuando complete, actualiza este artifact (leoda-flow-editor) marcando el nodo como "done" con thumbnail.`);
}
```

### Cuando Claude recibe ese mensaje:

1. Aplica PRE-FLIGHT CHECKLIST (ver `/video-seedance`):
   - Verifica endpoint correcto
   - Reusa assets si están disponibles
   - Confirma costo
2. Resuelve los inputs del nodo siguiendo las conexiones (resolve upstream)
3. Llama a fal.ai con el endpoint correcto
4. Espera el resultado
5. Descarga el output y crea thumbnail (PIL, max 360x640, calidad 82)
6. Llama `mcp__cowork__update_artifact` con el state actualizado:
   - `state.nodes[id].status = "done"`
   - `state.nodes[id].thumbnail = "data:image/jpeg;base64,..."`
7. Reporta al usuario en chat con preview del output

### Cuando el usuario usa el Flows Agent chat:

```javascript
function sendAgentMsg(text) {
  const context = `Estoy editando un Leoda Flow llamado "${state.flowName}". Estado actual:

  ${JSON.stringify({nodes, connections}, null, 2)}

  Usuario pide: "${text}"

  Responde editando el flow: agrega/quita nodos, modifica prompts, ajusta conexiones según lo que pide. NO ejecutes nada con fal.ai sin que el usuario apruebe explícitamente.`;

  window.sendPrompt(context);
}
```

Claude lee el contexto + petición, edita el state del flow, y actualiza el artifact.

## PRE-FLIGHT CHECKLIST aplicado a Flow Editor

Antes de ejecutar CUALQUIER nodo, Claude completa:

```
[ ] ¿El input del nodo viene de un nodo upstream "done"? → resolver y reusar output
[ ] ¿El usuario ya proveyó un Reference Image? → usar directo, no regenerar
[ ] ¿Endpoint correcto para este tipo de nodo?
    - image-gen → fal-ai/nano-banana
    - image-edit → fal-ai/nano-banana/edit
    - video-gen → bytedance/seedance-2.0/image-to-video (SIN fal-ai/)
    - voice-clone → fal-ai/minimax/voice-clone
    - tts → fal-ai/minimax/speech-02-hd
    - sfx → fal-ai/stable-audio
    - music → fal-ai/elevenlabs/music
[ ] ¿Costo estimado dentro del autorizado por el usuario?
[ ] ¿Status_url extraído DIRECTAMENTE del response inicial?
[ ] ¿Composition node? → NO mandar a fal.ai, usar ffmpeg local
```

## Composition node — siempre local con ffmpeg

El nodo Composition NUNCA llama a fal.ai. Resuelve los inputs videos+audios upstream y compone localmente:

```bash
# Calcular offsets de xfade automáticamente
# Aplicar acrossfade para audio
ffmpeg -y \
  -i clip1.mp4 -i clip2.mp4 -i clip3.mp4 \
  -filter_complex "
    [0:v][1:v]xfade=transition=fade:duration=0.5:offset=4.5[v01];
    [v01][2:v]xfade=transition=fadeblack:duration=0.6:offset=9.0[v];
    [0:a][1:a]acrossfade=d=0.5[a01];
    [a01][2:a]acrossfade=d=0.6[a]
  " \
  -map "[v]" -map "[a]" \
  -c:v libx264 -preset medium -crf 18 \
  -c:a aac -b:a 192k \
  TRAILER_FINAL.mp4
```

Costo: **$0.00 USD**. Solo CPU local.

## Estado persistido

```javascript
state = {
  nodes: {
    "n1": {
      id: "n1",
      type: "video-gen",
      x: 420, y: 80,
      name: "Frame 1 · Cámara sube",  // editable doble-click
      fields: {
        prompt: "Cinematic low-angle dolly UP...",
        duration: 5,
        resolution: "1080p",
        aspect: "9:16",
        audio: true,
      },
      status: "idle",  // idle | queued | running | done | failed
      thumbnail: null,  // data URL después de Run completo
    },
    // ...
  },
  connections: [
    {fromId: "n1", fromPortIdx: 0, toId: "n4", toPortIdx: 0},
    // ...
  ],
  selected: null,
  viewport: {x: 0, y: 0, zoom: 1},
  flowName: "Trailer Pintor · Mona Lisa",
  agentMessages: [],
}
```

## Checklist al entregar un flow editor al usuario

```
[ ] Artifact creado con id "leoda-flow-editor"
[ ] Seed inicial pre-cargado si es trabajo nuevo
[ ] Si el usuario tiene assets en el workspace, pre-cargarlos como nodos Reference
[ ] Cost tracker en $0 si el seed no tiene operaciones costosas
[ ] localStorage funcional (cambios persisten al recargar)
[ ] Botones Save/Load/Reset/Export operativos
[ ] Toolbar dock con 11 tipos de nodos accesible
[ ] Zoom con scroll + botones operativos
[ ] Pan con drag funciona
[ ] Flows Agent chat abre con click
[ ] Right-click muestra context menu
[ ] sendPrompt() configurado para comunicación con Claude
```

## Comparación con ElevenLabs Flows

| Feature | ElevenLabs Flows | Leoda Flow |
|---|---|---|
| Canvas infinito | ✅ | ✅ |
| 50+ modelos | ✅ | 🟡 11 (Nano Banana, Seedance 2.0, Eleven, Stable Audio, MiniMax) |
| Run individual por nodo | ✅ | ✅ |
| Run from here | ✅ | ✅ |
| Cost tracker | ✅ | ✅ (en USD directo, no créditos) |
| Templates | ✅ (workspace/explore) | ✅ (export JSON) |
| Real-time collaboration | ✅ multi-user | ❌ (single user via Cowork) |
| Flows Agent | ✅ chat in-canvas | ✅ chat in-canvas |
| Multi image refs | ✅ | ✅ |
| Composition timeline | ✅ tracks visual | 🟡 simple xfade local con ffmpeg |
| Export a Studio | ✅ ElevenCreative Studio | ❌ (puede exportar mp4 directo) |
| API programática | ⏳ planned | 🟡 a través de Claude conversacionalmente |
| **Pricing** | Pay-per-credit ElevenLabs | **fal.ai directo, sin markup, MIT** |
| **Costo** | $99-$1320/mes plan | **$0 plugin + pay-as-you-go fal.ai** |

## Templates de flows incluidos

### 1. Trailer 3-escenas
```
input-image → video-gen (clip1)

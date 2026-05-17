# Guía de deploy de Leoda.io a GitHub público

Sigue estos pasos exactos para publicar el plugin como producto.

## ✅ Pre-requisitos

- Cuenta de GitHub
- Git instalado en tu Windows
- La carpeta `leoda-io` en tu computadora (la copio del workspace de Cowork)

## Paso 1 — Crear el repo en GitHub

1. Ve a https://github.com/new
2. **Repository name**: `leoda-io`
3. **Description**: `Pipeline automatizado de marketing visual con IA · Plugin para Claude Cowork`
4. **Public** (importante: tiene que ser público para que Cowork lo lea)
5. **NO marques** "Initialize with README" (vamos a subir el nuestro)
6. Click **Create repository**

GitHub te da una URL tipo: `https://github.com/TU-USUARIO/leoda-io.git`

## Paso 2 — Copiar la carpeta a tu computadora

Yo la generé en tu carpeta de trabajo. La estructura completa que tienes que subir es esta:

```
leoda-io/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/                  (10 carpetas con SKILL.md cada una)
├── docs/                    (landing page + demos)
├── promotion/               (textos para redes)
├── .mcp.json
├── README.md
├── DEPLOY-GUIDE.md          (este archivo)
└── leoda-io-v0.2.0.plugin   (el binario)
```

Yo copio todo a `C:\Users\elian\Documents\leoda-io\` automáticamente (siguiente paso te lo hago).

## Paso 3 — Reemplazar `TU-USUARIO` en los archivos

Antes de subir, busca y reemplaza `TU-USUARIO` por tu usuario real de GitHub en estos 3 archivos:

- `README.md`
- `docs/index.html`
- `promotion/twitter-thread.md`
- `promotion/tiktok-instagram-script.md`
- `promotion/reddit-product-hunt.md`

En VS Code o cualquier editor: `Ctrl+Shift+H` → buscar `TU-USUARIO` → reemplazar por tu username de GitHub (ej. `elianmh21`).

## Paso 4 — Push a GitHub

Abre PowerShell o terminal en la carpeta `leoda-io`:

```powershell
cd C:\Users\elian\Documents\leoda-io
git init
git add .
git commit -m "Leoda.io v0.2.0 — initial public release"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/leoda-io.git
git push -u origin main
```

Te va a pedir login a GitHub la primera vez. Si tienes 2FA, usa un Personal Access Token (Settings → Developer settings → Personal access tokens en GitHub).

## Paso 5 — Activar GitHub Pages (landing)

En tu repo en GitHub:
1. Click en **Settings** (arriba a la derecha)
2. Menú izquierdo: **Pages**
3. Source: **Deploy from a branch**
4. Branch: **main** → folder: **/docs**
5. Click **Save**

Espera 1-2 minutos. La landing va a estar en:
```
https://TU-USUARIO.github.io/leoda-io/
```

## Paso 6 — Crear el release (binario .plugin)

En tu repo:
1. Click en **Releases** (lado derecho)
2. **Create a new release**
3. **Choose a tag** → escribe `v0.2.0` → Create new tag
4. **Release title**: `Leoda.io v0.2.0`
5. **Description**: copia el changelog del README
6. **Attach binaries**: arrastra `leoda-io-v0.2.0.plugin` al área de attach
7. Click **Publish release**

Ahora la gente puede descargar el .plugin directamente desde:
```
https://github.com/TU-USUARIO/leoda-io/releases/latest
```

## Paso 7 — Verificar que funciona

Abre Cowork (en tu computadora o pídele a un amigo):

1. **Settings → Plugins → Add Marketplace**
2. Pega: `https://github.com/TU-USUARIO/leoda-io`
3. Debería detectar Leoda.io en la lista
4. Click **Install**

Si funciona, ¡estás listo para promocionar!

## Paso 8 — Promoción (días 1-7)

Usa los textos que ya tienes en `promotion/`:

| Día | Plataforma | Qué postear |
|---|---|---|
| **Día 1** | X / Twitter | Thread + demo-ortiz-10s-twitter.mp4 |
| **Día 2** | TikTok / Instagram Reels | Script #1 (Hook viral) + demo-ortiz-15s.mp4 |
| **Día 3** | Reddit r/ClaudeAI | Post + link a GitHub + 3 demos |
| **Día 4** | LinkedIn | Versión profesional del thread X |
| **Día 5** | Reddit r/StableDiffusion | Post técnico orientado a developers |
| **Día 6** | Discord Anthropic / fal.ai | Compartir en canales showcase |
| **Día 7** | Product Hunt | Lanzamiento oficial (idealmente martes-jueves) |

**Tip**: pre-anuncia 24h antes en X "tomorrow I'm launching Leoda.io on Product Hunt, upvote helps!" para juntar momentum.

## Paso 9 — Monitoreo y respuesta

Durante los primeros 7 días, responde a TODOS los comentarios y issues. La gente que entra temprano son tus power users — cuídalos.

Usa estos canales para feedback:
- Issues en GitHub (problemas técnicos)
- Discussions en GitHub (preguntas generales)
- DMs en X (relaciones públicas)

## Métricas para celebrar

- ⭐ 100 stars en GitHub → momentum sólido
- 📦 50 installs en una semana → producto viable
- 💬 5 testimonios reales de gente que generó campañas → social proof
- 🎥 1 reel viral de alguien usando tu plugin → ❤️ jackpot

---

## Mantenimiento mensual

Una vez al mes:

1. Revisa nuevos modelos en fal.ai (suben modelos nuevos cada mes)
2. Si algún modelo se vuelve obsoleto, actualiza el skill correspondiente
3. Bump versión (0.2.1, 0.3.0, etc.) y crea nuevo release
4. Postea en X el changelog

Suerte 🚀

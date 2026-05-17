# Leoda.io

> Pipeline automatizado de marketing visual con IA generativa premium · Para creadores, agencias y negocios pequeños · Sin diseñador, sin editor, sin estudio.

[![Version](https://img.shields.io/badge/version-0.2.0-blue)](https://github.com/TU-USUARIO/leoda-io)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Cowork Plugin](https://img.shields.io/badge/Cowork-plugin-purple)](https://claude.com/cowork)

**De una idea de producto a un reel cinematográfico listo para subir, en 5 minutos, por $4 USD.**

## 🎬 ¿Qué hace?

Convierte texto en paquetes completos de marketing visual:
- **Imágenes hero** premium en cualquier aspect ratio
- **Videos cinematográficos** hasta 15 segundos en un solo plano (sin cortes)
- **Audio nativo + lip-sync** generado en la misma llamada
- **Voice cloning** desde 10s de muestra de audio
- **Sound effects** (snaps, whooshes, ambient, sparks, explosiones)
- **Captions bilingües** optimizados para IG, TikTok, YouTube, Facebook, LinkedIn, WhatsApp
- **Calendario de publicación** con horarios óptimos

Todo en un solo workflow conversacional dentro de Claude Cowork.

## ⚡ Instalación rápida (3 minutos)

### Paso 1 — Instalar Cowork
Descarga **Claude Cowork** en [claude.com/cowork](https://claude.com/cowork) (gratis).

### Paso 2 — Instalar el plugin
Tres opciones:

**A. Desde este repositorio (1 click)**

En Cowork: `Settings → Plugins → Add Marketplace` → pega:
```
https://github.com/TU-USUARIO/leoda-io
```

**B. Manual con el archivo `.plugin`**

Descarga [`leoda-io-v0.2.0.plugin`](./releases/leoda-io-v0.2.0.plugin) y arrástralo a la ventana de chat de Cowork.

**C. Clonar el repo**
```bash
git clone https://github.com/TU-USUARIO/leoda-io.git
```
Y arrastra la carpeta a Cowork.

### Paso 3 — Configurar tu API key de fal.ai
1. Crea cuenta en [fal.ai](https://fal.ai/) (gratis, agrega $5-10 USD para empezar)
2. En Cowork escribe: `/setup-leoda` — te guía paso a paso para guardar tu key

Listo. Ya puedes generar.

## 🎯 Demos reales

### Reel "Time Manipulator" (Ortiz Remodeling)
Un solo plano continuo de 15 segundos. El contratista camina por el patio en construcción, hace un chasquido y el tiempo se congela. Camina entre las chispas suspendidas, arregla una luz LED descolgada, hace otro chasquido y todo vuelve a la normalidad. Cliente nunca se enteró.

[Ver demo](docs/assets/demo-ortiz-15s.mp4) · Generado en 3 minutos · Costo: **$4.58 USD**

### Logo Reveal cinematográfico (Bencomo Studio)
12 segundos de pared estuco con luz dorada raking, animación de letras emergiendo letra-por-letra. Para nail studio premium.

[Ver demo](docs/assets/demo-bencomo-logo.mp4) · Costo: **$0.40 USD**

### Brand Film 10 escenas
Editorial de marca para nail boutique: pared, velvet, brass, orquídea, mármol, herramientas, perlas, esmalte, cortina, hero shot — todo a hora dorada con crossfades cinematográficos.

[Ver demo](docs/assets/demo-bencomo-brand.mp4) · Costo: **$4.40 USD**

## 🛠 Los 10 skills incluidos

| Comando | Para qué sirve |
|---|---|
| `/setup-leoda` | Configuración inicial: obtener y guardar tu API key de fal.ai |
| `/brief-creativo` | Brief de campaña bilingüe ES+EN (concepto, hooks, CTAs) |
| `/imagen-nano-banana` | Imágenes con Nano Banana (text-to-image + edit con referencias) |
| `/video-seedance` | Video cinematográfico con Seedance 2.0 (audio nativo + lip-sync) |
| `/voz-clon` | Clonar voz desde audio + generar narración HD |
| `/sfx-generar` | Efectos de sonido (snaps, whooshes, ambient, sparks) |
| `/variantes-ab` | Variantes A/B y reformateo automático a todos los aspect ratios |
| `/captions-multiplataforma` | Captions ES+EN optimizados por plataforma |
| `/calendario-publicaciones` | Calendario semanal con horarios óptimos |
| `/pipeline-completo` | Ejecuta todo de extremo a extremo en una sola invocación |

## 💰 Costos típicos (por uso real en fal.ai, sin suscripciones)

| Operación | USD |
|---|---|
| 1 imagen Nano Banana | $0.04 |
| 1 video Seedance 2.0 Fast 5s 720p (con audio) | $1.21 |
| 1 video Seedance 2.0 Standard 8s 1080p (con audio + lip-sync) | $2.42 |
| 1 OneShot Seedance 2.0 15s 1080p (premium) | $4.54 |
| Voice clone (una sola vez por persona) | $0.30 |
| Narración TTS por frase | $0.05-0.10 |
| 1 SFX cinematográfico | $0.05-0.08 |
| **Campaña típica completa** | **$6-8** |

fal.ai cobra solo por lo que uses. Sin suscripciones.

## 🎓 Casos de uso

### Para nail studios / salones
Logo reveals premium · transformación antes/después · UGC con voice clone del dueño · brand films editoriales

### Para construcción / remodelación
Pergola installations · before/after transformaciones · time-stop "Quicksilver" style · cliente disfrutando el resultado

### Para e-commerce / productos
Hero shots cinematográficos · mockups premium · variantes A/B para Meta Ads · stop motion editorial

### Para inmobiliarias
Recorridos de propiedades · drone shots simulados · staging virtual · sunset hero shots

### Para servicios profesionales (abogados, contadores, médicos)
Voice cloning del profesional + narración con autoridad · explainers visuales · branded intros

## 🌍 Idiomas

Por defecto **bilingüe ES + EN**. Voice cloning soporta español, inglés, portugués, francés, italiano, chino, japonés y más vía MiniMax Speech-02 HD.

## 🤝 Contribuir

Issues y PRs bienvenidos. Si encuentras un endpoint nuevo de fal.ai que vale la pena integrar, abre un issue.

## 📜 Licencia

MIT — usa, modifica y distribuye libremente.

## 🙋 Autor

**Elian** · elianmh21@gmail.com

---

⭐ Si te sirve este plugin, dale star al repo. Ayuda a que má
---
name: setup-leoda
description: >
  Configurar Leoda.io por primera vez. Use cuando el usuario diga "setup leoda",
  "configurar leoda", "instalar leoda", "conectar fal.ai", "necesito una API key",
  "/setup-leoda" o cuando intente usar otra skill del plugin sin haber configurado FAL_KEY.
metadata:
  version: "0.1.0"
---

# Setup Leoda.io

Guide the user through obtaining a fal.ai API key and configuring the `FAL_KEY` environment variable on their machine. This skill should run before any other skill in this plugin needs to call the fal.ai API.

## Pre-flight check

Before doing anything, check whether `FAL_KEY` is already set on the user's machine:

```bash
echo "${FAL_KEY:+SET}${FAL_KEY:-MISSING}"
```

If the output is `SET`, tell the user "Ya tienes `FAL_KEY` configurada / `FAL_KEY` is already set" and offer to run a quick test call (see "Smoke test" below). Skip the rest of this skill unless the user explicitly wants to reconfigure.

If the output is `MISSING`, proceed with the guided setup below.

## Guided setup (bilingual, ES default)

Always present steps in Spanish first, then English in parentheses on the same line. The user opted for bilingual output for this plugin.

### Step 1 — Crear cuenta en fal.ai / Create a fal.ai account

Tell the user:

1. Abre / Open: `https://fal.ai/`
2. Click en "Sign up" (recomendado: continuar con Google o GitHub)
3. Confirma el email si te lo pide

Wait for the user to confirm "listo" / "done" before continuing.

### Step 2 — Agregar saldo / Add credits

fal.ai charges per request. Nano Banana runs about ~$0.039 per image, Seedance about ~$0.18 per 5-second 720p video clip (precios aproximados, pueden variar). Recommend a starter top-up of $5–$10 USD:

1. Ir a / Go to: `https://fal.ai/dashboard/billing`
2. Agregar método de pago / Add a payment method
3. Cargar saldo inicial / Add starter credits ($5 USD recomendado)

### Step 3 — Generar la API key / Generate the API key

1. Ir a / Go to: `https://fal.ai/dashboard/keys`
2. Click "Add key" → ponle un nombre como `leoda-io`
3. **Copia la key COMPLETA inmediatamente** (no se vuelve a mostrar) / Copy the full key now — it won't be shown again

The key looks like: `key_id:key_secret` (a long string with a colon).

### Step 4 — Guardar la key como variable de entorno / Save the key as an environment variable

Detect the user's operating system and provide the right command. Use the Bash tool to detect:

```bash
uname -s 2>/dev/null || echo "Windows"
```

**On macOS / Linux (zsh, the default on modern macOS):**

```bash
echo 'export FAL_KEY="PEGA_TU_KEY_AQUI"' >> ~/.zshrc
source ~/.zshrc
```

For bash users, swap `~/.zshrc` for `~/.bashrc`.

**On Windows (PowerShell, permanente para el usuario):**

```powershell
[Environment]::SetEnvironmentVariable("FAL_KEY", "PEGA_TU_KEY_AQUI", "User")
```

After running this, the user must **restart Claude / Cowork** so the new env var is picked up.

**On Windows (cmd.exe, sesión actual + permanente):**

```cmd
setx FAL_KEY "PEGA_TU_KEY_AQUI"
```

Tell the user to replace `PEGA_TU_KEY_AQUI` with the key they copied in Step 3, then restart Cowork.

### Step 5 — Smoke test

After the user confirms restart, verify the key works with a minimal Nano Banana call. Use the Bash tool:

```bash
curl -s -X POST "https://fal.run/fal-ai/nano-banana" \
  -H "Authorization: Key ${FAL_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"a simple red apple on white background, photographic","num_images":1}' \
  | head -c 400
```

If the response includes `"images"` and an `"url"`, setup is complete. Tell the user:

> ✅ Leoda.io está listo. Ya puedes usar /brief-creativo, /imagen-nano-banana, /video-seedance, /variantes-ab, /captions-multiplataforma, /calendario-publicaciones, o /pipeline-completo.

If the response includes `"error"` or HTTP 401/403, the key is wrong or missing — walk the user back through Step 3 and Step 4.

## Troubleshooting

- **"FAL_KEY: command not found"** → the env var wasn't exported. Re-run Step 4 and restart Cowork.
- **"Insufficient balance"** → top up at `https://fal.ai/dashboard/billing`.
- **"Unauthorized"** → the key is wrong or revoked. Generate a fresh one at `https://fal.ai/dashboard/keys`.
- **Key has `:` and bash complains** → wrap it in double quotes: `export FAL_KEY="abc:def"`.

## Security notes

- Never commit the key to git or paste it into a public chat.
- The key gives full access to spend on the user's fal.ai account.
- If the key leaks, revoke it immediately at `https://fal.ai/dashboard/keys` and generate a new one.

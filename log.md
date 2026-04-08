# Log de sesión — 2026-04-08

## Objetivo
Centralizar las apps de NyPE (UTDT) en una carpeta local, crear una landing page y unificar el estilo visual.

---

## 1. Clonado de repositorios

Se clonaron 7 repos de `github.com/gsolovey-utdt` en subcarpetas de `nype-apps/`:

```
stroop, umbral-de-deteccion, correlacion, asignacion_aleatoria,
muestreo_aleatorio, azar, set
```

`two-armed-bandit` ya estaba presente localmente. Se descartaron: `futbol-mundial`, `seminario`, `correlacion_actividad`, `potencialdeaccion`.

---

## 2. Landing page

Se creó `nype-apps/index.html` con:
- Tema claro profesional (fondo `#f4f5f9`, cards blancas)
- Fuentes DM Sans / DM Mono / Playfair Display
- Badge NyPE dorado
- Grilla de 8 cards con links a las URLs públicas de GitHub Pages

Links apuntan a `https://gsolovey-utdt.github.io/<repo>/`.

---

## 3. Unificación de estilo visual

### Sistema de diseño compartido
- **Fuentes:** DM Sans (cuerpo), DM Mono (labels/mono), Playfair Display (títulos)
- **Badge NyPE:** elemento de identidad en todas las apps
- **Paleta:** tema claro con acento dorado (`#e8c547`)

### Cambios por app

| App | Archivos modificados | Cambios |
|-----|---------------------|---------|
| `two-armed-bandit` | `styles.css` | Variables CSS → tema claro; badge: `color: #0d0e12` hardcodeado |
| `stroop` | `index.html` | Variables CSS → tema claro; corregidos `color: #fff` en h1, `.transition-title`, `.stat-value`; fondos `rgba(255,255,255,.04)` → `var(--surface-2)` |
| `azar` | `index.html` | Ídem stroop; agregada variable `--surface-2` |
| `umbral-de-deteccion` | `styles.css`, `index.html` | `styles.css` reescrito al tema claro; badge actualizado; título corregido |
| `correlacion` | `styles.css`, `index.html` | `styles.css` reescrito al tema claro; badge NyPE agregado; chart-panel en columna (1 gráfico) |
| `asignacion_aleatoria` | `styles.css`, `index.html` | Ídem correlacion; chart-panel en fila (2 gráficos lado a lado) |
| `muestreo_aleatorio` | `styles.css`, `index.html` | Ídem asignacion; dos histogramas en fila |
| `set` | `styles.css`, `index.html` | `styles.css` reescrito al tema claro; badge NyPE agregado |

---

## 4. Git y deploy

### Autenticación
- Se generaron dos tokens de acceso clásico (`repo` scope) para `gsolovey-utdt`
- El segundo token (`ghp_1WHrsK3...`) quedó configurado en el remote de cada repo con el formato `https://gsolovey-utdt:TOKEN@github.com/...`
- Se configuró `credential.helper store` y `GIT_TERMINAL_PROMPT=0` para pushes no interactivos

### Commits pusheados

| Repo | Commits |
|------|---------|
| `correlacion` | Tema oscuro inicial → tema claro |
| `asignacion_aleatoria` | Ídem |
| `muestreo_aleatorio` | Ídem |
| `umbral-de-deteccion` | Ídem |
| `set` | Ídem |
| `stroop` | Tema claro + corrección de textos blancos |
| `azar` | Ídem |
| `two-armed-bandit` | Tema claro (merge con commits remotos) |
| `nype-apps` | Landing page + CLAUDE.md + log |

### Nuevo repo creado
Se creó `gsolovey-utdt/nype-apps` en GitHub para hospedar la landing page. GitHub Pages disponible en `https://gsolovey-utdt.github.io/nype-apps/`.

---

## 5. Verificaciones y correcciones

- **Supabase:** Se verificó que `umbral-de-deteccion`, `stroop`, `azar` y `two-armed-bandit` usan el mismo proyecto Supabase (`irryksaoygdklwtsjsru`). No había error — estaba bien.
- **Keep-alive:** El workflow `stroop/.github/workflows/keep-alive.yml` hace ping a Supabase lunes y jueves. Cubre todas las apps (mismo proyecto). No se replicó a otros repos.
- **Textos blancos:** Corregidos en `stroop` y `azar` al pasar al tema claro.
- **Credential manager:** Se deshabilitó el Windows Credential Manager por repo para evitar popups de autenticación en el browser.

---

## 6. Pendiente / Para próxima sesión

- Borrar la subcarpeta vacía `nype-apps/nype-apps/` (bloqueada por Claude Code durante la sesión — hacerlo después de cerrar Claude Code)
- Revisar el estilo visual: posible decisión de volver al tema oscuro para apps de experimento y mantener claro solo para visualizaciones
- El token de GitHub expira — renovarlo en `gsolovey-utdt` → Settings → Developer settings → Tokens (classic) y actualizar remotes (ver instrucciones en CLAUDE.md)

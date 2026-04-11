# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Apps interactivas para la materia **Neurociencia y Psicología Experimental (NyPE)** de la UTDT. Autor: Guillermo Solovey.

La carpeta `nype-apps/` contiene la **landing page** y 8 subcarpetas, cada una un repositorio git independiente.

## Estructura

```
nype-apps/
├── index.html               ← Landing page (repo: gsolovey-utdt/nype-apps)
├── CLAUDE.md
├── log-DD-MM-YYYY.md        ← Un log por sesión (ver convención abajo)
├── _templates/              ← Templates HTML base para nuevas apps
│   ├── experimento.html
│   └── visualizacion.html
├── two-armed-bandit/        ← repo independiente
├── stroop/
├── azar/
├── umbral-de-deteccion/
├── correlacion/
├── asignacion_aleatoria/
├── muestreo_aleatorio/
├── set/
└── memoria-de-digitos/      ← repo independiente, tiene su propio CLAUDE.md
```

Cada subcarpeta es un repo git clonado de `github.com/gsolovey-utdt/<repo>`. **No hay un repo raíz que las contenga a todas** — solo la landing page tiene su propio repo (`nype-apps`).

## Logs de sesión

Cada sesión (thread) de Claude Code genera un archivo `log-DD-MM-YYYY.md` en la raíz. Si hay más de una sesión en el mismo día, se agrega la hora: `log-DD-MM-YYYY-HHhMM.md`.

El log documenta qué se hizo, qué se decidió y qué queda pendiente.

## Deploy

Todas las apps están publicadas en **GitHub Pages** bajo `gsolovey-utdt`:

- Landing: `https://gsolovey-utdt.github.io/nype-apps/`
- Cada app: `https://gsolovey-utdt.github.io/<nombre-repo>/`

GitHub Pages deploya automáticamente al pushear a `main`. No hay build step — son HTML/CSS/JS estáticos.

## Git y autenticación

Cada repo tiene configurado el remote con token en la URL (`git config credential.helper store`). Para pushear desde Claude Code usar siempre `GIT_TERMINAL_PROMPT=0`.

Si el token expira, generar uno nuevo en GitHub (`gsolovey-utdt` → Settings → Developer settings → Personal access tokens → Tokens classic → scope: `repo`) y actualizar el remote de cada repo:

```bash
TOKEN="ghp_..."
for repo in two-armed-bandit stroop azar umbral-de-deteccion correlacion asignacion_aleatoria muestreo_aleatorio set; do
  cd /c/Users/guillermo.solovey/Dropbox/work/2026/nype-apps/$repo
  git remote set-url origin "https://gsolovey-utdt:${TOKEN}@github.com/gsolovey-utdt/${repo}.git"
done
cd /c/Users/guillermo.solovey/Dropbox/work/2026/nype-apps
git remote set-url origin "https://gsolovey-utdt:${TOKEN}@github.com/gsolovey-utdt/nype-apps.git"
```

## Backend (Supabase)

Las apps con resultados colectivos y leaderboard usan Supabase. **Todas comparten el mismo proyecto**: `irryksaoygdklwtsjsru.supabase.co`.

El repo `stroop` tiene un workflow `.github/workflows/keep-alive.yml` que hace ping a Supabase dos veces por semana (lunes y jueves) para que el proyecto free no se pause. Cubre a todas las apps — no replicar a otros repos.

## Sistema de diseño

Fuentes (Google Fonts): **DM Sans** (cuerpo), **DM Mono** (monoespaciado/labels), **Playfair Display** (títulos).

El badge NyPE es el elemento de identidad compartido:
```html
<div class="nype-header">
  <span class="nype-badge">NyPE</span>
  <span class="nype-sub">Neurociencia y Psicología Experimental</span>
</div>
```

Variables CSS base (tema claro actual):
```css
--bg:        #f4f5f9;
--surface:   #ffffff;
--surface-2: #f0f1f7;
--border:    #dde0ee;
--text:      #1a1d2e;
--muted:     #6b7280;
--gold:      #b8860b;   /* texto sobre fondo claro */
--gold-bg:   #e8c547;   /* fondo del badge y botones primarios */
--shadow:    0 2px 16px rgba(26,29,46,0.08);
--radius:    12px;
```

**Importante:** El badge usa `color: #0d0e12` (hardcodeado, no `var(--bg)`) para mantener contraste independientemente del tema.

## Tipos de app

| Tipo | Apps | Características |
|------|------|----------------|
| Experimento | two-armed-bandit, stroop, azar, umbral-de-deteccion, memoria-de-digitos | Pantalla única, flujo secuencial, datos a Supabase |
| Visualización | correlacion, asignacion_aleatoria, muestreo_aleatorio | Layout dos columnas (controles + gráfico), Chart.js |
| Juego | set | Sin backend, lógica en JS puro |

## Nueva app — checklist

Cuando Guillermo diga "quiero crear una nueva app llamada `<nombre>`", seguir estos pasos:

### 1. Contexto mínimo necesario (pedirlo si falta)
- Nombre del repo (slug, ej: `memoria-de-trabajo`)
- Tipo: experimento / visualización / juego
- Idea en 2–3 líneas: qué hace el usuario, qué aprende

### 2. Crear la carpeta y el repo git local
```bash
cd /c/Users/guillermo.solovey/Dropbox/work/2026/nype-apps
mkdir <nombre>
cd <nombre>
git init
git checkout -b main
```

### 3. Crear el HTML base desde el template
Copiar el template correspondiente de `_templates/`:
- `_templates/experimento.html` → para flujo secuencial con datos a Supabase
- `_templates/visualizacion.html` → para layout dos columnas con Chart.js
- `_templates/juego.html` → para lógica JS pura sin backend

Renombrarlo a `index.html` y adaptar título, descripción y lógica.

### 4. Crear repo en GitHub y conectar remote
```bash
# Crear repo vacío en GitHub (via gh CLI o manualmente en github.com)
gh repo create gsolovey-utdt/<nombre> --public --source=. --remote=origin

# O si se crea manualmente, setear el remote con token:
TOKEN="ghp_..."
git remote add origin "https://gsolovey-utdt:${TOKEN}@github.com/gsolovey-utdt/<nombre>.git"
```

### 5. Primer commit y push
```bash
git add index.html
git commit -m "Initial commit"
GIT_TERMINAL_PROMPT=0 git push -u origin main
```

### 6. Activar GitHub Pages
En `github.com/gsolovey-utdt/<nombre>` → Settings → Pages → Branch: main / (root) → Save.
La app quedará en `https://gsolovey-utdt.github.io/<nombre>/`.

### 7. Actualizar la landing page
En `nype-apps/index.html` agregar la nueva card con link a la URL de GitHub Pages.
Luego commitear y pushear el repo `nype-apps`.

---

## CLAUDE.md por app

Las apps más desarrolladas pueden tener su propio `CLAUDE.md` dentro de su carpeta. Claude Code lo lee **además** del CLAUDE.md raíz al trabajar desde esa subcarpeta — no lo reemplaza. Hoy solo `memoria-de-digitos` tiene el suyo.

## Decisiones tomadas

| Fecha | Decisión | Razionale |
|-------|----------|-----------|
| 2026-04 | Tema claro para las apps de visualización y juego | Chart.js se ve mejor sobre fondo blanco |
| 2026-04 | Tema oscuro para las apps de experimento y la landing | Más inmersivo para tareas cognitivas |
| 2026-04 | Supabase compartido para todas las apps de experimento | Un solo proyecto free, keep-alive centralizado en stroop |
| 2026-04 | Links en landing apuntan a URLs públicas (GitHub Pages) | Las apps se sirven de repos independientes, no hay servidor común |
| 2026-04 | Cada subcarpeta es un repo git independiente | Permite deployar cada app por separado en GitHub Pages |
| 2026-04-09 | Logs por sesión con formato `log-DD-MM-YYYY.md` | Un archivo por thread para tener historial claro |
| 2026-04-09 | Templates base en `_templates/` | Punto de partida para nuevas apps sin reescribir el sistema de diseño |
| 2026-04-11 | CLAUDE.md por app para las apps más complejas | El CLAUDE.md raíz contiene convenciones globales; el de cada app, decisiones específicas. Claude lee ambos en cascada. |

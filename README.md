# OpenClaw Agents — Instagram Content con Claude Code

Dos agentes de IA para crear carousels e historias de Instagram de alta conversión, usando **Claude Code** como motor de razonamiento y **Gemini** para generación de imágenes.

---

## ¿Qué incluye?

| Agente | Carpeta | Para qué |
|--------|---------|----------|
| 🎠 **Carru** | `ig-carruseles/` | Crea carousels de Instagram (7-11 slides) |
| 📖 **History** | `ig-historias/` | Crea y programa historias de Instagram |

---

## Requisitos

- **Claude Code** instalado: `npm install -g @anthropic-ai/claude-code`
- **Node.js** 18+ 
- **Google AI Studio API Key** (gratis en [aistudio.google.com](https://aistudio.google.com))
- **Instagram Graph API token** (necesita cuenta de Facebook Business)

---

## Instalación

### 1. Clonar el repo

```bash
git clone https://github.com/highvalue-llc/openclaw-agents-v2.git
cd openclaw-agents-v2
```

### 2. Instalar dependencias en cada agente

```bash
# Agente de carousels
cd ig-carruseles
npm init -y
npm install @google/genai sharp

# Agente de historias
cd ../ig-historias
npm init -y
npm install @google/genai sharp
```

### 3. Configurar variables de entorno

Crear un archivo `.env` en cada carpeta de agente:

```env
# ig-carruseles/.env o ig-historias/.env

GOOGLE_API_KEY=tu_clave_de_google_ai_studio
IG_ACCESS_TOKEN=tu_token_de_instagram
IG_ID=tu_instagram_user_id
```

**Cómo obtener cada una:**

| Variable | Cómo obtenerla |
|----------|---------------|
| `GOOGLE_API_KEY` | [aistudio.google.com](https://aistudio.google.com) → Get API Key |
| `IG_ACCESS_TOKEN` | [developers.facebook.com](https://developers.facebook.com) → Graph API Explorer → generar token con permisos `instagram_content_publish` |
| `IG_ID` | Llamar a `GET /me?fields=id` con tu token en el Graph API Explorer |

### 4. Agregar foto de referencia (para Carru con personas)

Si vas a generar slides con tu cara:

```bash
# Copiar una foto clara de frente a la carpeta history-inbox/
cp tu-foto.jpg ig-carruseles/history-inbox/creator-ref.jpg
```

### 5. Crear carpeta de output

```bash
mkdir -p ig-carruseles/carousel-images
mkdir -p ig-historias/story-images ig-historias/history-inbox
```

---

## Cómo usar

### Agente Carru (Carousels)

```bash
cd ig-carruseles
claude
```

Claude Code leerá el `CLAUDE.md` automáticamente y sabrá que es el agente Carru.

**Qué podés pedirle:**
- `"Hacé un carousel sobre [TEMA] tipo H"` — herramienta/repo
- `"Carousel tipo D sobre esta noticia: [URL]"` — urgencia/FOMO
- `"Carousel tipo E sobre [STARTUP] que levantó [MONTO]"` — caso de estudio
- `"Mostrá los tipos disponibles"` — ver todos los estilos

### Agente History (Stories)

```bash
cd ig-historias
claude
```

**Qué podés pedirle:**
- `"Creá 5 ideas de stories para hoy"`
- `"Hacé una story tipo 1 con esta foto: [ruta/foto.jpg]"`
- `"Story tipo tweet sobre [TEMA]"`
- `"Programá la secuencia para las 9am"`

---

## Cómo funciona internamente

```
Usuario → Claude Code → lee CLAUDE.md → hace briefing
                                       → genera prompts para Gemini
                                       → Gemini genera imágenes
                                       → Claude Code analiza y revisa
                                       → muestra al usuario
                                       → publica en Instagram Graph API
```

Las imágenes se generan localmente en `carousel-images/` o `story-images/`.
Para publicarlas en Instagram necesitás hostearlas en una URL pública accesible (GitHub + jsDelivr CDN es la opción más simple y gratis).

**CDN con GitHub + jsDelivr (gratis):**
```bash
# 1. Crear repo público en GitHub
gh repo create tu-usuario/mis-carousels --public

# 2. Subir las imágenes
git add carousel-images/
git commit -m "carousel nuevo"
git push

# 3. URL pública de cada imagen:
# https://cdn.jsdelivr.net/gh/tu-usuario/mis-carousels@main/carousel-images/s1-cover.jpg
```

---

## Estructura de archivos

```
ig-carruseles/
├── CLAUDE.md           ← instrucciones del agente (Claude Code las lee automáticamente)
├── SOUL.md             ← personalidad y estilo del agente
├── IDENTITY.md         ← identidad del agente
├── OPTIMIZACION.md     ← guía de optimización de prompts (tiene prioridad)
├── history-inbox/      ← fotos de referencia del creador
│   └── creator-ref.jpg ← foto de referencia principal
└── carousel-images/    ← imágenes generadas (se crea automáticamente)

ig-historias/
├── CLAUDE.md           ← instrucciones del agente
├── SOUL.md
├── IDENTITY.md
├── ref-images/         ← referencias de estilos de stories
│   ├── ref-levantador-1slide.jpg
│   ├── ref-texto-puro-lista.jpg
│   └── ...
├── history-inbox/      ← fotos nuevas para usar en stories
└── story-images/       ← imágenes generadas
```

---

## Personalizar para tu marca

### 1. Cambiar colores y tipografía

En `CLAUDE.md` de Carru, buscá la sección **Brand rules** y modificá:

```
Gold: #e4c69a  →  tu color principal
Fondo: #000000  →  tu color de fondo
```

### 2. Cambiar el copy framework

Podés reemplazar los frameworks de Hormozi/Suby por el tuyo propio en la sección **Copy frameworks**.

### 3. Cambiar tipos de contenido

Editá la sección **Tipos de contenido** para agregar o quitar tipos según tu marca.

---

## Preguntas frecuentes

**¿Necesito pagar por Gemini?**
Google AI Studio tiene un tier gratuito generoso. Para uso intensivo necesitás pagar.

**¿Necesito una cuenta de Facebook Business?**
Sí, para publicar via Instagram Graph API necesitás una cuenta de Instagram Professional conectada a una página de Facebook.

**¿Funciona en Windows, Mac y Linux?**
Sí, Claude Code y Node.js son cross-platform.

**¿Las imágenes se publican automáticamente?**
No por defecto — el agente genera las imágenes, las muestra para aprobación y recién ahí publica. Esto es intencional para que tengas control total.

---

## Créditos

Desarrollado por [High Value Digital](https://highvalue.digital).
Powered by [Claude Code](https://claude.ai/code) + [Gemini](https://aistudio.google.com).

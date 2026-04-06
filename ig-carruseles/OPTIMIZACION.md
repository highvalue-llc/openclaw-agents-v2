# OPTIMIZACIÓN — Ahorro de tokens en carruseles

**ESTE DOCUMENTO TIENE PRIORIDAD sobre cualquier instrucción anterior que contradiga lo que dice acá.**

El sistema anterior (leer archivos de estilo completos) gastaba ~100K tokens por carrusel. Este nuevo sistema reduce eso a ~35K tokens manteniendo la misma calidad.

---

## REGLA #1 — Carga modular de estilos

**NUNCA leer archivos de estilo innecesarios.** Solo cargar lo mínimo necesario en cada momento.

### Arranque de sesión (Carru)

Leer SOLO estos archivos:
1. `SOUL.md` e `IDENTITY.md` — si tienen [PENDIENTE], hacer configuración inicial primero
2. `./styles/style-base.json` (~1KB) — marca, colores, tipografía, formato

**NO leer nada más hasta que el usuario diga qué tipo de carrusel quiere.**

### Al recibir el briefing

Cuando el usuario dice "haceme un Tipo H sobre [tema]":
1. Leer `./styles/tipo-H.json` — SOLO ese tipo, no los demás
2. Leer `./styles/feedback-tipo-H.md` — feedback previo de ESE tipo
3. No leer ningún otro tipo-X.json

### Si necesita referencia de un carrusel anterior

SOLO en ese caso leer `./carousel-archive.json`.
No cargarlo por defecto.

---

## REGLA #2 — Style DNA una sola vez

El style DNA se lee UNA VEZ al inicio del carrusel y se mantiene en contexto. No volver a leer los archivos de estilo entre slides.

**Para prompts de Gemini:**
- **Slide 1:** incluir el style DNA completo del tipo en el prompt
- **Slides 2-7:** decir "Mismo estilo exacto que la slide 1. Misma tipografía, mismos colores, mismo fondo." + incluir 1-2 slides ya generadas como referencia visual
- **NO repetir** el bloque completo de style DNA en cada prompt

---

## REGLA #3 — Workflow de 3 pasos (no 10+ round-trips)

### Paso 1 — Briefing + Copy completo (1 interacción)
- El usuario dice qué tipo y tema
- Carru genera el copy de TODAS las slides de una sola vez
- Muestra todo junto para aprobación del copy

### Paso 2 — Generación de imágenes (1 bloque)
- Una vez aprobado el copy, generar TODAS las slides
- Generar en secuencia pero sin volver a consultar archivos de estilo
- Entregar el paquete completo al agente principal

### Paso 3 — Revisión por el agente principal + Feedback
- El agente principal revisa (ver sección abajo)
- Si hay correcciones → Carru corrige solo las slides indicadas
- Si todo OK → mostrar al usuario para aprobación final

**Objetivo: 3-5 interacciones en vez de 10+**

---

## REGLA #4 — Revisión por el agente principal

**Cuando Carru entrega las imágenes, el agente principal las examina 1 POR 1 con la herramienta de visión.**

### Checklist por imagen:

#### 1. Ortografía castellana (CRÍTICO)
- [ ] Acentos correctos: á é í ó ú (no a e i o u cuando corresponde acento)
- [ ] Ñ donde corresponde (no "n" sola)
- [ ] ¿Signos de pregunta? → SIEMPRE ¿ al inicio Y ? al final
- [ ] ¡Signos de exclamación! → SIEMPRE ¡ al inicio Y ! al final
- [ ] Sin errores de tipeo o letras cortadas

#### 2. Brand (según configuración en SOUL.md y style-base.json)
- [ ] Fondo oscuro (el color configurado en style-base.json)
- [ ] Color de acento exacto (el hexcode configurado, no variaciones)
- [ ] Tipografía consistente entre slides (mismo font, mismo weight)
- [ ] Handle de Instagram presente donde corresponde

#### 3. Tipo-específico
- [ ] Italic SOLO si el tipo configurado lo permite. Si no → NO italic
- [ ] Emoji SOLO si el tipo configurado lo permite
- [ ] Elementos obligatorios del tipo presentes (según tipo-X.json)

#### 4. Composición
- [ ] Texto completamente legible (no cortado por bordes)
- [ ] Jerarquía visual correcta (headline > subheadline > body)
- [ ] Espaciado consistente entre slides
- [ ] Imágenes/screenshots bien integradas (no pixeladas, no cortadas)

#### 5. Coherencia
- [ ] Todas las slides mantienen el mismo estilo visual
- [ ] Transición natural entre slides (no parece que cada una es de un carrusel diferente)
- [ ] CTA keyword consistente con el tema

### Si algo falla:
→ Devolver a Carru con indicación PRECISA: "Slide 3: falta ¿ al inicio de la pregunta. Slide 5: el color de acento parece más claro que el configurado."
→ Carru corrige SOLO las slides indicadas, no regenera todo.

### Si todo OK:
→ Mostrar al usuario para aprobación final.

---

## REGLA #5 — Feedback por tipo

**El feedback del usuario se guarda POR TIPO, no global.**

### Cuando el usuario da feedback:

1. Identificar el tipo del carrusel actual (ej: Tipo H)
2. Abrir `./styles/feedback-tipo-H.md`
3. Agregar una entrada con este formato:

```
## YYYY-MM-DD — [tema del carrusel]
**Feedback:** [lo que dijo el usuario]
**Resolución:** [cómo se corrigió]
**Regla aprendida:** [qué hacer diferente en el futuro para este tipo]
```

4. Guardar el archivo

### Al crear un nuevo carrusel:

Carru lee `feedback-tipo-X.md` del tipo correspondiente ANTES de empezar a generar. Así no repite errores previos de ese tipo específico.

**El feedback de Tipo E NO afecta Tipo H.** Cada tipo mejora independientemente.

---

## REGLA #6 — Imágenes de referencia eficientes

### Para Gemini:
- **NO enviar 3 imágenes de referencia en CADA prompt de slide**
- **Slide 1:** enviar la foto de referencia del creador + 1 slide aprobada anterior del mismo tipo como referencia
- **Slides 2+:** enviar SOLO la slide 1 recién generada como referencia de estilo
- **Nunca enviar más de 2 imágenes de referencia por prompt**

### Carpeta de referencias (`./ref-images/`):
Cuando el usuario sube una foto de referencia a Claude Code, guardarla en `./ref-images/`.

Archivos de referencia estándar:
```
./ref-images/
├── creator-ref.jpg          ← foto principal del creador (para generar su imagen)
├── creator-ref-2.jpg        ← foto alternativa si se necesita
├── logo.png                 ← logo de la marca
└── tipo-E-ref.jpg           ← referencia visual de un carrusel Tipo E aprobado
    tipo-H-ref.jpg           ← etc.
```

### Para fotos de CEOs (Tipo E):
- Descargar la foto REAL una sola vez al inicio (buscar en unavatar.io o web_search)
- Enviarla SOLO en el prompt de la slide 1 (portada)
- Para slides 2-7 no enviarla — ya no aparece el CEO

---

## Estructura de archivos del agente

```
ig-carruseles/
├── CLAUDE.md                ← instrucciones principales (Claude Code las lee automáticamente)
├── SOUL.md                  ← configuración de marca (completar en primera sesión)
├── IDENTITY.md              ← identidad del agente (completar en primera sesión)
├── OPTIMIZACION.md          ← este archivo (tiene prioridad)
├── ref-images/              ← fotos de referencia del creador y la marca
│   └── creator-ref.jpg
├── styles/                  ← archivos de estilo (crear al configurar la marca)
│   ├── style-base.json      ← base de marca (colores, tipografía, formato)
│   ├── tipo-A.json          ← style DNA del Tipo A
│   ├── tipo-D.json
│   ├── tipo-E.json
│   ├── tipo-F.json
│   ├── tipo-G.json
│   ├── tipo-H.json
│   ├── tipo-I.json
│   ├── feedback-tipo-A.md   ← feedback acumulado por tipo
│   ├── feedback-tipo-D.md
│   └── ...
└── carousel-images/         ← imágenes generadas (output)
```

### Crear style-base.json (al configurar la marca)

```json
{
  "brand": {
    "name": "Nombre de la marca",
    "handle": "@instagram_handle",
    "footer_text": "NOMBRE MARCA"
  },
  "colors": {
    "accent": "#e4c69a",
    "background": "#0a0a0a",
    "text_primary": "#ffffff",
    "text_secondary": "#888888"
  },
  "typography": {
    "headline_font": "Poppins ExtraBold",
    "body_font": "Poppins Regular",
    "italic_allowed": false
  },
  "format": {
    "dimensions": "1080x1350",
    "ratio": "4:5"
  }
}
```

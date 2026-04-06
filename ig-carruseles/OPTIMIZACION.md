# OPTIMIZACIÓN — Ahorro de tokens en carruseles

**ESTE DOCUMENTO TIENE PRIORIDAD sobre cualquier instrucción anterior que contradiga lo que dice acá.**

El sistema anterior (leer `content-style.json` completo de 23KB) gastaba ~100K tokens por carrusel. Este nuevo sistema reduce eso a ~35K tokens manteniendo la misma calidad.

---

## REGLA #1 — Carga modular (NO leer content-style.json)

**NUNCA leer `content-style.json`.** Ese archivo queda como backup histórico.

### Arranque de sesión (Carru)

Leer SOLO estos 3 archivos:
1. `SOUL.md` e `IDENTITY.md` (como siempre)
2. `~/.openclaw/workspace/styles/style-base.json` (~1KB) — marca, colores, tipografía, formato

**NO leer nada más hasta que Nico diga qué tipo de carrusel quiere.**

### Al recibir el briefing

Cuando Nico dice "haceme un Tipo H sobre [tema]":
1. Leer `~/.openclaw/workspace/styles/tipo-H.json` — SOLO ese tipo, no los demás
2. Leer `~/.openclaw/workspace/styles/feedback-tipo-H.md` — feedback previo de ESE tipo
3. No leer ningún otro tipo-X.json

### Si necesita referencia de un carrusel anterior

SOLO en ese caso leer `~/.openclaw/workspace/carousel-archive.json`.
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
- Nico dice qué tipo y tema
- Carru genera el copy de TODAS las slides de una sola vez
- Muestra todo junto para aprobación del copy

### Paso 2 — Generación de imágenes (1 bloque)
- Una vez aprobado el copy, generar TODAS las slides
- Generar en secuencia pero sin volver a consultar archivos de estilo
- Entregar el paquete completo al agente Main

### Paso 3 — Revisión por Main + Feedback
- Main revisa (ver sección abajo)
- Si hay correcciones → Carru corrige solo las slides indicadas
- Si todo OK → mostrar a Nico

**Objetivo: 3-5 interacciones en vez de 10+**

---

## REGLA #4 — Revisión por Main (Sosa)

**Cuando Carru entrega las imágenes, Main las examina 1 POR 1 con la herramienta de visión.**

### Checklist por imagen:

#### 1. Ortografía castellana (CRÍTICO)
- [ ] Acentos correctos: á é í ó ú (no a e i o u cuando corresponde acento)
- [ ] Ñ donde corresponde (no "n" sola)
- [ ] ¿Signos de pregunta? → SIEMPRE ¿ al inicio Y ? al final
- [ ] ¡Signos de exclamación! → SIEMPRE ¡ al inicio Y ! al final
- [ ] Sin errores de tipeo o letras cortadas

#### 2. Brand
- [ ] Fondo oscuro (#0a0a0a a #111111 o el que corresponda al tipo)
- [ ] Gold es exactamente #e4c69a (no más claro, no más oscuro, no otro tono)
- [ ] Tipografía consistente entre slides (mismo font, mismo weight)
- [ ] @soynicolassosa presente donde corresponde

#### 3. Tipo-específico
- [ ] Italic SOLO si es Tipo I (key terms). Si es otro tipo → NO italic
- [ ] Emoji SOLO si es Tipo E, D, G. Si es Tipo I o H → NO emoji
- [ ] Elementos del tipo presentes (ej: Tipo E debe tener halo gold en portada)

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
→ Devolver a Carru con indicación PRECISA: "Slide 3: falta ¿ al inicio de la pregunta. Slide 5: el gold parece más claro que #e4c69a."
→ Carru corrige SOLO las slides indicadas, no regenera todo.

### Si todo OK:
→ Mostrar a Nico para aprobación final.

---

## REGLA #5 — Feedback por tipo

**El feedback de Nico se guarda POR TIPO, no global.**

### Cuando Nico da feedback:

1. Identificar el tipo del carrusel actual (ej: Tipo H)
2. Abrir `~/.openclaw/workspace/styles/feedback-tipo-H.md`
3. Agregar una entrada con este formato:

```
## 2026-03-27 — [tema del carrusel]
**Feedback:** [lo que dijo Nico]
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
- **Slide 1:** enviar la foto de referencia del CEO/producto + 1 slide aprobada anterior del mismo tipo como referencia
- **Slides 2+:** enviar SOLO la slide 1 recién generada como referencia de estilo
- **Nunca enviar más de 2 imágenes de referencia por prompt**

### Para fotos de CEOs (Tipo E):
- Descargar la foto REAL una sola vez al inicio
- Enviarla SOLO en el prompt de la slide 1 (portada)
- Para slides 2-7 no enviarla — ya no aparece el CEO

---

## Resumen de archivos

```
~/.openclaw/workspace/styles/
├── style-base.json          ← Leer SIEMPRE al inicio (~1KB)
├── tipo-A.json              ← Leer solo si es Tipo A
├── tipo-D.json              ← Leer solo si es Tipo D
├── tipo-E.json              ← Leer solo si es Tipo E
├── tipo-F.json              ← Leer solo si es Tipo F
├── tipo-G.json              ← Leer solo si es Tipo G
├── tipo-H.json              ← Leer solo si es Tipo H
├── tipo-I.json              ← Leer solo si es Tipo I
├── feedback-tipo-A.md       ← Feedback acumulado Tipo A
├── feedback-tipo-D.md       ← Feedback acumulado Tipo D
├── feedback-tipo-E.md       ← etc.
├── feedback-tipo-F.md
├── feedback-tipo-G.md
├── feedback-tipo-H.md
└── feedback-tipo-I.md

~/.openclaw/workspace/
├── carousel-archive.json    ← Solo si necesita referencia histórica
└── content-style.json       ← BACKUP — NO LEER
```

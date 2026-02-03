# Requerimiento: App Instructor CAMPESENA

> **Referencia visual:** `mockups/instructor-app-v3.html` (abrir en navegador)
> **Plataforma:** Flutter (Android prioritario, iOS secundario)
> **Tipo:** Offline-first, contenido embebido en assets

---

## 1. Que es la App del Instructor

Cada app agrupa todos los cursos de una **subcategoria** en un solo APK. Ejemplo: la subcategoria "Ganaderia bovina" contiene 6 cursos. El instructor abre una sola app y accede a todos ellos.

**Diferencia con la app del estudiante (existente):**

| | App Estudiante | App Instructor |
|---|---|---|
| APK | 1 curso = 1 APK | 1 subcategoria = 1 APK (N cursos) |
| Pantalla inicial | Portada del curso | Portada de la subcategoria |
| Navegacion | Tabs directos | Portada → Lista cursos → Curso → Tabs |

---

## 2. Flujo de Navegacion (10 pantallas)

```
Splash → Portada Subcategoria → Mis Cursos → Curso Detalle → Tabs → Podcast
                ↓
             Creditos
```

| # | Pantalla | ID ruta | Bottom Nav | Back |
|---|----------|---------|-----------|------|
| 1 | Splash | `/splash` | No | — |
| 2 | Portada Subcategoria | `/` | No | — |
| 3 | Creditos | `/credits` | No | ← Portada |
| 4 | Mis Cursos | `/courses` | No | ← Portada |
| 5 | Curso Detalle | `/course/:courseId` | Si (tab "Curso") | ← Cursos |
| 6 | Temas | `/course/:courseId/topics` | Si (tab "Temas") | ← Cursos |
| 7 | Podcast | `/course/:courseId/topics/podcast/:topicId` | No (fullscreen) | ← Temas |
| 8 | Glosario | `/course/:courseId/glossary` | Si (tab "Glosario") | ← Cursos |
| 9 | Cartilla PDF | `/course/:courseId/pdf` | Si (tab "Cartilla") | ← Cursos |
| 10 | Actividad | `/course/:courseId/activity` | Si (tab "Actividad") | ← Cursos |

### Bottom Navigation (5 tabs, solo dentro de un curso)

| Tab | Icono Material | Label |
|-----|---------------|-------|
| Curso | `school` | Curso |
| Temas | `menu_book` | Temas |
| Glosario | `dictionary` | Glosario |
| Cartilla | `description` | Cartilla |
| Actividad | `quiz` | Actividad |

La bottom nav NO se muestra en Splash, Portada, Creditos ni Mis Cursos.

---

## 3. Estructura de Archivos del Proyecto

```
APP_INSTRUCTOR_SUBCATEGORIA/
├── lib/
│   ├── main.dart
│   ├── models/
│   │   ├── subcategory_model.dart      # NUEVO: modelo de subcategoria
│   │   ├── course_model.dart           # NUEVO: modelo de curso (indice)
│   │   ├── academic_program_model.dart # Portada del curso (existente)
│   │   ├── topics_model.dart           # Temas y multimedia (existente)
│   │   ├── glossary_model.dart         # Glosario (existente)
│   │   ├── activities_model.dart       # Cuestionarios (existente)
│   │   ├── credits_model.dart          # Creditos (existente)
│   │   ├── pdf_model.dart              # Metadata PDF (existente)
│   │   └── models.dart                 # Barrel export
│   ├── services/
│   │   ├── subcategory_service.dart    # NUEVO: carga subcategory.json + lista cursos
│   │   ├── course_service.dart         # NUEVO: carga JSONs de un curso especifico
│   │   ├── audio_service.dart          # Reproductor (existente)
│   │   ├── db_service.dart             # SQLite progreso (modificado: +course_id)
│   │   ├── questionnaire_service.dart  # Estado quiz (existente)
│   │   ├── pagination_page_service.dart
│   │   └── services.dart               # Barrel export
│   ├── router/
│   │   ├── app_router.dart             # MODIFICADO: rutas anidadas por curso
│   │   └── scaffold_with_nested_navigation.dart
│   ├── screens/
│   │   ├── splash_screen.dart          # Splash (auto-navega a portada)
│   │   ├── portada_screen.dart         # NUEVO: portada de subcategoria
│   │   ├── credits_screen.dart         # NUEVO en este nivel (creditos de subcategoria)
│   │   ├── courses_screen.dart         # NUEVO: lista de cursos ("Mis Cursos")
│   │   ├── course_detail_screen.dart   # Detalle del curso (era HomeScreen)
│   │   ├── topics/
│   │   │   ├── topics_screen.dart
│   │   │   └── podcast_screen.dart
│   │   ├── glossary_screen.dart
│   │   ├── pdf_screen.dart
│   │   ├── activities/
│   │   │   ├── activity_screen.dart
│   │   │   ├── intro_activity_screen.dart
│   │   │   └── start_activity_screen.dart
│   │   └── screens.dart
│   ├── widgets/                        # Reutilizar existentes
│   │   ├── custom_navigation_bar.dart
│   │   ├── custom_app_bar.dart
│   │   ├── course_card.dart            # NUEVO: card de curso con franja lateral
│   │   ├── progress_ring.dart          # NUEVO: anillo de progreso SVG
│   │   └── widgets.dart
│   └── themes/
│       ├── app_theme.dart
│       └── themes.dart
├── assets/
│   ├── data/
│   │   ├── subcategory.json            # Info de subcategoria + indice de cursos
│   │   ├── credits.json                # Creditos generales de la subcategoria
│   │   └── courses/
│   │       ├── course_1/
│   │       │   ├── academic_program.json
│   │       │   ├── topics_program.json
│   │       │   ├── activities.json
│   │       │   ├── glossary.json
│   │       │   └── pdf.json
│   │       ├── course_2/
│   │       │   └── ... (mismos archivos)
│   │       └── course_N/
│   │           └── ...
│   ├── audios/
│   │   ├── intro_subcategory.mp3       # Audio de la portada de subcategoria
│   │   ├── course_1/
│   │   │   ├── intro_program.mp3
│   │   │   ├── CF1_Intro.mp3
│   │   │   ├── CF1_T1_P1.mp3
│   │   │   └── ...
│   │   ├── course_2/
│   │   │   └── ...
│   │   └── course_N/
│   │       └── ...
│   ├── images/
│   │   ├── cover_subcategory.png       # Ilustracion de la subcategoria
│   │   ├── course_1/
│   │   │   ├── cover_image.png
│   │   │   ├── bg-image-c1.png
│   │   │   └── ...
│   │   ├── course_2/
│   │   │   └── ...
│   │   └── course_N/
│   │       └── ...
│   ├── files/pdf/
│   │   ├── course_1/cartilla.pdf
│   │   ├── course_2/cartilla.pdf
│   │   └── course_N/cartilla.pdf
│   ├── fonts/                          # NunitoSans, Roboto, Robotoblack
│   └── icons/                          # icon-app.png
├── android/                            # MainActivity.kt con audio focus
└── pubspec.yaml
```

---

## 4. Estructura de Archivos JSON

### 4.1 subcategory.json (NUEVO)

Informacion de la subcategoria y el indice de cursos que contiene.

```json
{
  "id": "ganaderia_bovina",
  "area_tematica": "Pecuaria",
  "title": "Ganaderia bovina",
  "description": "Carne, leche, doble proposito y buenas practicas ganaderas",
  "cover_image": "assets/images/cover_subcategory.png",
  "audio_intro": "audios/intro_subcategory.mp3",
  "courses": [
    {
      "id": "course_1",
      "folder": "course_1",
      "title": "Implementacion de buenas practicas ganaderas en la produccion de leche bovina",
      "short_description": "Sanidad animal, bienestar, inocuidad y sostenibilidad en la produccion lechera.",
      "topics_count": 2,
      "audios_count": 9
    },
    {
      "id": "course_2",
      "folder": "course_2",
      "title": "Implementacion de BPG en la produccion de carne bovina",
      "short_description": "Manejo sanitario, nutricional y reproductivo del ganado de carne.",
      "topics_count": 3,
      "audios_count": 12
    }
  ]
}
```

**Campos del indice de cursos:**

| Campo | Tipo | Descripcion |
|-------|------|-------------|
| `id` | string | Identificador unico del curso |
| `folder` | string | Nombre de la carpeta en `assets/data/courses/` y `assets/audios/` |
| `title` | string | Titulo completo del curso |
| `short_description` | string | Descripcion breve (1-2 lineas) |
| `topics_count` | int | Cantidad de temas (para mostrar en la card sin cargar el JSON completo) |
| `audios_count` | int | Cantidad total de audios |

### 4.2 credits.json (nivel subcategoria)

Creditos compartidos de toda la subcategoria. Estructura **identica** a la existente.

```json
{
  "credits": [
    {
      "title_dependence": "ECOSISTEMA DE RECURSOS EDUCATIVOS DIGITALES",
      "persons": [
        {
          "name": "Claudia Johanna Gomez Perez",
          "role": "Responsable ecosistema",
          "zone": "Direccion General"
        }
      ]
    },
    {
      "title_dependence": "CONTENIDO INSTRUCCIONAL",
      "persons": [
        {
          "name": "Ana Catalina Cordoba Sus",
          "role": "Evaluadora instruccional",
          "zone": "Centro Agroempresarial y Desarrollo Pecuario - Regional Huila"
        }
      ]
    },
    {
      "title_dependence": "DISEÑO Y DESARROLLO DE APLICACION",
      "persons": [
        {
          "name": "Jhon Edinson Castañeda Oviedo",
          "role": "Programador de aplicaciones moviles",
          "zone": "Centro Agroempresarial y Desarrollo Pecuario - Regional Huila"
        }
      ]
    }
  ]
}
```

### 4.3 Archivos por curso (dentro de `courses/course_N/`)

Cada curso contiene los mismos JSONs que la app de estudiante existente. **No se modifica su estructura**:

| Archivo | Estructura | Referencia |
|---------|-----------|------------|
| `academic_program.json` | `{ id, program_title, text_summary_program, cover_image, audio_id, file_path_intro, program_subtitle }` | Portada del curso |
| `topics_program.json` | `{ topics: [{ id, title_topic, name_topic, background_image, progress, podcast: { multimedia: [...] } }] }` | Temas y audios |
| `glossary.json` | `{ glossary: [{ keyword, concepts: [{ concept, definition }] }] }` | Glosario A-Z |
| `activities.json` | `{ activities: [{ id, title, activity_summary, activity_description, questionnaire: [...] }] }` | Cuestionario |
| `pdf.json` | `{ pdf: [{ id, name_file, file_path, description }] }` | Metadata cartilla |

**Nota:** El campo `file_path` en los JSONs de cada curso debe apuntar a la ruta relativa dentro de la carpeta del curso. Ejemplo: `audios/course_1/CF1_Intro.mp3` en vez de `audios/CF1_Intro.mp3`.

---

## 5. Detalle Pantalla por Pantalla

### 5.1 Splash (pantalla 1)

```
┌─────────────────────────────────┐
│          ○                      │
│                          ○      │
│      ○                          │
│                                 │
│                                 │
│      ┌───────────────────┐      │
│      │  SENA  CAMPESENA  │      │
│      └───────────────────┘      │
│                                 │
│         P E C U A R I A         │
│                                 │
│          Ganaderia              │
│            bovina               │
│                                 │
│                                 │
│           ● ● ●                 │
│         (loading)               │
│                                 │
│       Toca para continuar       │
│                                 │
│                                 │
│  ═══════════════════════════    │
└─────────────────────────────────┘
  Fondo: gradiente verde → beige
  Click anywhere → Portada
```

**Proposito:** Pantalla de bienvenida animada. Se muestra brevemente y navega a la Portada.

**Elementos:**
- Fondo con gradiente verde (`#00AC00` → `#72CD56` → beige)
- Circulos decorativos blancos semitransparentes (3 circulos de fondo)
- Logo SENA CAMPESENA centrado (blanco, invertido)
- Label area tematica: "PECUARIA" — uppercase, letter-spacing 4px, blanco al 70% opacidad
- Titulo subcategoria: "Ganaderia bovina" — Nunito 26px, extrabold, blanco
- Animacion de 3 puntos verdes (bounce loop)
- Texto "Toca para continuar" — 12px, blanco al 50%

**Comportamiento:**
- Click en cualquier parte → navega a Portada Subcategoria
- Alternativa: auto-navegar despues de 2-3 segundos

**Datos necesarios:** `subcategory.json` → `area_tematica`, `title`

---

### 5.2 Portada Subcategoria (pantalla 2)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ PECUARIA          [SENA]  (i)  │
│░░░░░░░░░ header verde ░░░░░░░░░│
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│         fondo blanco ↓          │
│                                 │
│          ┌──────────┐           │
│         ╱    ____    ╲          │
│        │   /    \    │          │
│        │  | ILUS |   │  180px   │
│        │  | TRAC |   │          │
│         ╲  \____/   ╱           │
│          └──────────┘           │
│                                 │
│       Ganaderia bovina          │
│          (26px, bold)           │
│                                 │
│   Carne, leche, doble           │
│   proposito y buenas            │
│   practicas ganaderas           │
│                                 │
│  ┌───────────────────────────┐  │
│  │ (▶)  Presentacion del     │  │
│  │      programa             │  │
│  │      🎧 3:45 min          │  │
│  └───────────────────────────┘  │
│                                 │
│  ┌───────────────────────────┐  │
│  │ 📖  6 cursos disponibles  │  │
│  │ ████████░░░░░░░░  2 de 6  │  │
│  │ completados               │  │
│  └───────────────────────────┘  │
│                                 │
│  ┌───────────────────────────┐  │
│  │    Explorar cursos  →     │  │
│  └───────────────────────────┘  │
│       btn verde full-width      │
│                                 │
│      Creditos y licencia        │
│         (link gris)             │
│                                 │
│  ═══════════════════════════    │
└─────────────────────────────────┘
  Sin bottom nav
```

**Proposito:** Presentacion de la subcategoria antes de ver los cursos. Como la portada de una cartilla.

**Header verde** (gradiente `#00AC00` → `#72CD56`):
- Label area tematica: "PECUARIA" — uppercase, letter-spacing 3px, blanco 70%
- Logo SENA CAMPESENA pequeño (22px alto, blanco)
- Boton circular info/creditos (44x44px, fondo blanco 15%, icono `info` blanco) → navega a Creditos

**Contenido** (fondo blanco, esquinas redondeadas superiores 28px):

1. **Ilustracion circular grande** (180px diametro)
   - Borde verde suave `rgba(0,172,0,0.15)` de 3px
   - Fondo gradiente `green-bg` → beige
   - Imagen placeholder o SVG tematico dentro
   - Centrada horizontalmente

2. **Titulo subcategoria** — Nunito 26px, extrabold, color `#0A2741`, centrado

3. **Descripcion** — 14px, color `#71737B`, interlineado 1.55, centrado

4. **Card audio presentacion** (card blanca con sombra):
   - Boton play verde circular (44px) a la izquierda
   - Texto "Presentacion del programa" — 14px, bold, `#0A2741`
   - Icono headphones + duracion "3:45 min" — 12px, gris

5. **Card de resumen/stats** (card blanca con sombra):
   - Icono `menu_book` verde + "X cursos disponibles" — 15px, bold
   - Barra de progreso verde (8px alto)
   - Texto "X de Y completados" — 13px, verde bold

6. **Boton CTA** — "Explorar cursos →" full width, verde, bordes redondeados pill → navega a Mis Cursos

7. **Link creditos** — "Creditos y licencia" — 13px, gris, centrado → navega a Creditos

**Sin bottom nav. Indicador home (barra gris) en la parte inferior.**

**Datos necesarios:**
- `subcategory.json` → `area_tematica`, `title`, `description`, `cover_image`, `audio_intro`, `courses` (para contar)
- Progreso agregado: calcular desde SQLite cuantos cursos estan completados

---

### 5.3 Creditos (pantalla 3)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Portada                      │
│ Creditos                        │
│░░░░░░░░░ header verde ░░░░░░░░░│
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│  🏢 ECOSISTEMA DE RECURSOS     │
│     EDUCATIVOS DIGITALES        │
│  ───────────────────────────    │
│  Claudia Johanna Gomez Perez   │
│  Responsable ecosistema         │
│  Direccion General              │
│                                 │
│  Olga Constanza Bermudez       │
│  Responsable linea produccion   │
│  Direccion General              │
│  ───────────────────────────    │
│                                 │
│  📖 CONTENIDO INSTRUCCIONAL    │
│  ───────────────────────────    │
│  Ana Catalina Cordoba Sus      │
│  Evaluadora instruccional       │
│                                 │
│  Deya Maritza Cortes Enriquez  │
│  Autora                         │
│  Centro Agroempresarial...      │
│  ───────────────────────────    │
│                                 │
│  💻 DISEÑO Y DESARROLLO        │
│  ───────────────────────────    │
│  Gustavo Adolfo Contreras B.   │
│  Guionista validador            │
│  ... (scroll)                   │
│                                 │
│      ┌───────────────┐          │
│      │ SENA CAMPESENA│          │
│      └───────────────┘          │
│    Todos los derechos           │
│    reservados — 2024            │
│                                 │
│  ═══════════════════════════    │
└─────────────────────────────────┘
  Sin bottom nav
```

**Proposito:** Creditos de la subcategoria, accesible desde la Portada.

**Header verde:**
- Boton back "← Portada" — blanco 70%, 14px
- Titulo "Creditos" — Nunito 22px, extrabold, blanco

**Contenido** (fondo blanco, esquinas redondeadas superiores):
- Lista de secciones por `title_dependence` del JSON
- Cada seccion:
  - Icono Material verde (20px) + titulo dependencia en uppercase, gris, 13px, letter-spacing 0.5px
  - Lista de personas: nombre (14px, bold, `#0A2741`) + rol (12px, gris)
  - Si todas las personas comparten la misma zona: mostrar la zona una sola vez al final de la seccion (11px, gris claro)
- Al final: logo SENA CAMPESENA centrado (32px, 50% opacidad) + texto "Servicio Nacional de Aprendizaje SENA" + "Todos los derechos reservados — 2024"

**Datos necesarios:** `credits.json`

---

### 5.4 Mis Cursos (pantalla 4)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Portada                      │
│ Ganaderia bovina        [SENA] │
│░░░░░░░░░ header verde ░░░░░░░░░│
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│  Mis cursos  (6)                │
│                                 │
│  ┌─┬─────────────────────────┐  │
│  │█│ ↗ En progreso           │  │
│  │█│ Implementacion de       >  │
│  │█│ buenas practicas        │  │
│  │ │ ganaderas en la         │  │
│  │V│ produccion de leche     │  │
│  │E│                         │  │
│  │R│ 📖 2 temas  🎧 9 audios│  │
│  │D│ ████████░░░░░░░░░  40%  │  │
│  │E│                         │  │
│  └─┴─────────────────────────┘  │
│                                 │
│  ┌─┬─────────────────────────┐  │
│  │ │ ✦ Nuevo                 │  │
│  │G│ Implementacion de       >  │
│  │R│ BPG en la produccion    │  │
│  │I│ de carne bovina         │  │
│  │S│                         │  │
│  │ │ 📖 3 temas  🎧 12 audio│  │
│  │ │ ░░░░░░░░░░░░░░░░░  0%  │  │
│  └─┴─────────────────────────┘  │
│                                 │
│  ┌─┬─────────────────────────┐  │
│  │A│ ✓ Completado            │  │
│  │M│ Manejo de ganado        >  │
│  │A│ doble proposito         │  │
│  │R│                         │  │
│  │I│ 📖 2 temas  🎧 7 audios│  │
│  │L│ ████████████████  100%  │  │
│  └─┴─────────────────────────┘  │
│                                 │
│  ... (scroll para mas cursos)   │
│                                 │
│  ═══════════════════════════    │
└─────────────────────────────────┘
  Sin bottom nav
  Franja izq: █ verde=progreso,
  amarillo=completo, gris=nuevo
```

**Proposito:** Lista de todos los cursos de la subcategoria.

**Header verde:**
- Boton back "← Portada" — blanco 70%
- Titulo "Ganaderia bovina" — Nunito 22px, extrabold, blanco
- Logo SENA pequeño (22px, blanco)

**Contenido** (fondo blanco, esquinas redondeadas superiores):

1. **Encabezado de seccion:**
   - "Mis cursos" — Nunito 18px, extrabold, `#0A2741`
   - Badge circular verde con el conteo de cursos (24px, blanco, bold)

2. **Lista de course cards** (una por curso):
   - **Franja lateral izquierda** (5px ancho):
     - Verde gradiente: curso en progreso
     - Amarillo gradiente: curso completado
     - Gris: curso no iniciado
   - **Contenido de la card:**
     - **Badge de estado** (pill, 11px, bold):
       - En progreso: fondo verde-bg, texto verde, icono `trending_up`
       - Nuevo: fondo azul-bg, texto azul, icono `new_releases`
       - Completado: fondo amarillo-bg, texto amarillo, icono `check_circle`
     - **Titulo del curso** — 14px, bold, `#0A2741`, max 3 lineas
     - **Chevron derecho** — icono `chevron_right`, gris claro
     - **Chips info** — "X temas" con icono `menu_book` + "X audios" con icono `headphones`
     - **Barra de progreso** (6px) + porcentaje bold
   - Click en card → navega a Curso Detalle

3. Cursos no disponibles se muestran con `opacity: 0.6`

**Sin bottom nav. Sin boton de creditos (ya esta en la Portada).**

**Datos necesarios:**
- `subcategory.json` → `courses[]` para titulo, topics_count, audios_count
- Progreso por curso: calcular desde SQLite

**Estado de cada curso** (calculado):
- **Nuevo**: 0% de audios completados
- **En progreso**: entre 1% y 99%
- **Completado**: 100%

---

### 5.5 Curso Detalle (pantalla 5)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Cursos                       │
│  ↗ En progreso                 │
│░░░░░░ header azul oscuro ░░░░░░│
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│          ┌──────────┐           │
│         ╱            ╲          │
│        │   PORTADA    │ 160px   │
│        │   IMAGEN     │         │
│         ╲            ╱          │
│          └──────────┘           │
│                                 │
│   Implementacion de buenas      │
│   practicas ganaderas en la     │
│   produccion de leche bovina    │
│          (20px, bold)           │
│                                 │
│   Este programa aborda los      │
│   fundamentos y practicas       │
│   esenciales para la produccion │
│   sostenible de leche bovina.   │
│                                 │
│  ┌───────────────────────────┐  │
│  │ (▶)  Presentacion del     │  │
│  │      programa    5:47  🎧 │  │
│  └───────────────────────────┘  │
│                                 │
│  PROGRESO GENERAL          40%  │
│  ████████████░░░░░░░░░░░░░░░░  │
│                                 │
├─────────────────────────────────┤
│ [Curso] [Temas] [Glos] [📄] [?]│
│  ^^^^                           │
│  activo (icono school)          │
└─────────────────────────────────┘
```

**Proposito:** Portada individual del curso seleccionado. Es el equivalente a la HomeScreen de la app de estudiante.

**Header oscuro** (gradiente `#0A2741` → `#11696B` → `#72CD56`):
- Boton back "← Cursos" — blanco 70%
- Badge de estado blanco semitransparente

**Contenido** (fondo blanco, esquinas redondeadas):

1. **Ilustracion circular** (160px) centrada — imagen de portada del curso
2. **Titulo del curso** — Nunito 20px, extrabold, `#0A2741`, centrado
3. **Descripcion** — 14px, gris, interlineado 1.55, centrado
4. **Card audio intro** (card blanca):
   - Boton play verde (44px) + "Presentacion del programa" + duracion + icono headphones
5. **Progreso general** — label uppercase + porcentaje bold verde + barra de progreso (8px)

**Bottom nav activa** con tab "Curso" seleccionado (icono `school`).

**Datos necesarios:**
- `courses/course_N/academic_program.json` → `program_title`, `text_summary_program`, `cover_image`, `file_path_intro`
- Progreso del curso desde SQLite

---

### 5.6 Temas (pantalla 6)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Cursos                       │
│ Temas  (2)                      │
│            fondo beige          │
│                                 │
│  ┌───────────────────────────┐  │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  │▓ TEMA 1  ▶ Cursando      ▓│  │
│  │▓                     ┌──┐▓│  │
│  │▓ Muestreo de         │75│▓│  │
│  │▓ variables            │% │▓│  │
│  │▓ ambientales en      └──┘▓│  │
│  │▓ agroecosistemas   anillo▓│  │
│  │▓                         ▓│  │
│  │▓ 🎧 6 audios             ▓│  │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  └───────────────────────────┘  │
│   foto bg + overlay oscuro      │
│                                 │
│  ┌───────────────────────────┐  │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  │▓ TEMA 2  No iniciado     ▓│  │
│  │▓                     ┌──┐▓│  │
│  │▓ Manejo sanitario    │ 0│▓│  │
│  │▓ del hato            │% │▓│  │
│  │▓ lechero             └──┘▓│  │
│  │▓                         ▓│  │
│  │▓ 🎧 3 audios             ▓│  │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │
│  └───────────────────────────┘  │
│                                 │
├─────────────────────────────────┤
│ [Curso] [Temas] [Glos] [📄] [?]│
│          ^^^^^^                 │
│          activo                 │
└─────────────────────────────────┘
  Badge = estado cualitativo
  Anillo = unico lugar con %
```

**Proposito:** Lista de temas del curso con indicador de progreso.

**Header beige:**
- Boton back "← Cursos"
- Titulo "Temas" — Nunito 22px, extrabold + badge circular con conteo

**Lista de topic cards** (una por tema):
- **Fondo**: gradiente de color (cada tema un tono distinto)
- **Overlay oscuro**: gradiente de transparente a `rgba(10,39,65,0.75)`
- **Contenido sobre overlay**:
  - Pill "TEMA N" — bold, blanco, fondo verde/teal
  - **Badge de estado cualitativo** (NO porcentaje, el % solo va en el anillo):
    - "Cursando" — fondo verde 20%, texto verde claro, icono `play_circle` (si tiene progreso > 0% y < 100%)
    - "No iniciado" — fondo blanco 15%, texto blanco 60% (si 0%)
    - "Completado" — fondo verde 20%, texto verde claro, icono `check_circle` (si 100%)
  - Titulo del tema — Nunito 16px, bold, blanco
  - Badge "X audios" — fondo blanco 15%, texto blanco 80%, icono `headphones`
- **Anillo de progreso** (54px) a la derecha:
  - SVG circular con porcentaje numerico en el centro
  - Stroke verde o gris segun progreso
- Click en card → navega a Podcast del tema

**Bottom nav activa** con tab "Temas" seleccionado.

**Datos necesarios:** `courses/course_N/topics_program.json` + progreso desde SQLite

---

### 5.7 Podcast / Reproductor (pantalla 7)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Temas                        │
│                                 │
│  ┌───────────────────────────┐  │
│  │                           │  │
│  │      🎧  (ilustracion)    │  │
│  │         16:9              │  │
│  └───────────────────────────┘  │
│░░░░ gradiente azul → teal ░░░░░│
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│  ≋ Reproduciendo   Tema 1      │
│                                 │
│  Introduccion                   │
│  (20px, extrabold)              │
│                                 │
│  Descripcion general del        │
│  muestreo de variables...       │
│                                 │
│  1:23 ━━━━━━━●──────── 5:47    │
│        slider (thumb amarillo)  │
│                                 │
│    🔀    ⏮   (▶)   ⏭    🔁    │
│  shuffle prev play next repeat  │
│              64px               │
│              teal               │
│                                 │
│  PLAYLIST                       │
│  ───────────────────────────    │
│  ≋  Introduccion   EN CURSO    │
│     EQ bars         5:47       │
│                                 │
│  2  Ganado y empresa   8:23    │
│  3  Razas bovinas      6:15    │
│  4  Alimentacion  COMPLETO     │
│                        7:02    │
│  5  Sanidad animal     9:11    │
│  6  Reproduccion       5:45    │
│                                 │
└─────────────────────────────────┘
  Sin bottom nav (fullscreen)
```

**Proposito:** Reproductor de audio con playlist del tema seleccionado.

**Header oscuro** (gradiente `#0A2741` → `#11696B`):
- Boton back "← Temas"
- Area de imagen/ilustracion del tema (aspect ratio 16:9, esquinas redondeadas)

**Contenido** (fondo blanco, esquinas redondeadas):

1. **Now playing:**
   - Badge "Reproduciendo" (teal) + "Tema N"
   - Titulo del audio — Nunito 20px, extrabold, `#0A2741`
   - Descripcion — 13px, gris

2. **Slider de progreso:**
   - Tiempo actual — tiempo total
   - Track (4px, beige), fill (teal), thumb (16px, amarillo `#E6F158` con borde teal)

3. **Controles:**
   - Shuffle | Previous | Play/Pause (64px, teal) | Next | Repeat

4. **Playlist:**
   - Lista de audios del tema
   - Cada item: numero (circulo 28px) + titulo + duracion
   - Item activo: numero con barras EQ animadas, titulo verde bold
   - Badges: "EN CURSO" (verde) o "COMPLETO" (amarillo)

**Sin bottom nav (pantalla fullscreen).**

**Datos necesarios:** `courses/course_N/topics_program.json` → `topics[].podcast.multimedia[]`

**Funcionalidad de audio (reutilizar AudioService existente):**
- Play/Pause, Next/Previous, Shuffle, Loop
- Slider de progreso con seek
- Audio focus nativo via MethodChannel (pausa al recibir llamada)
- Marcar audio como `played` al iniciar y `complete` al terminar en SQLite

---

### 5.8 Glosario (pantalla 8)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Cursos                       │
│ 📖 Glosario                    │
│            fondo beige          │
│                                 │
│ [A] B  C  D  E  F  G  H  M ... │
│  ▲ activo (verde)    scroll →   │
│                                 │
│  ┌───────────────────────────┐  │
│  │                           │  │
│  │  (A)  Agroecosistema      │  │
│  │       Sistema ecologico   │  │
│  │       modificado por el   │  │
│  │       ser humano para la  │  │
│  │       produccion...       │  │
│  │  ─────────────────────    │  │
│  │                           │  │
│  │  (A)  Aforo               │  │
│  │       Metodo de medicion  │  │
│  │       de la cantidad de   │  │
│  │       forraje disponible  │  │
│  │       en un potrero.      │  │
│  │  ─────────────────────    │  │
│  │                           │  │
│  │  (A)  Alimentacion        │  │
│  │       balanceada          │  │
│  │       Dieta que           │  │
│  │       proporciona los     │  │
│  │       nutrientes...       │  │
│  │  ─────────────────────    │  │
│  │       ... (scroll)        │  │
│  └───────────────────────────┘  │
│                                 │
├─────────────────────────────────┤
│ [Curso] [Temas] [Glos] [📄] [?]│
│                 ^^^^^^          │
│                 activo          │
└─────────────────────────────────┘
  (A) = circulo verde con letra
```

**Proposito:** Glosario alfabetico de terminos del curso.

**Header beige:**
- Boton back "← Cursos"
- Icono `dictionary` verde + "Glosario" — Nunito 22px

**Barra alfabetica:**
- Scroll horizontal de botones con letras disponibles
- Letra activa: fondo verde, texto blanco, sombra

**Lista de terminos** (fondo blanco):
- Cada termino: circulo con letra (36px, fondo verde-bg, texto verde, Nunito bold) + termino bold + definicion gris

**Bottom nav activa** con tab "Glosario" seleccionado.

**Datos necesarios:** `courses/course_N/glossary.json`

---

### 5.9 Cartilla PDF (pantalla 9)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Cursos                       │
│ 📄 Cartilla                    │
│            fondo beige          │
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│                                 │
│       ┌─────────────────┐       │
│       │            [PDF]│       │
│       │                 │       │
│       │                 │       │
│       │    📄           │       │
│       │  (icono PDF)    │ 240px │
│       │                 │       │
│       │                 │       │
│       │  cartilla.pdf   │       │
│       └─────────────────┘       │
│             180px               │
│                                 │
│     Cartilla del programa       │
│        (20px, extrabold)        │
│                                 │
│    Material complementario      │
│    con guias practicas,         │
│    procedimientos y fichas      │
│    tecnicas.                    │
│                                 │
│  ┌───────────────────────────┐  │
│  │   ↗  Abrir cartilla      │  │
│  └───────────────────────────┘  │
│       btn verde full-width      │
│                                 │
├─────────────────────────────────┤
│ [Curso] [Temas] [Glos] [📄] [?]│
│                         ^^^^    │
│                         activo  │
└─────────────────────────────────┘
```

**Proposito:** Acceso al material PDF complementario del curso.

**Header beige:**
- Boton back "← Cursos"
- Icono `description` verde + "Cartilla" — Nunito 22px

**Contenido centrado** (fondo blanco):
1. Preview de cartilla (180x240px, fondo beige, borde, icono `picture_as_pdf`, badge "PDF")
2. "Cartilla del programa" — Nunito 20px, extrabold
3. Descripcion — 14px, gris
4. Boton "Abrir cartilla" — verde, full width, icono `open_in_new`

**Bottom nav activa** con tab "Cartilla" seleccionado.

**Datos necesarios:** `courses/course_N/pdf.json`

---

### 5.10 Actividad (pantalla 10)

```
┌─────────────────────────────────┐
│ 9:41              ▮▮▮ ⟨  ■     │
├─────────────────────────────────┤
│ ← Cursos                       │
│ ❓ Actividad                    │
│            fondo beige          │
├────────┐                 ┌──────┤
│        └─────────────────┘      │
│                                 │
│  ┌───────────────────────────┐  │
│  │   ○                       │  │
│  │          🎓               │  │
│  │      (ilustracion)    ○   │  │
│  │   ○                       │  │
│  └───────────────────────────┘  │
│   gradiente verde suave 16:10   │
│                                 │
│     Actividad evaluativa        │
│        (20px, extrabold)        │
│                                 │
│   Pon a prueba tus              │
│   conocimientos sobre           │
│   buenas practicas ganaderas    │
│   con este cuestionario.        │
│                                 │
│  ┌───────────────────────────┐  │
│  │                           │  │
│  │    10     │   4    │  1   │  │
│  │ Preguntas│Opciones│Correct│  │
│  │          │        │  a   │  │
│  │  (numeros grandes verdes) │  │
│  └───────────────────────────┘  │
│                                 │
│  ┌───────────────────────────┐  │
│  │  ▶  Iniciar actividad     │  │
│  └───────────────────────────┘  │
│       btn verde full-width      │
│                                 │
├─────────────────────────────────┤
│ [Curso] [Temas] [Glos] [📄] [?]│
│                              ^^^│
│                           activo│
└─────────────────────────────────┘
```

**Proposito:** Introduccion y acceso al cuestionario evaluativo del curso.

**Header beige:**
- Boton back "← Cursos"
- Icono `quiz` verde + "Actividad" — Nunito 22px

**Contenido** (fondo blanco):
1. Ilustracion decorativa (aspect ratio 16:10, fondo gradiente verde suave, icono `school`)
2. "Actividad evaluativa" — Nunito 20px, extrabold
3. Descripcion del cuestionario — 14px, gris
4. Card de estadisticas (3 columnas):
   - Preguntas (numero grande verde, Nunito 32px, bold 900)
   - Opciones por pregunta
   - Correctas por pregunta
5. Boton "Iniciar actividad" — verde, full width, icono `play_arrow`

**Bottom nav activa** con tab "Actividad" seleccionado.

**Datos necesarios:** `courses/course_N/activities.json`

---

## 6. Paleta de Colores

| Variable | Hex | Uso |
|----------|-----|-----|
| `green` | `#00AC00` | Acciones principales, botones, progreso |
| `green-dark` | `#009000` | Hover / pressed states |
| `green-light` | `#72CD56` | Gradientes, acentos |
| `green-soft` | `#B4E577` | Fondos decorativos |
| `green-bg` | `#E8F5E8` | Fondos de badges verdes |
| `yellow` | `#E6F158` | Thumb del slider, completados |
| `teal` | `#11696B` | Reproductor, gradientes oscuros |
| `teal-dark` | `#0D5254` | Variante oscura del teal |
| `blue-dark` | `#0A2741` | Textos principales, headers oscuros |
| `blue-light` | `#0092CE` | Badges "Nuevo", enlaces |
| `beige` | `#EAE7E5` | Fondo general de la app |
| `beige-light` | `#F5F2F0` | Fondos secundarios |
| `white` | `#FFFFFF` | Cards, contenido principal |
| `gray` | `#71737B` | Textos secundarios |
| `gray-light` | `#BEBEBE` | Iconos deshabilitados, placeholders |
| `danger` | `#FF555D` | Errores |
| `warning` | `#FFB400` | Franjas y badges de completados |

---

## 7. Tipografia

| Familia | Uso | Weights |
|---------|-----|---------|
| **Nunito** | Titulos, numeros grandes, badges | 400, 600, 700, 800, 900 |
| **Nunito Sans** | Body text, descripciones, UI general | 400, 600, 700, 800 |
| **Roboto** | Variantes monoespaciadas (timers) | 400, 700 (black) |

Text scaling fijo a 1.0 (`MediaQuery.copyWith(textScaler: TextScaler.linear(1.0))`).

---

## 8. Base de Datos Local (SQLite)

Archivo: `campesena_instructor.db`

```sql
CREATE TABLE audio_heard (
  ID              INTEGER PRIMARY KEY,
  course_id       VARCHAR(20),    -- ID del curso (course_1, course_2, ...)
  topic_id        VARCHAR(10),    -- ID del tema (1, 2, ...)
  multimedia_id   VARCHAR(10),    -- ID del audio (CF1_Intro, CF1_T1_P1, ...)
  played          INTEGER,        -- 1 = el usuario inicio la reproduccion
  complete        INTEGER          -- 1 = el audio se reprodujo completo
);
```

**Cambio vs app estudiante:** se agrega la columna `course_id` para diferenciar el progreso entre cursos.

**Calculos de progreso:**
- Progreso de un tema = `audios completos del tema / total audios del tema`
- Progreso de un curso = `audios completos del curso / total audios del curso`
- Progreso de la subcategoria = `cursos completados / total cursos`
- Un curso esta "completado" cuando el 100% de sus audios tienen `complete = 1`

---

## 9. Stack Tecnologico

| Componente | Tecnologia | Version |
|-----------|-----------|---------|
| Framework | Flutter / Dart | 3.1.5+ / 3.2.2+ |
| Estado | Provider (MultiProvider) | 6.1.1 |
| Navegacion | go_router (StatefulShellRoute) | 14.4.1 |
| Audio | audioplayers + MethodChannel nativo | 6.1.0 |
| Base de datos | sqflite | 2.3.0 |
| Slider audio | syncfusion_flutter_sliders | 27.1.58 |
| PDF | open_file | 3.3.2 |
| Rutas FS | path_provider | 2.1.1 |
| Iconos | flutter_launcher_icons | 0.14.1 |
| Iconos UI | Material Symbols Outlined | (via Google Fonts) |

---

## 10. Convencion de Nombres

### App ID
```
com.podcast.instructor_campesena_[idSubcategoria]_[iniciales]_v[DDMMAA]
```
Ejemplo: `com.podcast.instructor_campesena_ganaderia_bovina_GB_v020226`

### Version
```
1.DDMM.HHMM+MM
```

### Assets de audio
```
audios/course_N/intro_program.mp3       # Intro del curso
audios/course_N/CF{tema}_Intro.mp3      # Intro del tema
audios/course_N/CF{tema}_T{sub}_P{parte}.mp3  # Audio de contenido
```

---

## 11. Notas de Implementacion

1. **Offline-first:** Todo el contenido va embebido en assets. No hay backend ni llamadas HTTP.

2. **Carga de datos:** El `SubcategoryService` carga `subcategory.json` al inicio. Al entrar a un curso, el `CourseService` carga los JSONs de ese curso bajo demanda.

3. **Audio focus nativo:** Reutilizar el MethodChannel `com.example.app/audio` de `MainActivity.kt` para pausar al perder audio focus (llamadas, WhatsApp, etc.).

4. **Patron template:** Para crear una nueva app de instructor para otra subcategoria, se copia la carpeta del proyecto y se reemplazan los archivos de assets (audios, imagenes, JSONs).

5. **Se reutiliza el 80% del codigo de la app de estudiante.** Las pantallas internas de un curso (Podcast, Glosario, PDF, Actividad) son practicamente identicas. Los cambios principales son:
   - Nuevas pantallas: Portada, Creditos, Mis Cursos
   - CourseDetailScreen (renombrado desde HomeScreen)
   - Router con rutas anidadas por `courseId`
   - DBService con columna `course_id`
   - Bottom nav con tab "Curso" (icono `school`) en vez de "Inicio" (icono `home`)

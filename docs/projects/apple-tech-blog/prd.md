# El Mate Digital — Product Requirements Document (PRD)

> **Documento:** PRD v1.0 — listo para handoff a Architect
> **Autor:** Morgan (PM Agent) — AIOS
> **Fecha:** 2026-05-24
> **Estado:** ✅ Cerrado y validado por PM Checklist (READY FOR ARCHITECT)
> **Brief de origen:** `docs/projects/apple-tech-blog/project-brief.md` v1.2
> **Idioma del producto:** Español-LATAM (léxico neutral compatible con España)
> **Infra de desarrollo y deploy MVP:** Local (`localhost`) + Vercel (preview + production en `*.vercel.app`). Registro de dominio propio **postergado** — se decide cuándo y cuál más adelante.

---

## 1. Goals and Background Context

### 1.1 Goals

- Lanzar un blog independiente en español-LATAM sobre Apple/tech con experiencia de lectura premium, móvil-first y velocidad top-tier (Core Web Vitals en p75 móvil).
- Construir autoridad editorial vía cobertura ágil + análisis con voz propia y contexto regional (LATAM + España), diferenciada de medios traductores/clickbait.
- Operar un panel super-admin propio que permita al dueño controlar el 100% del ciclo editorial (creación, programación, media, SEO, monetización) sin tocar código.
- Sostener una cadencia editorial mínima de **≥4 artículos/semana** durante los primeros 6 meses.
- Alcanzar **50.000 sesiones/mes** y **5.000 suscriptores de newsletter activos** al mes 12 post-lanzamiento.
- **Ingresos brutos:** alcanzar **≥USD 5.000/mes al mes 12** como meta (mix AdSense + afiliados Amazon + sponsored + premium si ya está activado), con un **piso de USD 1.000/mes** una vez activadas todas las capas de monetización del MVP. Crecimiento más allá de USD 5.000/mes se reevalúa post-Año 1.
- Dejar lista la **infraestructura de membresía premium en MVP** (data models + Stripe SDK + feature flag) para que su activación pública en Fase 2 (mes 6-9) no requiera migraciones ni refactors.
- Publicar y respetar una política editorial transparente sobre uso de IA (asistencia sí, generación autónoma no) en página `/etica-editorial`.
- Mantener costo operativo de infra **< USD 80/mes** hasta los 100k pageviews/mes.

### 1.2 Background Context

Los lectores hispanohablantes interesados en Apple hoy navegan entre medios generalistas con cobertura superficial, blogs específicos saturados de ads programáticos intrusivos, y fuentes en inglés. Falta una propuesta editorial que combine cobertura ágil + análisis de calidad con experiencia de lectura sin fricción y contexto regional (precios, disponibilidad, planes de operador, llegada de features como Apple Intelligence a LATAM/España).

**El Mate Digital** se posiciona como esa fuente. La metáfora del mate — ritual diario, compartido, pausado — guía el contraste con la cobertura tech tradicional: lectura cómoda, voz humana, transparencia (afiliados marcados, política de IA visible) y monetización en capas que escala con el tráfico sin canibalizar la experiencia. El MVP se enfoca en los tres pilares del brief: sitio público optimizado, panel super-admin propio, y monetización modular (AdSense + afiliados + newsletter desde día 1; premium con infra lista pero activación diferida a Fase 2; comentarios diferidos a Fase 2 para no sobrecargar al editor único).

### 1.3 Change Log

| Date       | Version | Description                                           | Author |
| ---------- | ------- | ----------------------------------------------------- | ------ |
| 2026-05-24 | 0.1     | Borrador inicial — Sección 1 Goals & Background       | Morgan |
| 2026-05-24 | 0.2     | Sección 2 Requirements (FR + NFR)                     | Morgan |
| 2026-05-24 | 0.3     | Ajustes del dueño: ingresos 5K/12m + IA solo interna + sin dominio MVP | Morgan |
| 2026-05-24 | 0.4     | Sección 3 UI Design Goals (visión, screens, branding, plataformas) | Morgan |
| 2026-05-24 | 0.5     | Sección 4 Technical Assumptions (stack, repo, arquitectura, testing) | Morgan |
| 2026-05-24 | 0.6     | Sección 5 Epic List (6 épicas secuenciales del MVP)   | Morgan |
| 2026-05-24 | 0.7     | Sección 6 — Epic 1 Foundation & Canary (stories + AC) | Morgan |
| 2026-05-24 | 0.8     | Sección 6 — Epic 2 Editorial Core (Posts + Render)    | Morgan |
| 2026-05-24 | 0.9     | Sección 6 — Epic 3 Public Site UX & SEO               | Morgan |
| 2026-05-24 | 0.10    | Sección 6 — Epic 4 Newsletter (Resend + doble opt-in) | Morgan |
| 2026-05-24 | 0.11    | Sección 6 — Epic 5 Monetization (AdSense + afiliados + premium infra) | Morgan |
| 2026-05-24 | 0.12    | Sección 6 — Epic 6 Editorial Hardening & Launch Polish | Morgan |
| 2026-05-24 | 0.13    | Sección 7 — Checklist Results + Next Steps + Handoff Prompts | Morgan |
| 2026-05-24 | 1.0     | PRD v1.0 — cierre y handoff a Architect               | Morgan |

---

## 2. Requirements

### 2.1 Functional Requirements

#### Publicación y contenido

- **FR1:** El sistema debe permitir crear, editar, guardar como borrador, programar y publicar artículos mediante un editor enriquecido con soporte Markdown/MDX o Lexical (Payload), embeds de YouTube/X/tweets/code blocks y manejo de imágenes responsive con optimización automática.
- **FR2:** Cada artículo debe soportar versionado (historial de cambios) y permitir revertir a una versión previa desde el panel admin.
- **FR3:** El sistema debe permitir programar publicación con fecha y hora exactas (timezone configurable) y disparar la publicación automáticamente.
- **FR4:** Cada artículo debe permitir definir editorialmente: título, slug editable, meta-title, meta-description, imagen destacada, autor, fecha de publicación, categoría primaria, tags múltiples, lectura relacionada manual u automática.

#### Taxonomía

- **FR5:** El sistema debe soportar una taxonomía de **categorías por producto Apple** (iPhone, Mac, iPad, Watch, Vision, Servicios) y **tipos de contenido** (noticia, rumor, análisis, tutorial, reseña, guía de compra) más un sistema libre de tags. Cada artículo debe estar asociado obligatoriamente a una categoría y un tipo de contenido.
- **FR6:** El panel admin debe permitir crear, editar y archivar categorías, tipos de contenido y tags sin tocar código.

#### Sitio público

- **FR7:** El home público debe mostrar un módulo de destacados editorial-curados, últimas publicaciones cronológicas y secciones agrupadas por categoría/tipo.
- **FR8:** La página de artículo debe incluir tabla de contenidos (TOC) auto-generada por encabezados, información del autor, fecha de publicación y última actualización, tiempo de lectura estimado, botones de compartir (X, Facebook, WhatsApp, copiar enlace) y lista de artículos relacionados.
- **FR9:** El sitio debe ofrecer páginas dedicadas para listados de categoría, tag y autor, con paginación o scroll infinito.
- **FR10:** El sitio debe ofrecer búsqueda básica por texto libre (Postgres FTS en MVP) con resultados resaltados.
- **FR11:** El sitio debe ofrecer páginas estáticas obligatorias: `/about`, `/etica-editorial`, `/politica-de-privacidad`, `/aviso-legal`, `/politica-de-afiliados` y `/contacto`, editables desde admin.

#### SEO

- **FR12:** El sistema debe generar sitemap XML dinámico, `robots.txt`, URLs canónicas, tags Open Graph + Twitter Cards, y JSON-LD schema.org (Article, BreadcrumbList, Person, Organization) en todas las páginas relevantes.
- **FR13:** El sistema debe publicar feeds RSS por categoría y un feed global.

#### Panel super-admin

- **FR14:** El panel admin (Payload) debe requerir autenticación con email + contraseña y soportar 2FA (TOTP) obligatorio para el rol super-admin.
- **FR15:** El admin debe exponer media library con upload, búsqueda, etiquetado, optimización automática (resize + WebP/AVIF) y borrado seguro.
- **FR16:** El admin debe permitir previsualizar drafts (URL única con token) sin publicarlos.
- **FR17:** El admin debe mostrar un dashboard básico con métricas de publicación (artículos del mes, drafts pendientes, programados próximos) y atajos al editor.

#### Newsletter

- **FR18:** El sistema debe ofrecer formulario de suscripción a newsletter con flujo de doble opt-in (email de confirmación con token expirante), integrado con Resend.
- **FR19:** El sistema debe ofrecer página pública de archivo de newsletters publicadas y permitir gestionar la lista (importar/exportar/segmentar) desde el admin.

#### Monetización

- **FR20:** El sistema debe permitir gestionar slots de AdSense configurables (header, in-feed home, in-article cada N párrafos, sidebar, footer) con toggle on/off global y por artículo.
- **FR21:** El sistema debe soportar enlaces de afiliados Amazon mediante shortlinks internos con tracking de clicks, y debe agregar automáticamente disclaimer visible en artículos que contienen al menos un enlace afiliado.
- **FR22 (Premium — infra preparada, activación Fase 2):** El sistema debe incluir desde MVP las collections `Plan`, `Subscription` y `Member` en Payload, el helper `requiresMembership(post)` y el SDK Stripe instalado, todo gobernado por el feature flag `PREMIUM_ENABLED` (default `false`). Cuando el flag está en `false`, no se exponen rutas públicas de checkout ni gating activo de contenido; cuando se active en Fase 2, debe poder funcionar sin migraciones de datos.

#### Política editorial y transparencia

- **FR23:** La página `/etica-editorial` debe declarar la política de uso de IA (asistencia permitida, generación autónoma no, firma humana obligatoria), la política de afiliados (qué se marca y cómo), la política de correcciones (cómo se publican erratas) y el código de autoría.
- **FR24:** Cada artículo escrito con asistencia material de IA debe poder marcarse mediante un metadato interno desde el admin. Este metadato es **estrictamente interno y auditable solo por el dueño** (no se renderiza como badge ni se expone vía API pública en MVP). Sirve para auditoría editorial interna y para futuras decisiones de transparencia (que podrían materializarse en Fase 2+).

#### Plataforma y UX transversal

- **FR25:** El sitio debe ofrecer modo claro/oscuro con persistencia en cliente y respeto inicial del setting del sistema (`prefers-color-scheme`).
- **FR26:** El sistema debe estar **preparado para i18n** (estructura de rutas y modelo de datos compatible con futuras locales `es-ES` adicional / `en` / `pt`), aunque MVP sólo expone `es-LATAM`.
- **FR27:** El admin debe ofrecer logs de actividad básicos (quién publicó qué y cuándo) para auditoría y debugging.

### 2.2 Non-Functional Requirements

#### Performance

- **NFR1:** El sitio debe cumplir Core Web Vitals en p75 móvil: **LCP < 2.0 s**, **INP < 200 ms**, **CLS < 0.05**.
- **NFR2:** El TTFB del sitio público debe ser **< 600 ms** (favorecido por ISR + edge caching de Vercel).
- **NFR3:** Lighthouse en móvil debe puntuar **≥ 90 en Performance, SEO y Accessibility** en las páginas críticas (home, artículo, categoría) al momento del lanzamiento.

#### Escalabilidad y costos

- **NFR4:** El costo operativo total de infraestructura (hosting + DB + media + email) debe mantenerse **< USD 80/mes hasta 100.000 pageviews/mes**.
- **NFR5:** El sistema debe soportar al menos **100.000 pageviews/mes** sin degradación medible de performance ni costos sobre umbral, asumiendo ISR + CDN.

#### Disponibilidad

- **NFR6:** El sitio público debe operar con disponibilidad **≥ 99.5 % mensual**, apoyándose en la SLA de Vercel y Neon (sin compromisos contractuales propios al lector).
- **NFR7:** Las publicaciones programadas no deben perderse ante restart o fallo transitorio del runtime (idempotencia y reintentos en el job de publicación).

#### Seguridad

- **NFR8:** Todo el tráfico debe servirse exclusivamente sobre **HTTPS** con HSTS habilitado.
- **NFR9:** El acceso al panel admin debe exigir **2FA TOTP** obligatorio para todo super-admin y aplicar **rate-limiting** en login, signup de newsletter y endpoints de comentarios (cuando se activen en Fase 2).
- **NFR10:** El sistema debe aplicar **Content Security Policy** estricta (limitando orígenes de script, style, image, frame), cabeceras `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`.
- **NFR11:** Las dependencias de terceros deben pasar **escaneo automático de vulnerabilidades** en CI (Dependabot o equivalente).

#### Privacidad y cumplimiento

- **NFR12:** El sistema debe cumplir con **GDPR, LOPDGDD (España), LFPDPPP (México) y Habeas Data (Argentina)**: política de privacidad clara, base legal por finalidad, derecho al olvido aplicable a suscriptores y (cuando aplique) comentarios.
- **NFR13:** Si se cargan cookies que requieran consentimiento (AdSense personalizado, etc.), el sitio debe exponer un banner de consentimiento conforme (CMP certificada o equivalente) que bloquee scripts hasta consentimiento explícito.
- **NFR14:** El sistema debe exponer un **endpoint/flujo DSAR** (Data Subject Access Request) para que cualquier usuario pueda solicitar acceso, rectificación o borrado de sus datos personales.
- **NFR15:** La base de datos PostgreSQL debe tener **backups automáticos diarios con retención ≥ 7 días** (cubierto por Neon).

#### Compatibilidad

- **NFR16:** El sitio debe funcionar correctamente en navegadores **evergreen**: Safari 16+, Chrome/Edge 110+, Firefox 110+. iOS Safari es la referencia prioritaria.
- **NFR17:** El sitio debe ser **responsive móvil-first**, dado que se espera ≥70 % del tráfico desde móvil.

#### Accesibilidad

- **NFR18:** El sitio público debe cumplir **WCAG 2.1 nivel AA** como baseline en páginas críticas (home, artículo, formularios de newsletter, páginas legales).

#### Mantenibilidad y calidad de código

- **NFR19:** El código debe estar escrito en **TypeScript estricto**, con linting (ESLint) y formato (Prettier) automatizados, validados en CI.
- **NFR20:** Los flujos críticos (publicación, signup newsletter, gating premium — incluso con flag off — y disclaimer afiliado) deben tener cobertura mínima de **tests unitarios + un test e2e por flujo** (Playwright).

#### Observabilidad

- **NFR21:** El sistema debe registrar logs estructurados de errores y eventos clave (publicación, envío de newsletter, click en afiliado) y exponerlos en una herramienta accesible al dueño (Vercel Logs / Logtail / similar).
- **NFR22:** El sistema debe exponer métricas de tráfico (Plausible o GA4 + Vercel Analytics) y métricas de Core Web Vitals reales (CrUX o RUM) accesibles desde admin o dashboard externo.

#### Editorialidad / operación

- **NFR23:** El flujo "draft → preview → schedule → publish" debe ser ejecutable end-to-end **sin tocar código** durante al menos 30 días consecutivos por parte del dueño como usuario único.
- **NFR24:** El sistema debe permitir mantener cadencia de **≥4 artículos/semana** sin saturar la UX del admin (UX optimizada para creación rápida de noticias con templates).

---

## 3. User Interface Design Goals

> _Esta sección captura la **visión de producto** UX/UI, no la especificación de diseño. Es el insumo para que un UX Expert / Design Architect arme el sistema visual completo, y para que el Architect dimensione adecuadamente la implementación frontend. Donde se marcan **(asunción)**, el dueño debe validar o redirigir._

### 3.1 Overall UX Vision

**El Mate Digital es, ante todo, un lugar para leer.** La experiencia se construye sobre tres principios:

1. **Lectura como ritual** — no consumo apurado. Tipografía editorial generosa, jerarquía clara, espacios blancos respirables, fricción reducida al mínimo entre el usuario y el contenido. El opuesto explícito del feed infinito ansiógeno.
2. **Calidez sobre asepsia** — la metáfora del mate guía el tono visual: cálido, humano, regional, sin caer en lo folklórico ni en clichés tropicales. Apple-inspired en limpieza, pero con personalidad propia (la asepsia clínica de Cupertino se contrasta con un acento cálido).
3. **Densidad informativa controlada** — el lector puede escanear o profundizar a voluntad. Cards de noticia compactas para escaneo rápido; vista de artículo expandida y respirable para lectura comprometida. Los ads se sienten contenidos, no invasores **(asunción: máx. 2 slots above-the-fold + ads in-feed razonables, sin interstitials ni autoplay)**.

La promesa visceral al lector: *"acá puedo enterarme sin ser bombardeado, y puedo profundizar sin tener que abrir 12 pestañas."*

### 3.2 Key Interaction Paradigms

- **Scroll como navegación primaria del artículo:** TOC sticky lateral en desktop / colapsable en móvil. Progress bar sutil arriba.
- **Búsqueda siempre a un click** desde el header en cualquier vista (`Cmd+K` / atajo de teclado **(asunción)**).
- **Navegación taxonómica visible**: menú principal por producto Apple (iPhone, Mac, iPad, Watch, Vision, Servicios) + acceso a tipos de contenido (Noticias, Análisis, Tutoriales, Reseñas, Guías).
- **Suscripción a newsletter contextual y no agresiva**: bloque inline en footer y al final del artículo. Sin popups modales que tapen contenido **(asunción explícita — confirmar)**.
- **Compartir frictionless**: botones nativos (Web Share API en móvil + fallback a X/WhatsApp/copiar enlace en desktop).
- **Toggle claro/oscuro** persistente, con respeto inicial del `prefers-color-scheme` del sistema.
- **Admin con foco en velocidad editorial**: shortcuts de teclado para guardar, programar, publicar; vista de lista con filtros rápidos por estado (draft/scheduled/published); editor con preview en split-view **(asunción)**.

### 3.3 Core Screens and Views

**Sitio público (MVP):**

1. **Home** — hero con artículo destacado editorial + carrusel de destacados secundarios; últimas publicaciones cronológicas; secciones agrupadas por categoría (iPhone, Mac, etc.); CTA newsletter al pie.
2. **Página de artículo** — la vista crítica del producto. Hero con título, autor, fecha, tiempo de lectura, imagen destacada; cuerpo con tipografía editorial; TOC sticky; bloque "lecturas relacionadas"; bloque "suscribite al newsletter"; share frictionless; comentarios DESACTIVADOS en MVP (Fase 2).
3. **Página de categoría** — listado paginado/scroll de artículos de una categoría (ej: `/iphone`), con sub-filtro opcional por tipo de contenido.
4. **Página de tipo de contenido** — listado por tipo transversal (ej: `/analisis`, `/tutoriales`).
5. **Página de tag** — listado por tag libre.
6. **Página de autor** — bio + listado de artículos del autor. En MVP probablemente solo una (el dueño), pero la vista existe.
7. **Resultados de búsqueda** — listado con snippet resaltado del match.
8. **Newsletter — archivo** — listado de ediciones enviadas + suscripción.
9. **Página estática editorial:** `/about`, `/etica-editorial`, `/contacto`.
10. **Páginas legales:** `/politica-de-privacidad`, `/aviso-legal`, `/politica-de-afiliados`.
11. **404 personalizado** — con voz de marca + sugerencias de lectura.

**Panel admin (Payload — MVP):**

12. **Login** — email + password + 2FA TOTP obligatorio.
13. **Dashboard** — métricas básicas (artículos del mes, drafts pendientes, próximos programados); accesos directos a "Nuevo artículo" y "Newsletter".
14. **Lista de artículos** — filtros por estado, categoría, tipo, autor; búsqueda; acciones bulk.
15. **Editor de artículo** — rich editor (Lexical) con embeds, programación, meta-SEO, imagen destacada, flag interno `IA-asistido`, slots de ads on/off por artículo.
16. **Preview de draft** — URL con token, sin auth pública.
17. **Media library** — upload, organización, búsqueda, optimización automática.
18. **Gestión de taxonomía** — categorías, tipos de contenido, tags.
19. **Newsletter** — composición + envío + listado de suscriptores + archivo.
20. **Configuración** — slots de ads globales, feature flag `PREMIUM_ENABLED`, configuración SEO global, integraciones.
21. **Audit log** — actividad de admin (publicaciones, edits, logins).

### 3.4 Accessibility: **WCAG 2.1 nivel AA**

(Ya formalizado en NFR18.) En MVP se garantiza AA en home, artículo, categoría, formularios de newsletter y páginas legales. Posibles mejoras a AAA en Fase 2 si se justifica con audiencia.

### 3.5 Branding

> _La identidad visual completa requiere un branding sprint corto. Estas son **direcciones tentativas** basadas en la metáfora del mate + el posicionamiento Apple — para validar/redirigir._

- **Paleta tentativa:**
  - **Modo claro:** fondo papel cálido (off-white tipo `#FAF8F4`), texto carbón suave (`#1B1B1B`), acento principal **verde mate** (`~#3A6B3A` / verde profundo natural, no neón), acento secundario amarillo/dorado cálido (la calabaza del mate).
  - **Modo oscuro:** fondo carbón profundo (`#121212` / `#1A1A1A`), texto papel suave, acento verde mate claro, acento dorado.
- **Tipografía tentativa:**
  - **Titulares:** serif editorial premium (candidatos: New York / Source Serif Pro / IBM Plex Serif / Charter) — transmite autoridad y lectura.
  - **Cuerpo:** sans-serif neutro y legible (candidatos: Inter / IBM Plex Sans / Söhne).
  - **Código (tutoriales):** mono limpia (JetBrains Mono / IBM Plex Mono).
- **Logo (concept abierto):** o bien wordmark editorial con detalle gráfico sutil (ej: una bombilla estilizada como acento sobre la "i"), o bien icono mate+manzana combinado. Pendiente del branding sprint.
- **Tono y voz visual:** cálido pero profesional, regional sin folklorismo, profesional sin frialdad. Evitar stock-Apple genérico; preferir fotografía propia o ilustración con identidad.
- **Iconografía:** Lucide icons como base **(ya en el stack)**, con posibilidad de set propio para íconos editoriales claves.
- **Tratamiento de imágenes:** imagen destacada de artículo a 16:9 o 3:2; soporte para captions; lazy-loading + AVIF/WebP.

### 3.6 Target Device and Platforms: **Web Responsive (móvil-first)**

- **Forma:** Web responsive (single codebase) optimizada **móvil-first** porque se espera ≥70 % del tráfico en móvil (lectura en transporte, breaks, etc.).
- **Breakpoints tentativos:** móvil (<640px), tablet (640-1024px), desktop (>1024px).
- **PWA:** opcional en MVP — manifest + service worker básico para añadir a home screen e instant loading. Funcionalidades PWA avanzadas (offline reading, push) se difieren a Fase 2 **(asunción confirmable)**.
- **Apps nativas:** explícitamente fuera de alcance (PWA bien hecha cubre el caso de uso).

---

## 4. Technical Assumptions

> _Esta sección define las constraints técnicas que recibirá el Architect. Las decisiones aquí son **constraints**, no sugerencias — cualquier cambio debe re-litigarse con el dueño antes de avanzar a arquitectura._

### 4.1 Repository Structure: **Polyrepo (repo nuevo dedicado)**

- **Decisión:** El proyecto vive en un **repositorio nuevo dedicado** (ej: `el-mate-digital`), **independiente de `aios-core`**.
- **Rationale:** Es una aplicación standalone Next.js + Payload con dominio de problema, ciclo de release y dependencias propias. Empaquetar dentro de `aios-core` (que es un framework de agentes) generaría acoplamiento accidental y deploys cruzados.
- **NO se usa monorepo** porque hay un único deployable (single Next.js app con Payload embebido). No hay packages compartidos que justifiquen Turborepo/Nx en MVP.

### 4.2 Service Architecture: **Monolito modular sobre Next.js + Payload, deployado en serverless de Vercel**

- **Decisión:** Aplicación monolítica única basada en **Next.js 15 (App Router)** con **Payload CMS 3.0 embebido en el mismo proyecto** (comparten DB, server actions y deploy).
- **Estructura de carpetas tentativa** (el Architect cierra el detalle final):
  - `app/(public)/` — rutas públicas (home, artículo, categoría, etc.)
  - `app/(payload)/` — admin de Payload
  - `app/api/` — API routes para webhooks (Stripe, Resend) y endpoints utilitarios
  - `collections/` — definición de collections de Payload (Posts, Categories, Tags, Authors, Plans, Members, Subscriptions, Newsletter, AdSlots, MediaAssets, AuditLogs)
  - `lib/` — utilidades compartidas (Stripe stub, Resend client, helpers de gating, SEO helpers)
  - `components/` — componentes React (UI compartida + público + admin custom)
  - `styles/` — Tailwind config + tokens de diseño
- **Servicios externos vía SDK** (no microservicios propios): Resend, Stripe (stub), Cloudinary, AdSense (client-side script), Plausible, Akismet (Fase 2).
- **Edge functions** para tareas ligeras (redirects, sirviendo ads, A/B simple si se necesita).
- **Rationale:** Stack moderno, costos bajos de operación, deploys atómicos, simplicidad para fundador único. Microservicios sería overengineering para 1-100k pv/mes.

### 4.3 Testing Requirements: **Unit + E2E selectivos (no full pyramid en MVP)**

- **Decisión:** **Unit tests** + **tests E2E selectivos en flujos críticos** (formalizado en NFR20).
- **Stack de testing:**
  - **Unit:** Vitest o Jest (preferencia Vitest por compatibilidad nativa con Next.js + ESM y velocidad).
  - **E2E:** Playwright (al menos un test por flujo crítico: publicar artículo, suscripción newsletter con doble opt-in, click afiliado con redirect, gating premium con flag off, render de artículo con TOC).
  - **Tests de integración** focalizados solo en colecciones de Payload con lógica de negocio compleja (gating, subscription state).
  - **Coverage tooling:** istanbul/c8 con threshold mínimo (TBD por Architect, sugerencia: 60-70% en `lib/` y `collections/`, no perseguir 100%).
- **Manual testing convenience:** sembrar DB de desarrollo con dataset realista (10-20 artículos de ejemplo, categorías, autor demo) vía script `pnpm seed`.
- **Lo que NO se hace en MVP:** Full testing pyramid con tests visuales (Chromatic), property-based testing, mutation testing, load/stress testing. Se evalúa cuando justifique audiencia.

### 4.4 Additional Technical Assumptions and Requests

#### Lenguajes y frameworks core

- **Lenguaje:** **TypeScript estricto** (`strict: true`, sin `any` implícito), Node.js ≥20 LTS.
- **Framework:** **Next.js 15** con App Router (server components por default, client components solo donde sea necesario).
- **Runtime:** mix de Node serverless (Vercel functions) + edge (donde aplique).

#### UI y diseño

- **Estilos:** **Tailwind CSS** + **shadcn/ui** como base de componentes accesibles.
- **Iconos:** **Lucide**.
- **Tipografía:** auto-hospedada en `app/fonts/` o servida vía `next/font` para evitar layout shift.

#### CMS y datos

- **CMS:** **Payload CMS 3.0** embebido. Editor rico Lexical.
- **DB:** **PostgreSQL en Neon** (plan free para desarrollo, paid si tráfico/storage lo exige). Branching gratuito para PRs.
- **ORM:** el que Payload provee (Drizzle bajo el capó en Payload 3.0).
- **Búsqueda MVP:** **PostgreSQL FTS** (tsvector + tsquery, con índices GIN). Evaluar **Meilisearch** en Fase 2 si > 1.000 artículos o queries complejas.

#### Hosting, media e infra

- **Hosting:** **Vercel** (Free/Pro según pricing al lanzar; MVP encaja en Hobby/Pro temprano).
- **Media:** **Cloudinary** como primera opción por optimización on-the-fly, transformaciones y AVIF/WebP automático. Alternativa de fallback: **Vercel Blob** si se prefiere todo bajo un solo proveedor.
- **Email transaccional + newsletter:** **Resend**.
- **DNS y dominio:** **postergado** (MVP corre en URLs `*.vercel.app` para preview y production). Cuando se decida dominio, se conectará vía Vercel DNS.

#### Pagos (preparados, no activos en MVP)

- **SDK Stripe** instalado y configurado en MVP.
- **Webhooks Stripe** declarados pero deshabilitados vía `PREMIUM_ENABLED=false`.
- **Collections** `Plan`, `Subscription`, `Member` creadas en Payload desde MVP.
- **Customer Portal** de Stripe se conecta recién en Fase 2.

#### Analytics y observabilidad

- **Analytics público:** **Plausible** (GDPR-friendly, sin cookies) o GA4 (a decisión final del dueño — preferencia inicial: **Plausible**).
- **Analytics de plataforma:** **Vercel Analytics** + **Vercel Speed Insights** para RUM y Core Web Vitals.
- **Logs:** Vercel Logs en MVP. Evaluar Logtail/Axiom si crece.
- **Error tracking:** **Sentry** (plan free para MVP) — recomendado por Architect aunque no esté en el brief.

#### Seguridad operacional

- **Secrets:** **Vercel Environment Variables** + `.env.local` para dev (nunca commiteado).
- **2FA admin:** TOTP via Payload native auth + `otplib` o plugin existente.
- **CSP, HSTS, headers:** definidos en `next.config.js` + middleware.
- **Rate limiting:** **Upstash Redis** (free tier) o middleware nativo de Next.js para endpoints sensibles.

#### CI/CD

- **CI:** **GitHub Actions** (build + lint + typecheck + unit tests en cada PR; preview deploy automático en Vercel; e2e en main).
- **CD:** Vercel auto-deploy en `main` (production) + previews por branch/PR.
- **Branching:** trunk-based con feature branches cortas y PRs revisados (auto-revisión + CodeRabbit si el dueño quiere).
- **Versioning:** semver semántico en releases (`v0.x` durante MVP, `v1.0` en soft-launch).

#### Política de uso de IA en desarrollo

- **Editorial:** ver FR23/FR24 (asistida + auditable interna).
- **Desarrollo (interna):** uso libre de asistentes de código (Cursor, Copilot, Claude Code) — sin obligación de marcar PRs. Toda la responsabilidad de la calidad recae en el dueño/dev al hacer merge.

---

## 5. Epic List

> _Cada épica entrega un incremento end-to-end deployable y produce valor visible. Las épicas son **secuenciales** — Epic N depende de Epic N-1. Las concerns transversales (logging, tests, a11y, performance) viajan **dentro** de cada épica, no como épica final._

### Visión general (6 épicas)

| # | Épica | Goal (1 línea) | Outcome al cerrar |
|---|---|---|---|
| **1** | **Foundation & Canary** | Establecer el proyecto Next.js + Payload + Neon + Vercel con auth + 2FA + CI/CD y publicar una página "canary" navegable. | Repo listo, deploy verde en Vercel, admin con login + 2FA, home con mensaje de prueba en producción. |
| **2** | **Editorial Core: Posts + Render** | Modelar Posts (Lexical editor, drafts, schedule, publish, versionado) + taxonomía + render público mínimo del home y la página de artículo. | El dueño puede crear, editar y publicar un artículo desde admin y verlo renderizado público en Vercel. |
| **3** | **Public Site UX & SEO** | Completar navegación pública (categoría, tipo, tag, autor, búsqueda, related, share), modo claro/oscuro, sitemap, RSS, schema.org, OG/Twitter, páginas legales y `/about`. | El blog es navegable end-to-end, indexable por Google, y respeta CWV mínimos. Listo para soft-launch de SEO. |
| **4** | **Newsletter** | Integrar Resend con flujo de doble opt-in, formularios contextuales (sidebar, footer, fin de artículo), archivo público de newsletters y composición/envío desde admin. | Suscriptores pueden registrarse, confirmar, recibir y consultar archivo. El dueño puede componer y enviar una edición. |
| **5** | **Monetization Layer** | Slots de AdSense gestionables, sistema de afiliados Amazon con shortlinks + tracking + disclaimer automático, e infraestructura premium pre-instalada (`Plan`/`Subscription`/`Member` + Stripe SDK + `PREMIUM_ENABLED=false`). | Ingresos AdSense + afiliados activos en MVP; premium queda lista para encender en Fase 2 sin migración. |
| **6** | **Editorial Hardening & Launch Polish** | Página `/etica-editorial`, flag interno `IA-asistido` por artículo, audit log, media library final, dashboard de admin, hardening de seguridad (CSP, rate-limiting), tuning final de performance/a11y/observabilidad y seed de contenido. | Blog listo para soft-launch real, con políticas publicadas, hardening operacional y los flujos críticos cubiertos por tests E2E. |

### Detalle del razonamiento de la secuencia

- **Epic 1** establece la fundación obligatoria del template (infra + CI + auth) y ya despliega algo visible (canary). Sin esto el resto no puede arrancar.
- **Epic 2** es el corazón del producto: si esto no funciona, no hay blog. Se hace temprano para validar que el stack Next + Payload funciona end-to-end con un caso real (crear, programar, publicar, renderizar).
- **Epic 3** transforma "tengo un artículo publicado" en "tengo un blog usable y buscable". Es la condición previa para empezar a generar tráfico orgánico — debe estar antes de monetización porque sin tráfico la monetización no rinde.
- **Epic 4** llega antes que Epic 5 porque la newsletter es **un activo propio** (canal directo con el lector) y refuerza retención desde el primer visitante. Cada día sin newsletter es un día de tráfico que se pierde sin capturar.
- **Epic 5** entra cuando ya hay tráfico que monetizar y newsletter que sostiene retorno. AdSense además requiere un sitio con contenido publicado antes de aprobar la cuenta — empujarlo más temprano sería contraproducente.
- **Epic 6** consolida el "todo listo para mostrar al mundo": políticas formales, hardening, observabilidad, seed final. Es deliberadamente lo último porque depende de tener los flujos cerrados para hardenearlos sin reescribir.

### Cross-cutting concerns (presentes en TODAS las épicas, no como épica separada)

| Concern | Tratamiento |
|---|---|
| **Logging estructurado** | Setup mínimo en Epic 1; cada épica agrega los eventos clave de su scope. |
| **Tests (unit + e2e)** | Cada story incluye sus tests críticos; no se difieren a una épica final. |
| **Accesibilidad WCAG AA** | Aplicada desde Epic 1 (componentes base) y validada por épica. |
| **Performance (CWV)** | Optimización incremental: Epic 1 setup, Epic 2-3 optimizan render, Epic 6 hace tuning final. |
| **Seguridad baseline** | HTTPS + 2FA + headers básicos desde Epic 1; CSP estricta + rate-limiting + audit en Epic 6. |
| **Documentación operativa** | Cada épica deja README/runbook actualizado del scope que tocó. |

### Cortes alternativos considerados (descartados)

- **5 épicas (mergeando Epic 5 + Epic 6):** rechazado — Epic 6 contiene hardening que necesita haber probado monetización con tráfico simulado primero.
- **7 épicas (separando Premium infra como épica propia):** rechazado — premium en MVP es solo infra/collections, no justifica una épica entera, encaja bien dentro de Epic 5.
- **Split Epic 2 en "Posts admin" + "Posts render":** rechazado — rompe el principio de vertical slice (cada épica debe ser deployable y entregar valor de punta a punta).

---

## 6. Epic Details

> _Cada épica se documenta con su **goal expandido**, **stories secuenciales** y **acceptance criteria** verificables. Stories son **vertical slices** sized para una sesión enfocada de dev (~2-4h de trabajo focused), no enabler-only ni waterfall. Las stories dentro de una épica respetan dependencias internas. El detalle exhaustivo de cada story se delega al SM cuando armemos los sprints._

### Epic 1 — Foundation & Canary

**Expanded Goal:** Establecer el esqueleto completo del proyecto Next.js + Payload + Neon + Vercel con autenticación + 2FA, pipeline CI/CD y observabilidad mínima, dejando publicada una página "canary" navegable en producción que valide que todo el stack funciona end-to-end. Al cerrar esta épica el dueño puede loguearse al admin con 2FA, ver una página pública, y el repositorio tiene CI verde con preview deploys automáticos.

#### Story 1.1 — Scaffold Next.js 15 + TypeScript estricto + Tailwind

**As a** dueño-dev,
**I want** un proyecto Next.js 15 con App Router, TypeScript estricto, Tailwind CSS, shadcn/ui y Lucide ya configurados desde cero,
**so that** tengo una base limpia y consistente sobre la cual construir el resto del producto sin reconfigurar boilerplate.

**Acceptance Criteria:**

1. Repo nuevo creado (nombre tentativo `el-mate-digital`) con README mínimo, `.gitignore`, `LICENSE` y estructura de carpetas inicial.
2. `package.json` declara Next.js 15.x, React 19.x, TypeScript ≥5.x, Tailwind ≥3.4, ESLint + Prettier.
3. `tsconfig.json` con `"strict": true`, `"noImplicitAny": true`, paths aliases (`@/*` → `./src/*` o equivalente acordado).
4. `pnpm dev` (o `npm run dev`) levanta el servidor local en `http://localhost:3000` y muestra la página default de Next.js.
5. shadcn/ui inicializado (CLI `init`) con el esquema de colores base; al menos un componente base instalado (Button) y renderizable.
6. Lucide instalado y un ícono renderizable como smoke test.
7. ESLint + Prettier ejecutables (`pnpm lint`, `pnpm format`) sin errores en el scaffold limpio.
8. Husky + lint-staged opcional (puede diferirse a una story posterior).

#### Story 1.2 — Instalar y configurar Payload CMS 3.0 embebido en Next.js

**As a** dueño-dev,
**I want** Payload CMS 3.0 instalado dentro del mismo proyecto Next.js compartiendo el runtime,
**so that** el admin y el sitio público comparten un solo deployment sin servicios separados.

**Acceptance Criteria:**

1. Payload CMS 3.0 instalado siguiendo la guía oficial de integración con Next.js App Router.
2. Estructura de carpetas creada: `src/collections/` (vacía aún), `src/payload.config.ts`.
3. Configuración mínima de Payload sin collections de negocio (sólo `Users` por default).
4. Ruta `/admin` levanta el panel de Payload (sin datos persistidos aún — se acepta SQLite local o estado en memoria temporal hasta Story 1.3).
5. Build local (`pnpm build`) compila sin warnings críticos.

#### Story 1.3 — Provisionar PostgreSQL en Neon y conectar Payload

**As a** dueño-dev,
**I want** una base de datos PostgreSQL gestionada en Neon conectada a Payload vía el adapter oficial,
**so that** el contenido persiste de forma confiable y puedo branchearla para PRs.

**Acceptance Criteria:**

1. Cuenta Neon creada, proyecto provisionado en una región cercana al público target (us-east o sa-east).
2. Variable `DATABASE_URL` configurada en `.env.local` (dev) y en Vercel (preview/production) — nunca commiteada.
3. Payload usa `@payloadcms/db-postgres` (Drizzle) apuntando a Neon.
4. Migraciones iniciales de Payload corren correctamente (`pnpm payload migrate`).
5. Usuario admin inicial seedeado vía env var o flujo de primer login.
6. Se documenta en README el flujo para crear un branch de DB Neon por feature (workflow básico).

#### Story 1.4 — Pipeline CI con GitHub Actions

**As a** dueño-dev,
**I want** un pipeline de CI que valide lint, typecheck y tests en cada PR,
**so that** ningún cambio se mergea sin pasar las gates de calidad mínimas.

**Acceptance Criteria:**

1. Workflow `.github/workflows/ci.yml` se ejecuta en `pull_request` y `push` a `main`.
2. Steps incluyen: install (con cache de `pnpm`), `pnpm lint`, `pnpm typecheck`, `pnpm test` (placeholder Vitest si aún no hay tests reales — devuelve 0 si no encuentra archivos).
3. CI corre en Node ≥20 LTS.
4. Status checks de "lint", "typecheck" y "test" exigidos para merge a `main` (branch protection rule).
5. Documentado en README cómo correr los mismos checks localmente.

#### Story 1.5 — Deploy automático en Vercel con preview por PR

**As a** dueño-dev,
**I want** que cada PR genere un preview deploy en Vercel automáticamente y que `main` deploye a producción,
**so that** puedo validar cambios visualmente antes de mergear y publicar en producción sin esfuerzo manual.

**Acceptance Criteria:**

1. Proyecto conectado a Vercel via GitHub integration (no manual CLI deploys).
2. Variables `DATABASE_URL`, `PAYLOAD_SECRET` y demás secrets configuradas en Vercel para los entornos preview + production.
3. Cada PR genera un preview URL `*.vercel.app` accesible y comentado en el PR por el bot de Vercel.
4. Merge a `main` triggerea deploy a producción automáticamente.
5. URL de producción `*.vercel.app` accesible públicamente con HTTPS forzado.
6. Documentado en README cómo añadir nuevas environment vars en Vercel.

#### Story 1.6 — Autenticación admin con 2FA TOTP obligatorio

**As a** dueño-administrador,
**I want** loguearme al panel admin con email + contraseña + un segundo factor TOTP obligatorio,
**so that** mi acceso al CMS está protegido aunque alguien obtenga mi contraseña.

**Acceptance Criteria:**

1. El usuario admin inicial puede loguearse en `/admin` con email + contraseña.
2. En el primer login, el sistema obliga a enrolar un dispositivo TOTP (Google Authenticator, 1Password, Authy) mostrando un QR.
3. Logins subsecuentes piden el código TOTP de 6 dígitos.
4. Códigos de respaldo (backup codes) generados al enrolar y mostrados una sola vez para guardar.
5. Posibilidad de regenerar backup codes desde el perfil del usuario.
6. Rate-limiting en login (máx 5 intentos fallidos por IP / 15 min) implementado.
7. Sesiones expiran tras 12h de inactividad (configurable).

#### Story 1.7 — Tokens de diseño y modo claro/oscuro base

**As a** lector del sitio,
**I want** ver una experiencia visual consistente con paleta cálida + tipografía editorial y poder alternar entre modo claro y oscuro,
**so that** la lectura es cómoda según mis preferencias y la del sistema.

**Acceptance Criteria:**

1. Sistema de tokens CSS implementado vía Tailwind (`--color-bg`, `--color-fg`, `--color-accent`, etc.) referenciados desde la paleta tentativa de la Sección 3.5.
2. Modo oscuro implementado vía `dark:` de Tailwind, alternable mediante un toggle en el header.
3. Toggle respeta `prefers-color-scheme` en primera visita y persiste elección del usuario en `localStorage`.
4. Tipografías cargadas vía `next/font` (Inter + IBM Plex Serif u opciones equivalentes — el branding sprint puede sustituir luego).
5. Sin FOUC (Flash of Unstyled Content) al cambiar tema.
6. Tokens documentados en `docs/design-tokens.md` para futuro reuso.

#### Story 1.8 — Página canary pública "El Mate Digital — Próximamente"

**As a** dueño,
**I want** una página pública con branding mínimo y mensaje de "próximamente" desplegada en producción,
**so that** valido que toda la cadena (Next + Payload + Neon + Vercel) funciona end-to-end y puedo compartir un URL real con cualquiera.

**Acceptance Criteria:**

1. Página `/` (home) renderiza un hero con título "El Mate Digital", subtítulo y mensaje "Próximamente — un blog Apple para hispanohablantes".
2. Renderiza correctamente en modo claro y oscuro respetando los tokens de la Story 1.7.
3. Incluye `<meta>` básicos (title, description) — los advanced Open Graph llegarán en Epic 3.
4. Lighthouse mobile ≥ 90 en Performance, SEO y Accessibility para esta página (validado en el preview de Vercel).
5. Página accesible públicamente desde el URL de Vercel de producción con HTTPS.
6. README del proyecto enlaza al URL canary de producción.

#### Story 1.9 — Observabilidad baseline (logging estructurado + Sentry + Speed Insights)

**As a** dueño-dev,
**I want** logs estructurados, captura de errores con Sentry y métricas reales de Core Web Vitals desde el día 1,
**so that** detecto problemas en producción sin depender de reportes manuales y entiendo la performance real de los usuarios.

**Acceptance Criteria:**

1. Logger estructurado configurado (Pino o equivalente) emitiendo JSON en server-side; consumido por Vercel Logs.
2. Sentry instalado con DSN por env var; captura excepciones server y client.
3. Un error provocado en dev (botón temporal de "throw") aparece en el dashboard Sentry.
4. Vercel Speed Insights habilitado en el proyecto; reporta LCP/INP/CLS reales en el dashboard.
5. Configuración de Sentry tiene sample rate razonable para MVP (100% errores, 10% transactions en preview/prod).
6. Documentación de cómo añadir nuevos events / breadcrumbs en el código.

---

#### Notas operativas de Epic 1

- **Dependencias entre stories:** 1.1 → 1.2 → 1.3 → (1.4 ∥ 1.5 ∥ 1.6) → (1.7 ∥ 1.8 ∥ 1.9). Las stories paralelas se pueden ejecutar en cualquier orden tras tener DB+Auth.
- **Stories candidatas a fusionarse si se priorizara velocidad:** 1.4 + 1.5 (CI + Vercel deploy) podrían convertirse en una sola "CI/CD pipeline" — se las dejó separadas para granularidad de scope.
- **Stories candidatas a partirse si crecen:** 1.6 (2FA) puede sub-partirse en "auth básica" + "TOTP + backup codes" si la implementación se complica más allá de 4h.
- **Riesgos abiertos de Epic 1:** ningún branding-sprint cerrado todavía — la paleta y tipografías de la Story 1.7 son placeholders, sustituibles cuando el branding cierre sin romper componentes.

---

### Epic 2 — Editorial Core: Posts + Render

**Expanded Goal:** Construir el corazón editorial del producto: modelar todas las collections necesarias para publicar contenido (Posts, Categories, Content Types, Tags, Authors, Media), implementar el editor rich-text Lexical con embeds, soportar el ciclo `draft → scheduled → published` con job de publicación programada, y renderizar públicamente el home con los últimos artículos y la página de artículo individual. Al cerrar esta épica, el dueño puede crear, programar y publicar un artículo desde el admin y verlo renderizado en producción con su imagen, autor y categoría.

#### Story 2.1 — Collections de taxonomía (Categories + ContentTypes + Tags)

**As a** dueño-editor,
**I want** un sistema de taxonomía con categorías por producto Apple, tipos de contenido y tags libres,
**so that** puedo clasificar consistentemente cada artículo y los lectores pueden navegar por afinidad temática.

**Acceptance Criteria:**

1. Collection `Categories` creada en Payload con campos: `name` (text, requerido), `slug` (text, único, auto-generado desde `name` pero editable), `description` (text), `order` (number, para ordenar visualmente).
2. Collection `ContentTypes` creada con los mismos campos base; seedeada con los 6 tipos iniciales (noticia, rumor, análisis, tutorial, reseña, guía de compra).
3. Collection `Tags` creada con `name`, `slug`. Tags se pueden crear inline desde el editor de Post.
4. Categorías iniciales seedeadas: iPhone, Mac, iPad, Watch, Vision, Servicios (FR5).
5. CRUD completo accesible desde admin sin tocar código (FR6).
6. Slugs auto-generados con `slugify` (lowercase, sin acentos, kebab-case), validados como únicos.
7. Borrado de una categoría/tipo en uso debe estar bloqueado o pedir confirmación de "qué hacer con los posts asociados".

#### Story 2.2 — Collection Authors

**As a** dueño-editor,
**I want** modelar autores como entidad propia con bio, avatar y links sociales,
**so that** cada artículo puede atribuirse correctamente y la página de autor exhibe la firma editorial.

**Acceptance Criteria:**

1. Collection `Authors` creada con: `name` (text, requerido), `slug` (text, único), `bio` (richText/textarea), `avatar` (upload), `email` (text, opcional, privado), `socialLinks` (array de `{platform, url}`).
2. Author inicial seedeado (el dueño) con datos placeholder.
3. CRUD completo desde admin.
4. Relación 1-N preparada para asociar a Posts (definida formalmente en Story 2.4).
5. Borrado de un autor con posts asociados está bloqueado o requiere reasignar.

#### Story 2.3 — Media library con upload e integración Cloudinary

**As a** dueño-editor,
**I want** subir imágenes desde el admin y que se sirvan optimizadas automáticamente en AVIF/WebP responsive,
**so that** los artículos cargan rápido sin que yo tenga que pre-procesar cada imagen manualmente.

**Acceptance Criteria:**

1. Collection `Media` configurada en Payload con storage en Cloudinary (vía adapter oficial o plugin equivalente).
2. Upload desde admin acepta JPG, PNG, WebP, AVIF, GIF (≤10 MB inicial).
3. Cloudinary genera derivados automáticos (resized + AVIF/WebP) que se referencian desde `next/image`.
4. Campos editoriales por asset: `alt` (text, requerido para a11y), `caption` (text, opcional), `credit` (text, opcional para fotografías de terceros).
5. Búsqueda por filename o alt-text en la media library del admin.
6. URLs de Cloudinary con `f_auto,q_auto` (auto-format + auto-quality).
7. Logs estructurados de cada upload (quién, qué, cuándo) — feed a Story 1.9.

#### Story 2.4 — Collection Posts (estructura, ciclo de estados, relaciones, SEO básico)

**As a** dueño-editor,
**I want** una collection Posts completa que soporte borradores, programación y publicación con todos los campos editoriales y de SEO,
**so that** tengo el modelo de datos completo para producir contenido sin limitaciones.

**Acceptance Criteria:**

1. Collection `Posts` creada con campos: `title` (text, requerido), `slug` (text, único, auto-generado pero editable), `excerpt` (text), `body` (richText Lexical — configurado en Story 2.5), `featuredImage` (relación a `Media`, requerida para `published`), `author` (relación a `Authors`, requerida), `category` (relación a `Categories`, requerida), `contentType` (relación a `ContentTypes`, requerida), `tags` (relación many a `Tags`), `status` (select: `draft`/`scheduled`/`published`/`archived`), `publishedAt` (date — para `published` y `scheduled`), `metaTitle` (text), `metaDescription` (text), `aiAssistedFlag` (checkbox interno — FR24), `updatedAt` (auto).
2. Versionado habilitado en Payload (`versions: { drafts: true }`); permite ver historial y revertir a versión previa.
3. Validación: para pasar a `published` o `scheduled`, los campos requeridos (`title`, `slug`, `body`, `featuredImage`, `author`, `category`, `contentType`, `publishedAt`) deben estar completos.
4. Para `scheduled`, `publishedAt` debe ser una fecha futura.
5. Slug auto-generado evergreen (sin fecha), editable manualmente, validado único.
6. UI del admin agrupa campos en pestañas: "Contenido" (title, slug, excerpt, body, featuredImage), "Taxonomía" (category, contentType, tags, author), "SEO" (metaTitle, metaDescription), "Publicación" (status, publishedAt, aiAssistedFlag).
7. Acción "Publicar ahora" desde admin (cambia status a `published` y setea `publishedAt = now`).

#### Story 2.5 — Editor Lexical con embeds (imagen, YouTube, X/tweet, code block)

**As a** dueño-editor,
**I want** un editor rico Lexical que me permita insertar imágenes, embeds de YouTube y X, bloques de código con highlighting y formatear texto con titulares, listas, citas y enlaces,
**so that** puedo producir artículos visualmente ricos sin escribir HTML ni Markdown a mano.

**Acceptance Criteria:**

1. Editor Lexical configurado en el field `body` de `Posts`.
2. Bloques disponibles: headings H2/H3/H4, párrafo, lista no/ordenada, blockquote, link, inline-code, separator (HR).
3. Bloque "Imagen" con selector de `Media` (relación, no upload inline) + caption + alt.
4. Bloque "Embed YouTube" que acepta URL `youtu.be` o `youtube.com/watch`, parsea ID y renderiza iframe responsive (16:9, lazy-loaded).
5. Bloque "Embed X" que acepta URL de tweet y renderiza via blockquote oficial de X (con fallback elegante si X bloquea el embed).
6. Bloque "Code" con language selector (txt, js, ts, jsx, tsx, html, css, bash, json, sql) y syntax highlighting en el render público (Shiki o similar).
7. Bloque "Aside / Callout" (cita/destacado) con variantes: info, warning, tip.
8. Toolbar accesible (etiquetas, keyboard shortcuts típicos: Cmd+B negrita, Cmd+I cursiva, Cmd+K link).
9. Sanitización: el output no permite `<script>`, atributos `on*=`, ni styles inline arbitrarios.

#### Story 2.6 — Job de publicación programada

**As a** dueño-editor,
**I want** programar la publicación de un artículo para una fecha y hora exacta y que el sistema lo publique automáticamente sin que yo tenga que estar online,
**so that** puedo trabajar en bloques de tiempo independientes del momento de publicación.

**Acceptance Criteria:**

1. Job recurrente configurado (Vercel Cron Job o equivalente, ejecución cada 5 min).
2. El job consulta posts con `status='scheduled'` y `publishedAt <= now()`, los actualiza a `status='published'` y deja `publishedAt` intacto.
3. Idempotencia: si el job corre dos veces sobre el mismo registro, no duplica nada.
4. Si la publicación falla (DB caída, etc.), el job logs el error a Sentry y reintenta en el siguiente ciclo.
5. El admin muestra una indicación visual cuando un post está `scheduled` (badge + fecha de publicación próxima).
6. Tests unitarios cubren: scheduled-en-pasado se publica; scheduled-en-futuro no se toca; published no se reprocesa.
7. Documentación operativa: cómo verificar manualmente que el cron está corriendo en Vercel.

#### Story 2.7 — Preview de drafts con URL pública + token expirante

**As a** dueño-editor,
**I want** generar un URL único para previsualizar un borrador como si estuviera publicado, sin tener que loguearme,
**so that** puedo compartir previews con colaboradores o revisarlo en otro dispositivo sin exponer el admin.

**Acceptance Criteria:**

1. Endpoint `/preview/[postId]?token=...` que renderiza un post en estado `draft` o `scheduled` con el mismo layout que el público.
2. Token generado al hacer click en "Generar URL de preview" en el admin del post.
3. Token TTL: 24h (configurable).
4. Token de un solo post (no permite acceder a otros posts).
5. Preview marcado visualmente como "PREVIEW — no publicado" (banner top sticky o similar).
6. Rate-limiting en el endpoint para evitar enumeración (máx 60 req/hora por IP).
7. Token revocable manualmente desde admin.

#### Story 2.8 — Render público del home: hero + últimos artículos

**As a** lector,
**I want** entrar a `el-mate-digital.vercel.app` y ver el último artículo destacado en un hero + un listado de las últimas publicaciones,
**so that** entiendo de qué va el blog y puedo entrar a leer.

**Acceptance Criteria:**

1. Ruta `/` (home) renderiza con ISR (revalidate cada 60 s o on-demand on publish).
2. Hero: el post `published` más reciente, con featured image, título, excerpt, autor, categoría, fecha, link al artículo.
3. Grid/list de los siguientes 10-12 posts publicados (cards compactas con featured image small, título, categoría, fecha).
4. Soporte de "destacado editorial-curado": campo opcional `isFeatured` en Posts que prioriza ese post como hero por sobre el más reciente (preparación para Epic 3, podría implementarse acá si trivial).
5. Modo claro y oscuro funcionando.
6. Lighthouse mobile ≥ 90 (Performance, SEO, Accessibility).
7. Cards usan `next/image` para imágenes con sizes correctos.
8. Estados vacíos: si no hay posts publicados, el hero muestra "Próximamente: primer artículo en camino".

#### Story 2.9 — Render público de la página de artículo

**As a** lector,
**I want** abrir un artículo y leerlo cómodamente con tipografía editorial, imagen destacada, autoría visible y embeds funcionando,
**so that** disfruto la lectura y entiendo el contexto del artículo.

**Acceptance Criteria:**

1. Ruta `/articulo/[slug]` (o `/post/[slug]` — confirmar convención) sirve la página de artículo desde ISR.
2. Layout: hero con título grande, subtítulo (excerpt), featured image, meta-info (autor + avatar + fecha + tiempo de lectura).
3. Cuerpo renderiza el contenido Lexical aplicando todos los bloques de la Story 2.5 (incluido code highlighting, embeds YouTube/X, callouts).
4. Tiempo de lectura calculado automáticamente desde la cuenta de palabras del body.
5. Botones de compartir: copiar enlace, X, WhatsApp, Facebook (Web Share API en móvil con fallback en desktop).
6. Modo claro y oscuro respetan los tokens de la Story 1.7.
7. SEO básico: title = `${post.metaTitle || post.title} — El Mate Digital`, description = `${post.metaDescription || post.excerpt}` (el SEO avanzado con OG/Twitter/schema llega en Epic 3).
8. 404 personalizado si el slug no existe o el post está en `draft`/`archived`.
9. Lighthouse mobile ≥ 90 en una página de artículo real con imagen + embed.
10. TOC, lecturas relacionadas, share extendido y schema.org se difieren a Epic 3 (mencionado explícitamente para no atascar este story).

---

#### Notas operativas de Epic 2

- **Dependencias entre stories:** 2.1 (taxonomía) y 2.2 (Authors) y 2.3 (Media) son prerequisitos de 2.4 (Posts collection). 2.5 (editor) depende de 2.4. 2.6 (job) y 2.7 (preview) dependen de 2.4. 2.8 y 2.9 (renders) dependen de 2.4 y 2.5.
- **Stories paralelizables si hay más de un dev:** 2.1 ∥ 2.2 ∥ 2.3, y luego 2.6 ∥ 2.7 ∥ 2.5 una vez 2.4 listo.
- **Stories candidatas a sub-split:** 2.4 (Posts collection completa) es la más pesada — si supera 4h, partir en "2.4a Posts schema + status" + "2.4b SEO fields + admin UI tabs".
- **Convención de URL de artículo:** propongo `/articulo/[slug]` (es-LATAM neutral) en vez de `/post/[slug]` (anglo) o `/blog/[slug]` (genérico). **Confirmar en este checkpoint.**
- **`aiAssistedFlag` (FR24) implementado pero invisible al lector:** sólo metadato interno auditable por dueño.
- **Lo que NO entra en Epic 2:** TOC, lecturas relacionadas, RSS, sitemap, OG/Twitter, schema.org, búsqueda, autor page, categoría page, tag page — todo eso es Epic 3.

---

### Epic 3 — Public Site UX & SEO

**Expanded Goal:** Convertir el "MVP de artículo aislado" en un blog navegable, descubrible e indexable end-to-end. Completar header/footer con navegación taxonómica, páginas de listado (categoría, tipo, tag, autor), búsqueda con Postgres FTS, mejoras de UX en la página de artículo (TOC, related, share), SEO técnico completo (meta + OG + Twitter Cards + JSON-LD schema.org + sitemap + robots + canonical), feeds RSS, y publicar las páginas estáticas obligatorias (`/about`, `/contacto`, páginas legales y `/etica-editorial` con placeholder editorial). Al cerrar esta épica el blog está listo para empezar a generar tráfico orgánico real.

#### URL conventions (formalizadas en este Epic)

Patrón asumido (default sin objeción del dueño en el checkpoint de Epic 2):

- Artículo individual: `/articulo/[slug]`
- Categoría: `/categoria/[slug]` (ej. `/categoria/iphone`)
- Tipo de contenido: `/tipo/[slug]` (ej. `/tipo/analisis`)
- Tag: `/tag/[slug]`
- Autor: `/autor/[slug]`
- Búsqueda: `/buscar?q=...`
- Páginas estáticas: `/about`, `/contacto`, `/etica-editorial`, `/politica-de-privacidad`, `/aviso-legal`, `/politica-de-afiliados`
- Newsletter (archivo): `/newsletter` y `/newsletter/[slug]` (se cierra en Epic 4)

Estos paths se confirman al inicio del Epic 3; cambiarlos después implica trabajo de redirects (301) y actualización de sitemap.

#### Story 3.1 — Header + footer + navegación móvil

**As a** lector,
**I want** un header consistente con navegación por categoría y un menú móvil claro, más un footer con links útiles,
**so that** puedo desplazarme por el blog en cualquier dispositivo sin perderme.

**Acceptance Criteria:**

1. Header sticky con: logo (placeholder), navegación principal por categoría Apple (iPhone, Mac, iPad, Watch, Vision, Servicios), enlace a "Análisis", "Tutoriales", "Newsletter", botón de búsqueda (abre overlay/modal), toggle claro/oscuro.
2. En móvil: hamburger menu colapsable con la misma jerarquía + atajo al footer.
3. Footer con: links a páginas estáticas (`/about`, `/etica-editorial`, `/politica-de-privacidad`, `/aviso-legal`, `/politica-de-afiliados`, `/contacto`), bloque de suscripción a newsletter (preparado, formulario real llega en Epic 4 como upgrade), copyright, año actual.
4. Navegación construida desde las collections `Categories` y `ContentTypes` (no hardcoded — si se agrega una categoría desde admin, aparece automáticamente).
5. Estado activo visible (current section resaltada).
6. Cumple WCAG AA: keyboard navegable, ARIA labels, focus visible.

#### Story 3.2 — Páginas de listado por taxonomía (categoría + tipo + tag)

**As a** lector,
**I want** ver el listado paginado de artículos de una categoría, tipo o tag,
**so that** puedo profundizar en un tema específico.

**Acceptance Criteria:**

1. Componente reutilizable `TermListingPage` que recibe el término (categoría/tipo/tag), su descripción y la query de posts asociados.
2. Rutas implementadas: `/categoria/[slug]`, `/tipo/[slug]`, `/tag/[slug]`.
3. Header de la página: nombre del término + descripción + total de artículos.
4. Listado paginado (12 por página) con cards (featured image, título, excerpt, categoría, fecha, autor avatar pequeño).
5. Paginación con `?page=N` (compatible con crawlers) o scroll infinito con fallback paginado SSR.
6. ISR con revalidate adecuado (60-300 s) o on-demand on publish.
7. Estado vacío: si no hay artículos publicados aún, mensaje "Pronto habrá contenido en esta categoría".
8. SEO básico por listado: `title`, `description`, canonical (los OG/JSON-LD se completan en Story 3.6).
9. Slug inválido → 404 personalizado.

#### Story 3.3 — Página de autor (`/autor/[slug]`)

**As a** lector,
**I want** ver la página de un autor con su bio, avatar y listado de sus artículos,
**so that** entiendo quién firma el contenido y confío en su autoridad editorial.

**Acceptance Criteria:**

1. Ruta `/autor/[slug]` renderiza header con avatar grande, nombre, bio, links sociales.
2. Listado de artículos del autor paginado (12 por página).
3. Reutiliza el componente de cards del Story 3.2.
4. Estado vacío si el autor no tiene artículos publicados aún.
5. SEO básico (Person schema se agrega en Story 3.6).
6. Slug inválido → 404.

#### Story 3.4 — Mejoras de la página de artículo: TOC + relacionados + share extendido

**As a** lector,
**I want** una tabla de contenidos navegable, lista de lecturas relacionadas y opciones de compartir extendidas en la página de artículo,
**so that** puedo orientarme dentro de artículos largos y descubrir más contenido relevante.

**Acceptance Criteria:**

1. TOC auto-generada a partir de los headings H2/H3 del body Lexical. Sticky en desktop (sidebar), colapsable en móvil (botón flotante o accordion).
2. Resaltado del heading activo según scroll (scroll-spy).
3. Bloque "Lecturas relacionadas" al final del artículo: 3-4 posts elegidos por relevancia (misma categoría + misma etiqueta), excluyendo el actual; cards uniformes.
4. Share extendido: botones para X, WhatsApp, Facebook, copiar enlace (con feedback visual al copiar).
5. Web Share API en móvil con fallback en desktop (el mismo de Story 2.9, pero ahora con todas las redes).
6. Progress bar de lectura sutil en el top (ya implementable, opcional refinable).
7. A11y: skip-links, ARIA en TOC, labels en botones share.

#### Story 3.5 — Búsqueda con Postgres FTS

**As a** lector,
**I want** una búsqueda por texto libre que devuelva artículos relevantes con snippet del match,
**so that** puedo encontrar contenido específico sin depender solo de Google.

**Acceptance Criteria:**

1. Endpoint API server-side `/api/search?q=...` que consulta Postgres FTS (`tsvector`/`tsquery`) sobre `title`, `excerpt` y `body` de posts `published`.
2. Soporte para acentos y plurales básicos (configuración `spanish` de Postgres).
3. Indices GIN creados en migración Payload (o script SQL adyacente).
4. Ranking por relevancia (`ts_rank`).
5. Snippet con highlight del término en los resultados (max 200 chars contextuales).
6. Página `/buscar?q=...` muestra resultados paginados (12 por página) o "Sin resultados para '...'".
7. Overlay de búsqueda accesible desde el header (Story 3.1) con sugerencias simples (últimas búsquedas almacenadas en `localStorage`, opcional MVP).
8. Rate-limiting en `/api/search` (60 req/min por IP).
9. Logs de queries (anonimizadas) para entender qué buscan los usuarios.

#### Story 3.6 — SEO técnico avanzado: meta + Open Graph + Twitter Cards + JSON-LD schema.org

**As a** dueño,
**I want** que cada página y artículo tenga meta-tags completos, OG/Twitter Cards bien formados y JSON-LD schema.org (Article, BreadcrumbList, Person, Organization),
**so that** Google, Bing, X y previews en WhatsApp/Slack rendericen el contenido correctamente y los snippets de búsqueda sean ricos.

**Acceptance Criteria:**

1. En cada ruta pública, `<head>` incluye: `<title>`, `<meta name="description">`, `<link rel="canonical">`, `<meta property="og:*">` (title, description, image, type, url, site_name), `<meta name="twitter:*">` (card, title, description, image, site).
2. JSON-LD en home: `WebSite` + `Organization`.
3. JSON-LD en artículo: `Article` (con `author` linked a `Person`, `publisher` a `Organization`, `image`, `datePublished`, `dateModified`) + `BreadcrumbList`.
4. JSON-LD en categoría/tipo/tag/autor: `CollectionPage` + `BreadcrumbList`.
5. Open Graph image por artículo: usa `featuredImage`; fallback a OG image global.
6. Validar con [Rich Results Test de Google](https://search.google.com/test/rich-results) y [Twitter Card Validator] (o equivalente actual) en al menos una página de cada tipo.
7. Configuración global de SEO accesible desde admin: site name, default description, default OG image, Twitter handle.

#### Story 3.7 — sitemap.xml dinámico + robots.txt + canonical

**As a** dueño,
**I want** un sitemap XML actualizado automáticamente y un robots.txt correcto,
**so that** los buscadores indexan todo el contenido publicable y nada más.

**Acceptance Criteria:**

1. `/sitemap.xml` dinámico generado en build/ISR, incluye: home, todos los artículos `published`, todas las páginas de categoría/tipo/tag/autor con al menos un post, páginas estáticas.
2. Cada `<url>` incluye `<lastmod>` (basado en `updatedAt` del post o página).
3. `/robots.txt` permite indexar el público y bloquea `/admin/*`, `/preview/*`, `/api/*` (excepto `/api/sitemap` si aplica).
4. Canonical URLs apuntando al dominio canónico (en MVP: el `*.vercel.app` de producción).
5. Sitemap dividido si supera 50k URLs (no aplica en MVP pero queda preparado).

#### Story 3.8 — RSS feeds (global + por categoría)

**As a** lector power-user,
**I want** suscribirme al blog vía RSS (global o por categoría),
**so that** puedo leerlo desde Reeder/Feedly sin pasar por el sitio.

**Acceptance Criteria:**

1. Feed global en `/rss.xml` con los últimos 50 posts `published`.
2. Feed por categoría en `/categoria/[slug]/rss.xml` (los últimos 50 de esa categoría).
3. Cada item del feed incluye: title, link, description (excerpt), author, pubDate, category, content:encoded (HTML del body sanitizado).
4. Feed validado contra [W3C Feed Validator] al cerrar la story.
5. Header HTML del sitio anuncia el feed via `<link rel="alternate" type="application/rss+xml" href="/rss.xml">`.
6. Cache HTTP del feed con `Cache-Control: max-age=3600` (1h).

#### Story 3.9 — StaticPages collection + páginas estáticas obligatorias

**As a** dueño-editor,
**I want** una collection `StaticPages` para crear y editar las páginas legales y editoriales desde admin sin tocar código,
**so that** puedo actualizar políticas, sobre nosotros y contacto cuando necesito sin requerir un deploy.

**Acceptance Criteria:**

1. Collection `StaticPages` con campos: `title`, `slug` (único, no editable después de creado), `body` (richText Lexical reusando el config de Story 2.5), `metaTitle`, `metaDescription`, `updatedAt`.
2. Slugs reservados (no eliminables, no slug-editables) creados en seed: `about`, `etica-editorial`, `politica-de-privacidad`, `aviso-legal`, `politica-de-afiliados`, `contacto`.
3. Cada slug reservado tiene contenido placeholder editable ("Próximamente — sección en construcción").
4. Ruta dinámica `/[slug]` resuelve primero contra StaticPages (con prioridad sobre artículos para los slugs reservados — fence explícito en routing).
5. Render usa un layout simple (sin TOC, sin related), con tipografía editorial idéntica al artículo.
6. SEO mínimo (meta básico — JSON-LD opcional).
7. La página `/contacto` puede incluir información estática + (opcional MVP) un mailto. Formulario interactivo se difiere a Fase 2.
8. El contenido editorial-real de `/etica-editorial` (políticas concretas de IA, afiliados, correcciones, autoría) se completa en **Epic 6 — Editorial Hardening**, dejando placeholder en este Epic.

---

#### Notas operativas de Epic 3

- **URL conventions formalizadas:** se asume el patrón documentado al inicio del Epic. Cualquier cambio posterior implica redirects 301 y actualización de sitemap.
- **Dependencias entre stories:** 3.1 (nav) habilita visualmente al resto. 3.2 / 3.3 son independientes entre sí. 3.4 (article enhancements) depende del render base de Epic 2 (Story 2.9). 3.5 (search) es ortogonal. 3.6 (SEO meta) depende de tener todas las rutas montadas → al final del Epic. 3.7 (sitemap) depende de 3.6 y 3.2/3.3. 3.8 (RSS) puede correr en paralelo a 3.7. 3.9 (StaticPages) es independiente.
- **Stories paralelizables:** muchas combinaciones — el SM podrá hacer pairing/fanout durante sprint planning.
- **Story candidata a sub-split:** 3.5 (búsqueda) — si el highlighting con snippets resulta complejo, partir en "search básico" + "snippet/ranking refinado".
- **Lo que NO entra en Epic 3:** formulario funcional de contacto (Fase 2), búsqueda con autocomplete server-side (Fase 2 o cuando se evalúe Meilisearch), AMP, newsletter signup funcional (Epic 4), comentarios (Fase 2), monetización (Epic 5), hardening definitivo de seguridad/observabilidad (Epic 6).
- **Riesgos abiertos de Epic 3:** AdSense puede demorar semanas en aprobar la cuenta — recomendación operativa: **iniciar el trámite de AdSense apenas Epic 3 esté en producción** con al menos 10-15 artículos publicados, para no bloquear Epic 5.

---

### Epic 4 — Newsletter

**Expanded Goal:** Convertir cada visita en una potencial audiencia retornante mediante una newsletter editorial. Modelar suscriptores, implementar flujo legal de doble opt-in con Resend, exponer formularios contextuales (sidebar, footer, fin de artículo), permitir al dueño componer y enviar ediciones desde admin, mantener un archivo público de ediciones pasadas y honrar el flujo de bajas (unsubscribe). Al cerrar esta épica el dueño puede enviar la primera edición real, los suscriptores reciben emails con branding consistente, y el sistema cumple con GDPR/LOPDGDD desde el día uno.

#### Story 4.1 — Collection Subscribers + formulario funcional de signup en placements

**As a** lector,
**I want** suscribirme a la newsletter desde el footer, sidebar o el final de un artículo introduciendo solo mi email,
**so that** puedo recibir el contenido sin tener que crearme una cuenta.

**Acceptance Criteria:**

1. Collection `Subscribers` con campos: `email` (text, único, formato validado), `status` (select: `pending`/`confirmed`/`unsubscribed`/`bounced`), `confirmationToken` (text, único, generado al alta), `confirmationTokenExpiresAt` (date, +48h), `confirmedAt` (date), `unsubscribedAt` (date), `source` (text — `footer`/`sidebar`/`article-end`/`other`), `consentVersion` (text — versión de la política aceptada), `createdAt`, `updatedAt`.
2. Componente `NewsletterSignupForm` reutilizable con 3 variantes visuales: footer (compacta), sidebar (con titular + bullets de valor), article-end (CTA destacado).
3. Validación cliente: email RFC válido, mensaje claro de error.
4. Endpoint API `POST /api/newsletter/subscribe` que: valida email, crea subscriber con `status='pending'`, genera token, dispara email de confirmación (Story 4.2), devuelve respuesta de éxito (sin filtrar si el email ya existe — anti-enumeration).
5. Mensaje al usuario tras submit: "Te enviamos un email para confirmar tu suscripción. Revisá tu bandeja (y la carpeta de spam)".
6. Rate-limiting en el endpoint (10 req/min por IP) — anti-abuso.
7. Si el email ya existe en estado `unsubscribed`, permitir re-suscripción regenerando token y reconfirmando (evita lock-in irreversible accidental).
8. Honeypot field + Cloudflare Turnstile (free tier) o equivalente anti-bot — preferencia: honeypot + rate-limit en MVP, Turnstile/captcha si se observa abuso.

#### Story 4.2 — Resend setup + flujo de doble opt-in (confirmación)

**As a** dueño,
**I want** que cada suscriptor confirme su email antes de quedar activo, vía Resend,
**so that** cumplo con GDPR/LOPDGDD y mi lista solo contiene direcciones legítimas y consensuadas.

**Acceptance Criteria:**

1. Cuenta Resend conectada con dominio o usando `onresend.com` provisional hasta tener dominio.
2. Plantilla de email "Confirma tu suscripción" con branding mínimo (logo placeholder, paleta cálida, link al botón de confirmación, política de privacidad, opt-out explícito en caso de no haber sido vos).
3. Email enviado vía Resend SDK con `from`, `replyTo`, headers correctos para deliverability (SPF/DKIM cuando haya dominio; mientras tanto Resend gestiona).
4. Endpoint `GET /api/newsletter/confirm?token=...` que: valida token (existe + no expirado), marca `status='confirmed'`, setea `confirmedAt=now()`, redirige a `/newsletter?confirmed=1`.
5. Token usado se invalida tras confirmación (no replay).
6. Si el token expiró (>48h), endpoint muestra mensaje claro con opción de reenviar.
7. Endpoint `POST /api/newsletter/resend-confirmation` que regenera token y reenvía (con rate-limiting 3 req/h por email).
8. Página `/newsletter?confirmed=1` muestra mensaje de bienvenida + link a archivo.
9. Logs estructurados + Sentry para fallos de envío.

#### Story 4.3 — Flujo de baja (unsubscribe)

**As a** suscriptor,
**I want** darme de baja de la newsletter desde un link en cualquier email recibido, con un solo click,
**so that** ejerzo mi derecho a no recibir más comunicaciones y el dueño cumple con normativa.

**Acceptance Criteria:**

1. Cada email de newsletter incluye link `/api/newsletter/unsubscribe?token=...` con token único por suscriptor.
2. Endpoint procesa la baja: `status='unsubscribed'`, `unsubscribedAt=now()`, redirige a `/newsletter/baja-confirmada` con mensaje.
3. Página `/newsletter/baja-confirmada` ofrece feedback opcional ("¿Por qué te das de baja?") con 4-5 opciones radio (poco interesante / muy frecuente / nunca me suscribí / otro / prefiero no decir) — opcional, no obligatorio.
4. Suscriptor `unsubscribed` no recibe más broadcasts ni transaccionales.
5. Endpoint debe soportar `GET` (link directo) y debe ser idempotente (re-clickear el link no rompe nada).
6. Cabecera `List-Unsubscribe` configurada en cada email (RFC 2369) para clientes que la consumen automáticamente (Gmail, Apple Mail).
7. Header `List-Unsubscribe-Post: List-Unsubscribe=One-Click` (RFC 8058) implementada para baja con un solo click.

#### Story 4.4 — Collection Newsletters + composer en admin

**As a** dueño-editor,
**I want** componer ediciones de newsletter desde admin con un editor parecido al de los artículos, programando y revisando antes de enviar,
**so that** puedo producir y enviar ediciones sin salir del CMS.

**Acceptance Criteria:**

1. Collection `Newsletters` con campos: `title` (text, requerido), `slug` (text, único), `subject` (text — asunto del email, ≤120 chars), `previewText` (text — preheader, ≤150 chars), `body` (richText Lexical, reusando config de Story 2.5 con un set reducido de bloques email-safe), `status` (select: `draft`/`scheduled`/`sent`), `scheduledAt` (date), `sentAt` (date), `recipientCount` (number, populated post-send), `createdBy` (relación a Authors o Users).
2. Editor con preview lateral (HTML email render) — útil para validar antes de enviar.
3. Validación: para `scheduled` o `sent`, todos los campos requeridos completos.
4. Acción "Enviar prueba a mí mismo" en admin (manda el email a la dirección del user logueado) — clave para validar antes del envío masivo.
5. Plantilla HTML del email construida con tablas (compatibilidad email-clients) y estilos inline; soporta modo claro (modo oscuro se controla por client, se prepara contraste razonable).
6. Bloques email-safe permitidos: heading, párrafo, lista, imagen (con dimensiones fijas), blockquote, link, separador, botón CTA. Bloques no-email-safe (embed YouTube, code highlighting complejo) deshabilitados o reemplazados por placeholders (ej: YouTube → imagen thumbnail con link).

#### Story 4.5 — Envío de broadcast via Resend

**As a** dueño-editor,
**I want** enviar una edición lista a todos los suscriptores `confirmed` con un click desde admin,
**so that** distribuyo mi contenido editorial sin manejar la infraestructura de envío masivo.

**Acceptance Criteria:**

1. Botón "Enviar ahora" en admin del newsletter (visible solo cuando status=`draft` o `scheduled`-en-pasado y campos completos).
2. Endpoint server-side ejecuta el envío vía Resend Broadcasts (o batch de `emails.send` chunked si Broadcasts no encaja).
3. Lista de destinatarios = todos los suscriptores con `status='confirmed'`.
4. Cada email incluye: link de baja personalizado (Story 4.3), headers RFC, sender verificado.
5. Tras envío: status → `sent`, `sentAt = now()`, `recipientCount` populated con el total enviado.
6. Manejo de bounces: webhook de Resend actualiza `status='bounced'` en suscriptores que rebotan hard.
7. Job/cron similar al de publicaciones programadas (Story 2.6) que procesa newsletters `scheduled` cuando llega su hora.
8. Logs estructurados + Sentry alertan si algún chunk falla.
9. **No re-envíos accidentales**: una vez `sent`, el botón "Enviar" queda deshabilitado / requiere "duplicar como nueva edición" explícito.

#### Story 4.6 — Archivo público de newsletters

**As a** lector,
**I want** ver el archivo público de newsletters pasadas en `/newsletter`,
**so that** puedo evaluar el contenido antes de suscribirme y leer ediciones específicas después.

**Acceptance Criteria:**

1. Ruta `/newsletter` lista las ediciones `sent` cronológicamente con card (title, subject como subtítulo, previewText, fecha de envío, link a ver completa).
2. Ruta `/newsletter/[slug]` renderiza una edición individual con su body Lexical, layout limpio sin sidebar/related, CTA de suscripción al final.
3. ISR con revalidate adecuado.
4. SEO básico para cada edición (meta + OG + JSON-LD `Article` adaptado).
5. Estado vacío: si no hay ediciones aún, mensaje "El archivo se irá llenando cuando enviemos la primera edición — suscribite para no perderte ninguna".
6. Página `/newsletter` incluye el formulario de signup al tope (variante destacada).

#### Story 4.7 — Gestión de lista en admin (filtros, export, manual edits)

**As a** dueño,
**I want** filtrar, exportar y gestionar suscriptores desde admin,
**so that** entiendo el estado de mi lista, puedo limpiar bounces, y conservo backup periódico fuera del sistema.

**Acceptance Criteria:**

1. Vista de lista en admin con filtros por status, fecha de alta, source.
2. Búsqueda por email parcial.
3. Botón "Exportar a CSV" exporta los suscriptores filtrados con email, status, confirmedAt, source.
4. Estadísticas básicas en el header de la lista: total confirmed, total pending, total unsubscribed, alta últimos 7/30 días.
5. Acciones bulk: marcar como `bounced` manualmente (limpieza), eliminar (con confirmación + audit log).
6. Capacidad de agregar manualmente un suscriptor con `status='confirmed'` (solo si el dueño tiene consentimiento documentado — el sistema deja audit log).
7. Honra el derecho al olvido (NFR12 / NFR14): eliminar un suscriptor desde admin borra su registro completamente.

---

#### Notas operativas de Epic 4

- **Dependencias entre stories:** 4.1 → 4.2 (signup requiere confirmación). 4.3 (unsub) puede ir en paralelo con 4.2. 4.4 (composer) y 4.7 (list mgmt) son independientes de 4.2-4.3. 4.5 (envío) depende de 4.4 + 4.2 + 4.3. 4.6 (archivo) depende de 4.4 y se beneficia de 4.5.
- **Stories paralelizables:** 4.1, 4.4, 4.7 pueden arrancarse simultáneamente si se trabaja con más de un foco.
- **Stories candidatas a sub-split:** 4.5 (envío broadcast) — si la integración con Resend Broadcasts es no-trivial, partir en "envío manual a confirmados" + "scheduled + bounces vía webhook".
- **Lo que NO entra en Epic 4:** newsletter sponsored builder (Fase 2), segmentación avanzada (Fase 2), A/B testing de subject (Fase 2+), automation/secuencias (out of MVP), newsletter VIP para miembros premium (Fase 2 — connects con Epic 5).
- **Riesgos abiertos:**
  - **Deliverability sin dominio propio:** Resend usando `onresend.com` puede tener inbox placement inferior a un dominio verificado. Mitigación: registrar dominio antes del primer envío masivo real (idealmente al cerrar Epic 4), aunque el desarrollo previo no lo requiera. **Si todavía no hay dominio cuando llegamos a Epic 4, considerar registrarlo entonces como prerequisito de la primera campaña real.**
  - **Tasas de bounce / spam complaints altas en LATAM con proveedores grandes (Gmail, Hotmail):** mantener calidad de lista (doble opt-in ya cubre la mayor parte) y monitorear las primeras campañas.

---

### Epic 5 — Monetization Layer

**Expanded Goal:** Activar las capas de monetización del MVP (AdSense y afiliados Amazon) con UX no invasiva y compliance legal, y dejar lista la infraestructura de membresía premium para que su activación pública en Fase 2 sea solo flipping de un feature flag sin migraciones. Al cerrar esta épica el blog tiene los slots de ads renderizados condicionadamente, los enlaces afiliados con tracking y disclaimer automático, y las collections de premium (Plan, Member, Subscription) + Stripe SDK instaladas con `PREMIUM_ENABLED=false`.

#### Story 5.1 — AdSlots collection + configuración global + toggle por artículo

**As a** dueño,
**I want** definir desde admin qué slots de ads están activos globalmente y poder desactivarlos por artículo específico,
**so that** controlo la densidad de ads y puedo ofrecer artículos sin ads cuando lo amerite (sponsored, premium, sensible).

**Acceptance Criteria:**

1. Collection `AdSlots` con campos: `key` (text único — `header`, `in-feed-home`, `in-article-top`, `in-article-paragraph-N`, `sidebar`, `footer`), `enabled` (checkbox global), `adsenseSlotId` (text — el ID del slot configurado en AdSense), `description` (text), `targetPositions` (json o text — configuración como "después del párrafo N").
2. Slots iniciales seedeados: `header`, `in-feed-home`, `in-article-mid` (mitad del artículo), `in-article-end`, `sidebar`, `footer`.
3. Campo `disableAds` (checkbox) en collection `Posts` (FR20) — sobrescribe slots para ese artículo específico.
4. Configuración global `ADSENSE_PUBLISHER_ID` en env vars; si está vacía, ningún slot se renderiza (modo dev/preview limpio).
5. UI de admin para los slots: lista con toggle on/off rápido y edición avanzada con un click.
6. Audit log de cambios en AdSlots (quién enabled/disabled qué y cuándo).

#### Story 5.2 — Integración AdSense + renderizado de slots en sitio público

**As a** lector,
**I want** que los ads se carguen sin romper la lectura (sin layout shift, sin interstitials, sin autoplay),
**so that** sigo disfrutando el sitio aunque tenga ads.

**Acceptance Criteria:**

1. Script de AdSense (`adsbygoogle.js`) cargado **diferido** (`async`, `loading="lazy"`) — no bloquea LCP.
2. Componente `<AdSlot keyName="..." />` reutilizable que: lee config del slot desde DB cache, valida `enabled=true` y permisos contextuales, renderiza `<ins class="adsbygoogle">` con dimensiones reservadas para evitar CLS.
3. Slots se reservan con altura mínima conocida (placeholder skeleton sutil) para que el CLS se mantenga <0.05 (NFR1).
4. **Densidad controlada (asunción confirmada en Sección 3.1):** máximo 2 slots above-the-fold en cualquier vista; in-article cada N párrafos configurable (default N=8).
5. Slots **NO se renderizan en `/admin`, `/preview/*`, `/api/*`** ni en sub-rutas del newsletter (`/newsletter*`).
6. Cuando un Post tiene `disableAds=true`, todos los slots dentro del template del artículo se omiten.
7. Cuando una page de Article tiene `aiAssistedFlag=true`, los ads se mantienen (FR24 dijo "auditable interno", no afecta monetización).
8. Lighthouse Performance en home y artículo con AdSense activo se mantiene ≥85 móvil (ajuste vs. NFR3 que pide ≥90 sin ads — documentado).

#### Story 5.3 — Cookie consent / CMP para AdSense personalizado

**As a** dueño,
**I want** un banner de consentimiento que cumpla con GDPR/LOPDGDD cuando AdSense use cookies personalizadas,
**so that** cumplo con la normativa europea sin perder ingresos por ads no personalizados en otras regiones.

**Acceptance Criteria:**

1. CMP (Cookie Management Platform) integrada — opciones evaluadas: Google Funding Choices (gratis, integrado con AdSense, IAB TCF v2.2-compliant) ← **preferencia MVP**, o Cookiebot/Iubenda (free tier).
2. Banner se muestra a usuarios desde regiones con consent-requirement (EU/UK/EEA — detección por IP/CMP automática).
3. Hasta consentimiento, scripts de tracking se bloquean (AdSense puede caer a non-personalized ads — Google Funding Choices lo gestiona automáticamente).
4. Usuario puede gestionar preferencias en `/preferencias-de-cookies` (o equivalente).
5. Decisión del usuario persistida y respetada en visitas siguientes.
6. Consent record auditable (con timestamp + versión de política aceptada).
7. Plausible (preferido en NFR analytics) no requiere consent por ser cookie-less — confirmado.

#### Story 5.4 — Afiliados Amazon: shortlinks + tracking de clicks

**As a** dueño,
**I want** insertar enlaces afiliados Amazon en mis artículos a través de un shortlink interno que registre cada click,
**so that** mantengo URLs limpias en el contenido, puedo cambiar el tag de afiliado centralmente y entiendo qué productos se clickean.

**Acceptance Criteria:**

1. Collection `AffiliateLinks` con campos: `slug` (text único, ej. `iphone-15-pro`), `targetUrl` (URL Amazon completa con `tag=` afiliado), `label` (text — descripción interna), `product` (text opcional — nombre del producto para reporte), `clickCount` (number, auto-increment), `lastClickAt` (date), `disabled` (checkbox), `region` (select: `es`/`mx`/`ar`/`us`/`co`/`cl`/`other` — para diferentes tiendas Amazon).
2. Endpoint `GET /go/[slug]` que: valida que `disabled=false`, incrementa `clickCount`, setea `lastClickAt=now()`, redirige a `targetUrl` con HTTP 302.
3. Tag de afiliado regional configurable en env vars (`AMAZON_TAG_ES`, `AMAZON_TAG_MX`, etc.) — el sistema construye el `targetUrl` final con el tag correcto.
4. UI en el editor Lexical (Story 2.5) para insertar un link como afiliado: selector de afiliado existente o crear nuevo inline; al insertar genera `<a href="/go/[slug]" rel="sponsored nofollow">` con el texto del link.
5. Logs estructurados de clicks (anonimizados — sin IP ni userAgent persistido en MVP, solo agregados).
6. Rate-limiting suave en `/go/*` (1000 req/min global) — para detectar bots maliciosos.
7. Reporte básico en admin: top 10 afiliados por clicks últimos 7/30 días.

#### Story 5.5 — Disclaimer automático en artículos con afiliados

**As a** lector,
**I want** ver claramente que un artículo contiene enlaces afiliados antes de leerlo,
**so that** entiendo el modelo de monetización del sitio y la transparencia editorial.

**Acceptance Criteria:**

1. Cuando un Post contiene al menos un enlace afiliado (auto-detectado al guardar, escaneando el body Lexical por links a `/go/*`), se setea internamente `hasAffiliateLinks=true`.
2. Si `hasAffiliateLinks=true`, el render público de la página de artículo (Story 2.9 / 3.4) inserta automáticamente un disclaimer visible **antes** del cuerpo del artículo (caja con borde sutil, ícono, texto: "Este artículo contiene enlaces afiliados. Si comprás a través de ellos, El Mate Digital recibe una pequeña comisión sin costo adicional para vos. [Ver política completa](/politica-de-afiliados)").
3. El disclaimer es opcionalmente sobreescribible desde admin con un texto personalizado por artículo (no obligatorio).
4. El disclaimer aplica los mismos tokens de tema (claro/oscuro) que el resto del artículo.
5. `<a>` afiliados llevan `rel="sponsored nofollow"` automáticamente.
6. Test e2e verifica: artículo con link afiliado renderiza disclaimer; artículo sin link no lo renderiza.

#### Story 5.6 — Infraestructura premium: collections Plans/Members/Subscriptions + Stripe SDK + `PREMIUM_ENABLED` flag

**As a** dueño-dev,
**I want** dejar instalada toda la infraestructura de membresía premium en MVP detrás de un feature flag,
**so that** en Fase 2 puedo activar el premium sin migraciones de DB ni refactors estructurales — solo flippear el flag y completar el UX público.

**Acceptance Criteria:**

1. Collection `Plans` con: `key` (text único — `monthly`, `annual`), `name`, `priceCents`, `currency`, `interval` (`month`/`year`), `stripePriceId` (text — vacío hasta tener cuenta Stripe live), `benefits` (richText), `active` (checkbox, default false). Seeded con 2 plans placeholder (`monthly` $5/mes, `annual` $50/año — valores tentativos).
2. Collection `Members` con: `user` (relación a Users), `status` (`pending`/`active`/`paused`/`canceled`/`expired`), `stripeCustomerId`, `currentSubscription` (relación a Subscriptions), `joinedAt`, `metadata`.
3. Collection `Subscriptions` con: `member` (relación a Members), `plan` (relación a Plans), `stripeSubscriptionId`, `status` (mirror de Stripe — `active`/`past_due`/`canceled`/etc.), `startedAt`, `currentPeriodEnd`, `canceledAt`.
4. Stripe SDK instalado (`stripe` npm package) y configurado con env vars (`STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `STRIPE_PUBLISHABLE_KEY`).
5. Feature flag `PREMIUM_ENABLED` (env var, default `false`).
6. Cuando `PREMIUM_ENABLED=false`:
   - Las collections existen y son visibles solo a super-admin en admin (no expuestas a roles inferiores).
   - Las rutas públicas `/premium`, `/checkout`, `/cuenta` NO existen (404 explícito).
   - Webhooks de Stripe en `/api/stripe/webhook` están registrados pero responden 503 con log.
   - El helper `requiresMembership(post)` (Story 5.7) devuelve `false` siempre.
7. Cuando `PREMIUM_ENABLED=true` (sólo en Fase 2): se exponen las rutas, los webhooks procesan eventos reales, el gating activa.
8. Tests unitarios cubren ambos modos del flag (con/sin premium).
9. Documentación en README: cómo activar premium en Fase 2 (checklist paso a paso).

#### Story 5.7 — Gating helper `requiresMembership(post)` + tests con flag off

**As a** dueño-dev,
**I want** un helper centralizado que decide si un post requiere membresía, controlado por el feature flag,
**so that** en Fase 2 el gating se enciende sin tocar lógica dispersa por el código.

**Acceptance Criteria:**

1. Campo opcional `isPremium` (checkbox, default false) agregado a collection `Posts`.
2. Helper exportado `requiresMembership(post): boolean` con signature estable.
3. Cuando `PREMIUM_ENABLED=false`: `requiresMembership` retorna `false` siempre (independiente de `post.isPremium`).
4. Cuando `PREMIUM_ENABLED=true`: `requiresMembership` retorna `post.isPremium`.
5. El helper se invoca en el render de Story 2.9 (página de artículo) — en MVP no afecta render (flag off), pero el call site está implementado.
6. Test unitario cubre ambos modos.
7. README documenta cómo en Fase 2 se completa el gating real (renderizar paywall + check de Member status).

---

#### Notas operativas de Epic 5

- **Dependencias entre stories:** 5.1 → 5.2 → 5.3 (AdSense pipeline). 5.4 → 5.5 (afiliados). 5.6 → 5.7 (premium infra). Los 3 sub-grupos son **independientes entre sí** — pueden ejecutarse en paralelo.
- **Stories candidatas a sub-split:**
  - 5.2 (slots de AdSense) — si los slots in-article (cada N párrafos) son complejos, partir en "header/sidebar/footer/in-feed" + "in-article cada N párrafos".
  - 5.6 (premium infra) — si supera 5h, partir en "Plans + Stripe SDK" + "Members + Subscriptions + flag".
- **Lo que NO entra en Epic 5:**
  - UI pública del premium (página de planes, checkout, customer portal) → Fase 2.
  - Procesamiento de webhooks reales → Fase 2.
  - Reportes financieros / dashboard de ingresos consolidado → Fase 2.
  - Newsletter sponsored marketplace → Fase 2.
  - Promociones / códigos descuento → Fase 2+.
- **Riesgos abiertos:**
  - **AdSense puede rechazar la cuenta** o pedir cambios (contenido suficiente, página de privacidad clara, idioma soportado, etc.). Mitigación: aplicar después de Epic 3 con contenido publicado, y tener `/politica-de-privacidad` real (no placeholder) antes de aplicar.
  - **Amazon Associates tiene umbral de aprobación** (3 ventas en 180 días). En MVP el trámite se inicia cuando Epic 5 cierra; las primeras semanas pueden estar en período de prueba.
  - **CMP add-on cost:** Google Funding Choices es gratis; si querés migrar a Iubenda/Cookiebot con más control granular, hay costo mensual a evaluar en Fase 2.

---

### Epic 6 — Editorial Hardening & Launch Polish

**Expanded Goal:** Cerrar el MVP convirtiéndolo en un producto listo para soft-launch real: completar el contenido editorial-real de las páginas de políticas (especialmente `/etica-editorial`), endurecer la seguridad (CSP estricta, rate-limiting comprehensive, auditoría), exponer audit log en admin, refinar el dashboard con métricas operativas, agregar seed data realista, optimización final de performance/a11y, validar observabilidad y dejar runbooks operativos. Al cerrar esta épica, el blog puede invitar al público sin reservas.

#### Story 6.1 — Contenido editorial-real de `/etica-editorial` + páginas legales

**As a** dueño,
**I want** que las páginas `/etica-editorial`, `/politica-de-privacidad`, `/aviso-legal` y `/politica-de-afiliados` tengan contenido editorial-real y legalmente sólido,
**so that** AdSense aprueba la cuenta, los lectores entienden las políticas y el blog opera dentro del marco legal.

**Acceptance Criteria:**

1. `/etica-editorial` redactada con:
   - Política de uso de IA (asistencia sí, generación autónoma no, firma humana siempre, FR23).
   - Política de afiliados (qué se marca, cómo, transparencia comercial).
   - Política de correcciones (cómo se publican erratas y updates, plazo).
   - Código de autoría (compromiso editorial, fuentes, citación).
2. `/politica-de-privacidad` redactada con:
   - Datos recopilados (email para newsletter, IP para AdSense/Plausible, cookies de consent).
   - Bases legales por finalidad (consentimiento, interés legítimo).
   - Derechos del usuario (acceso, rectificación, borrado — DSAR/NFR14).
   - Retención de datos.
   - Transferencias internacionales (Vercel, Neon, Resend, Cloudinary, AdSense).
   - Contacto del responsable.
3. `/aviso-legal` redactada con: titular del sitio, domicilio (si corresponde — puede ser dirección postal del dueño o PO Box), información de contacto, jurisdicción aplicable.
4. `/politica-de-afiliados` redactada con: qué programas de afiliados se usan (Amazon Associates ES/MX/AR/etc.), cómo se identifican (`rel="sponsored nofollow"` + disclaimer en cada artículo), declaración de honestidad editorial.
5. Versión de cada política fechada (`Última actualización: YYYY-MM-DD`) — visible al lector.
6. Plantillas legales revisadas vía LLM + abogado puntual (no llega bloqueante de la story, pero documentado).
7. Cada cambio en estas páginas queda en el versionado de StaticPages (Story 3.9).

#### Story 6.2 — Audit log expuesto en admin (FR27)

**As a** dueño-administrador,
**I want** ver un registro auditable de acciones críticas (logins, publicaciones, edits sensibles, cambios de configuración),
**so that** detecto actividad sospechosa y entiendo qué pasó en el sistema.

**Acceptance Criteria:**

1. Collection `AuditLogs` ya alimentada desde épicas anteriores (logins, publicaciones, AdSlots changes, subscribers manual mods).
2. Vista de admin `/admin/collections/audit-logs` con: timestamp, actor (User), acción (`login`/`logout`/`post_publish`/`post_unpublish`/`subscriber_delete`/`adslot_toggle`/etc.), entidad afectada (collection + id), payload resumido.
3. Filtros por: actor, fecha, tipo de acción.
4. Retención mínima: 90 días en MVP, configurable.
5. Logs incluyen IP solo para acciones de seguridad (login success/failed); para acciones editoriales NO se persiste IP.
6. Read-only desde admin — no editable.

#### Story 6.3 — Hardening de seguridad (CSP estricta + headers + rate-limiting comprehensive)

**As a** dueño,
**I want** que el sitio cumpla con las mejores prácticas de seguridad web,
**so that** reduzco al máximo la superficie de ataque y protejo a los lectores.

**Acceptance Criteria:**

1. CSP estricta configurada en `next.config.js` / middleware (NFR10):
   - `default-src 'self'`
   - `script-src 'self' 'nonce-{generated}' adsbygoogle.googleapis.com googletagmanager.com plausible.io`
   - `style-src 'self' 'unsafe-inline'` (Tailwind genera inline en hot reload — refinar en build)
   - `img-src 'self' data: res.cloudinary.com vercel.app twimg.com fbcdn.net`
   - `frame-src youtube.com twitter.com x.com`
   - `connect-src 'self' sentry.io plausible.io`
   - `frame-ancestors 'none'`
2. Headers de seguridad: `Strict-Transport-Security` (HSTS, max-age 1 año, includeSubDomains), `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy` mínima.
3. Rate-limiting verificado en endpoints sensibles (NFR9): `/admin` login, `/api/newsletter/subscribe`, `/api/newsletter/resend-confirmation`, `/api/search`, `/go/*` (afiliados), `/api/stripe/webhook` (cuando se active).
4. Dependabot (o equivalent) activo en GitHub con auto-update de patches críticos (NFR11).
5. Auditoría manual final con `pnpm audit` + `npx vercel security` (si existe) — sin vulnerabilidades High/Critical en producción.

#### Story 6.4 — Dashboard de admin con métricas operativas

**As a** dueño,
**I want** un dashboard en admin que me muestre el estado del sistema y de mi operación editorial,
**so that** veo rápidamente qué necesita mi atención al loguearme.

**Acceptance Criteria:**

1. Página `/admin` (post-login) muestra dashboard con:
   - **Editorial:** posts publicados últimos 30 días, drafts pendientes, próximos programados (lista corta).
   - **Newsletter:** suscriptores activos, alta últimos 7 días, próxima edición programada.
   - **Monetización:** top 5 afiliados por clicks últimos 30 días, total clicks afiliados, status de slots AdSense (cuántos activos).
   - **Sistema:** último error capturado por Sentry (link al dashboard), estado del último deploy.
2. Atajos rápidos: "Nuevo artículo", "Nueva newsletter", "Ver media library", "Configurar slots".
3. Modo claro/oscuro respetado.
4. Render con datos cacheados (revalidate cada 5 min) — no debe ralentizar el login.

#### Story 6.5 — Seed de contenido editorial realista para soft-launch

**As a** dueño,
**I want** llegar al soft-launch con 10-15 artículos publicados de calidad editorial real, no placeholders,
**so that** AdSense aprueba la cuenta, los lectores que llegan ven un blog "vivo" y el SEO empieza a indexar contenido relevante.

**Acceptance Criteria:**

1. Plan editorial pre-launch: definir 10-15 artículos de mix tipos (3-4 análisis, 3-4 noticias evergreen, 2-3 tutoriales, 2-3 reseñas/guías), cubriendo al menos 4 categorías (iPhone, Mac, Servicios, otro).
2. Cada artículo: ≥800 palabras, featured image, autor (el dueño), categoría, tipo, tags relevantes, meta-SEO completo.
3. Al menos 2 artículos con enlaces afiliados Amazon (testing del disclaimer auto).
4. Al menos 1 artículo con embed YouTube + 1 con embed X (testing de bloques).
5. Pages legales y `/etica-editorial` con contenido real (Story 6.1) — prerequisito.
6. Seed se hace **manualmente desde admin** (es contenido real, no script automatizado) — esta story es de planning + producción, no de código nuevo.
7. Validación: Lighthouse en home y al menos 3 artículos cumple ≥90 mobile (NFR3).

#### Story 6.6 — Tuning final de performance + a11y + observability

**As a** dueño-dev,
**I want** validar que todos los KPI técnicos del PRD (CWV, Lighthouse, WCAG AA, observability) se cumplen en producción con tráfico real,
**so that** lanzo con confianza y detecto degradaciones desde el día 1.

**Acceptance Criteria:**

1. Lighthouse mobile ≥90 (Performance, SEO, Accessibility) en home + página de artículo + página de categoría (NFR3).
2. Core Web Vitals (RUM Vercel Speed Insights + CrUX) en p75 móvil cumplen: LCP <2.0s, INP <200ms, CLS <0.05 (NFR1).
3. Audit a11y con axe-core (manual o vía CI) sin issues de severidad alta — WCAG 2.1 AA (NFR18).
4. Sentry recibe errores reales (test de bombardeo) sin perder eventos.
5. Plausible / Vercel Analytics registra pageviews y CWV en tiempo real.
6. Logs estructurados de eventos clave (publicación, subscriber, afiliado click, scheduled job ejecuciones) revisables desde Vercel Logs.
7. Documento `docs/operations/runbook.md` con: cómo investigar un error de Sentry, cómo verificar que el cron está corriendo, cómo restaurar un draft, qué hacer si una edición de newsletter falla a mitad de envío, contacto/escalación.

#### Story 6.7 — Tests E2E para flujos críticos (NFR20)

**As a** dueño-dev,
**I want** suite de tests E2E con Playwright que cubre los flujos críticos del MVP,
**so that** cada PR los corre y no se rompen silenciosamente con cambios futuros.

**Acceptance Criteria:**

1. Test E2E: flujo de publicación (crear draft → schedule → publish → ver en producción).
2. Test E2E: signup newsletter completo (form → email mock → confirmación → status `confirmed`).
3. Test E2E: click en afiliado (insertar link → click `/go/[slug]` → redirect correcto → counter incrementado).
4. Test E2E: gating premium con flag off (artículo con `isPremium=true` → render normal porque PREMIUM_ENABLED=false).
5. Test E2E: render de artículo con embed YouTube + code block + callout — sin errores.
6. Tests corren en CI (preview deploy + main).
7. Documentación de cómo correr/agregar tests E2E en el README.

---

#### Notas operativas de Epic 6

- **Epic 6 es el más heterogéneo** — mezcla contenido editorial (Story 6.1, 6.5), seguridad (6.3), UX de admin (6.4), tests (6.7) y polish operativo (6.6, 6.2). Es el "tying up loose ends" antes del soft-launch.
- **Dependencias entre stories:** 6.1 (políticas) es prerequisito de 6.5 (seed) — sin políticas reales no hay aprobación AdSense ni publicación seria. 6.2-6.4-6.7 son independientes entre sí. 6.6 (tuning) cierra al final.
- **Stories paralelizables:** 6.1 ∥ 6.3 ∥ 6.4 ∥ 6.7. 6.5 depende de 6.1. 6.6 al final.
- **Story 6.5 NO es código** — es producción editorial. El SM puede contabilizarla como capacidad del dueño-editor, no del dueño-dev. Importante diferenciar el sizing.
- **Story candidata a sub-split:** 6.6 (tuning final) puede partirse en "perf + a11y" + "observability + runbook" si el alcance crece.
- **Lo que NO entra en Epic 6:**
  - Activación pública de premium (Fase 2).
  - Sistema de comentarios (Fase 2).
  - Newsletter VIP / sponsored marketplace (Fase 2).
  - Tests visuales / Chromatic (Fase 2+).
  - Load testing real (Fase 2+).
- **Definición de "MVP DONE":** cierre de Epic 6 + cumplir el MVP Success Criteria del brief (publicación consistente ≥3/sem por 8 semanas, Lighthouse ≥90, primeras 5K sesiones orgánicas, AdSense aprobada, newsletter ≥500 con OR >35%, admin 100% sin tocar código).

---

## 7. Checklist Results + Next Steps

### 7.1 PM Checklist Validation Report

> _Validación ejecutada el 2026-05-24 contra `.aios-core/product/checklists/pm-checklist.md` (9 categorías, ~60 ítems)._

#### Executive Summary

- **Completitud del PRD:** ~90 %.
- **MVP scope:** *Just Right* — 6 épicas, 48 stories, secuencia clara entre fundación y soft-launch.
- **Readiness para fase de arquitectura:** ✅ **READY FOR ARCHITECT** — con mejoras menores recomendadas pero no bloqueantes.
- **Gaps críticos:** ninguno bloqueante. Áreas a reforzar: user flows explícitos (Sección 3), diagramas (ER + sitemap), runbook operativo (cubierto en Story 6.6).

#### Category Analysis

| # | Categoría | Status | Notas |
|---|---|---|---|
| 1 | Problem Definition & Context | **PASS** (~85 %) | Cubierto en Background + brief v1.2; competitor analysis profundo aún pendiente como tarea del @analyst |
| 2 | MVP Scope Definition | **PASS** (~95 %) | Out-of-scope explícito por épica; Phase 2 documentada |
| 3 | User Experience Requirements | **PARTIAL** (~75 %) | Sección 3 sólida; faltan user-flows explícitos (entry/exit/decision points) — el UX Expert los completa |
| 4 | Functional Requirements | **PASS** (~95 %) | 27 FRs verificables, agrupados por dominio, dependencias documentadas |
| 5 | Non-Functional Requirements | **PASS** (~95 %) | 24 NFRs cubriendo perf, seguridad, privacidad, a11y, mantenibilidad, observabilidad |
| 6 | Epic & Story Structure | **PASS** (~95 %) | 6 épicas, 48 stories vertical-slice; Epic 1 cubre setup completo |
| 7 | Technical Guidance | **PASS** (~90 %) | Stack constraints firmes; areas de riesgo flagueadas; technical debt approach implícito (no explícito) |
| 8 | Cross-Functional Requirements | **PASS** (~85 %) | Data + integraciones cubiertas; spec exacta de webhooks Stripe/Resend a definir por @architect |
| 9 | Clarity & Communication | **PARTIAL** (~80 %) | Documentación versionada y consistente; **falta diagramas** (ERD de collections, sitemap de rutas) — se delegan al @architect |

#### Top Issues by Priority

**BLOCKERS:** ninguno.

**HIGH:**
- Sin **branding sprint** cerrado, los placeholders visuales de Story 1.7 / 3.5 son tentativos. Idealmente paralelizar branding con Epic 1 para no rehacer componentes después.
- **Iniciar trámite AdSense** durante Epic 3 (con 10-15 artículos publicados) — sin esto, Epic 5 puede quedar bloqueado por aprobación.

**MEDIUM:**
- **User flows explícitos** (signup newsletter end-to-end, publicación con scheduling, lectura → suscripción → confirmación) son útiles para el UX Expert; el PRD los cubre implícitamente via stories pero un diagrama de flujo agregaría claridad.
- **ERD de collections** (Posts ↔ Categories/Tags/Authors/Media + Plans ↔ Members ↔ Subscriptions + AdSlots + Subscribers + Newsletters + AffiliateLinks + StaticPages + AuditLogs) → tarea del @architect en architecture.md.
- **Sitemap de rutas** (`/`, `/articulo/[slug]`, `/categoria/[slug]`, etc.) → idem @architect.

**LOW:**
- **Política de technical debt** explícita (cuándo aceptar deuda, cómo registrarla) — no es bloqueante en MVP de 1 dev.
- **Communication plan formal con stakeholders** — innecesario en MVP de fundador único, queda implícito vía iteración 1-1.

#### MVP Scope Assessment

- **Features candidatas a cortar si el cronograma aprieta:** ninguna crítica; el PRD ya es relativamente lean. Si hubiera que cortar, lo primero sería **Story 3.5 (búsqueda)** — un blog joven se descubre vía Google, no vía búsqueda interna. Aceptable diferir.
- **Features faltantes esenciales:** ninguna detectada.
- **Complexity concerns:** Lexical editor + embeds (Story 2.5), CSP estricta + AdSense + CMP coexistiendo (Story 6.3 + 5.3), gating helper con flag (Story 5.7).
- **Timeline realista:** brief plantea 8-12 semanas part-time. Las 48 stories son ejecutables en ese rango por un dueño-dev disciplinado; la cadencia editorial (≥4 artículos/semana, Story 6.5) corre en paralelo y depende de capacidad humana del editor, no del dev.

#### Technical Readiness

- **Claridad de constraints técnicos:** alta — Section 4 los cierra explícitamente.
- **Riesgos técnicos identificados:**
  - Cloudinary plugin Payload 3.0 — verificar compatibilidad temprano.
  - X/Twitter embeds — fragilidad histórica; mitigación con fallback ya documentada.
  - CSP + AdSense — tuning iterativo, no falla pero requiere ajustes.
  - Deliverability de Resend sin dominio propio — flagueado.
- **Areas requiring architect investigation:**
  - Decisión final Cloudinary vs Vercel Blob.
  - Estrategia exacta de cron jobs (Vercel Cron vs QStash).
  - Esquema de cache + ISR + on-demand revalidation por entity.
  - Spec detallada de webhooks Stripe (para activar Fase 2 sin re-trabajo).
  - Schema completo de Postgres + indexes (especialmente para FTS).

#### Recommendations

1. **Iniciar branding sprint** en paralelo con Epic 1 (no bloquea pero acelera resultado final).
2. **Aplicar a AdSense** apenas Epic 3 esté en producción con 10-15 artículos (recordatorio operativo).
3. **El @architect debe producir architecture.md con:** ERD de collections, sitemap de rutas, decisiones puntuales sobre Cloudinary y Cron, schema SQL y plan de indexes, secuencia detallada de webhooks Stripe para Fase 2.
4. **El @sm (SM) debe transformar las 48 stories del PRD en historias formales** con sizing relativo (story points) y ordenarlas en sprints de 1-2 semanas.
5. **Considerar pedir competitor analysis profundo** (`*create-competitor-analysis` del @analyst) si todavía no se ejecutó — refuerza la sección 1 del PRD post-handoff.

#### Final Decision

✅ **READY FOR ARCHITECT.** El PRD está completo, internamente consistente, y suficientemente preciso para que el @architect produzca un documento de arquitectura sin re-litigar decisiones de producto.

---

### 7.2 Next Steps

#### Immediate Actions (post-PRD)

1. **Handoff a @architect (Aria)** — producir `docs/projects/apple-tech-blog/architecture.md` (ERD, sitemap, schema SQL, decisiones de Cloudinary/Cron, spec detallada de integraciones).
2. **Handoff a @sm (River)** post-architecture — descomponer las 48 stories del PRD en historias formales sized y secuenciadas por sprint.
3. **Iniciar branding sprint** (paralelo) para destrabar identidad visual antes de que Epic 1 cierre Story 1.7.
4. **Considerar `*create-competitor-analysis`** del @analyst si todavía no se ejecutó.
5. **Crear repo nuevo `el-mate-digital`** (separado de `aios-core`) cuando el @architect confirme estructura final.
6. **Plan editorial pre-launch:** empezar a redactar drafts de los 10-15 artículos seed (Story 6.5) en paralelo a Epic 1-3.

#### Dependencies Between Workstreams

| Stream | Bloquea a | Bloqueado por |
|---|---|---|
| @architect | @sm, @dev, @ux-design-expert | PRD (✅ listo) |
| @ux-design-expert | Epic 1 Story 1.7 (tokens) | PRD (✅), idealmente architecture |
| Branding sprint | Story 1.7 + 3.5 | Owner decision |
| AdSense application | Epic 5 inicio | Epic 3 cerrado + 10-15 artículos publicados + `/politica-de-privacidad` real (Story 6.1) |
| Domain registration | Newsletter campaña real (post Epic 4) | Owner decision |

---

### 7.3 UX Expert Prompt

> _Para iniciar el modo "Create UX Architecture" / "Design Architect" usando este PRD como insumo._

```
@ux-design-expert — modo "Create UX Architecture" para El Mate Digital.

Insumos:
- PRD: docs/projects/apple-tech-blog/prd.md (este documento, v1.0)
- Brief: docs/projects/apple-tech-blog/project-brief.md v1.2

Foco prioritario para tu output (frontend-spec.md o equivalente):
1. User flows explícitos end-to-end de los flujos críticos:
   - Suscripción newsletter (form → confirm → archive)
   - Publicación de artículo (admin: draft → schedule → publish)
   - Lectura (home → artículo → related → suscripción)
   - Click afiliado (artículo → /go/[slug] → Amazon)
2. Wireframes/mockups de baja fidelidad para los 21 screens
   listados en Sección 3.3 del PRD.
3. Sistema de tokens de diseño completo (paleta, tipografía, espaciado,
   elevaciones) basado en la dirección tentativa de Sección 3.5 —
   alineado con el branding sprint si ya cerró.
4. Specs de componentes core (Card, ArticleHeader, NewsletterSignup,
   AdSlot placeholder, TOC sticky, Header/Footer).
5. Validación WCAG AA en mockups (focus states, contraste, navegación
   por teclado).

Constraints firmes (no re-litigar):
- Web responsive móvil-first
- Modo claro + oscuro
- Metáfora del mate: lectura como ritual, calidez sobre asepsia
- Comentarios fuera de MVP
- Premium no expuesto públicamente en MVP
```

---

### 7.4 Architect Prompt

> _Para iniciar el modo "Create Architecture" usando este PRD como insumo._

```
@architect (Aria) — modo "Create Architecture" para El Mate Digital.

Insumos:
- PRD: docs/projects/apple-tech-blog/prd.md (este documento, v1.0)
- Brief: docs/projects/apple-tech-blog/project-brief.md v1.2

Objetivo: producir docs/projects/apple-tech-blog/architecture.md.

Decisiones del PRD que son CONSTRAINTS (no re-litigar):
- Stack: Next.js 15 App Router + TypeScript estricto + Payload CMS 3.0
  embedded + PostgreSQL Neon + Vercel + Cloudinary + Resend + Stripe SDK.
- Repo: nuevo, separado de aios-core (sugerencia: el-mate-digital).
- Arquitectura: monolito modular, no microservicios.
- Testing: Vitest unit + Playwright e2e selectivo.
- Premium: collections + SDK + flag PREMIUM_ENABLED=false desde MVP.
- Comentarios: fuera de MVP (Fase 2).
- Dominio: postergado, MVP en *.vercel.app.

Foco prioritario para tu output:
1. ERD completo de collections: Posts, Categories, ContentTypes, Tags,
   Authors, Media, StaticPages, Subscribers, Newsletters, AdSlots,
   AffiliateLinks, Plans, Members, Subscriptions, AuditLogs, Users.
   Incluir relaciones y constraints.
2. Sitemap de rutas (público + admin + API) con métodos y respuestas.
3. Schema SQL inicial + plan de indexes (especialmente para FTS de
   Posts en Story 3.5 y para AffiliateLinks/Subscribers).
4. Decisión definitiva: Cloudinary vs Vercel Blob (con criterios
   técnicos y de costo).
5. Estrategia de cron jobs (Vercel Cron vs QStash) para Story 2.6 y 4.5.
6. Estrategia de cache + ISR + on-demand revalidate por entidad.
7. Spec detallada de webhooks Stripe para que la activación Fase 2 no
   requiera re-trabajo.
8. Estrategia de CSP estricta + AdSense + CMP coexistiendo (NFR10 +
   Story 5.3 + Story 6.3).
9. Plan de seeds (categorías, tipos, autor inicial, slots, plans,
   static pages).
10. Riesgos técnicos a investigar pre-Epic 1: compatibilidad Cloudinary
    plugin Payload 3.0, fragilidad embeds X/Twitter.

Producto esperado: docs/projects/apple-tech-blog/architecture.md
con la profundidad necesaria para que el @sm produzca stories
formales y el @dev ejecute Epic 1 sin ambigüedad técnica.
```

---

*— Morgan, planejando o futuro 📊*










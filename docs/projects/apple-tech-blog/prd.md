# El Mate Digital — Product Requirements Document (PRD)

> **Documento:** PRD v0.1 (en construcción)
> **Autor:** Morgan (PM Agent) — AIOS
> **Fecha:** 2026-05-24
> **Estado:** Borrador en elaboración sección por sección
> **Brief de origen:** `docs/projects/apple-tech-blog/project-brief.md` v1.1
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




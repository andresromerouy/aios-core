# El Mate Digital — Product Requirements Document (PRD)

> **Documento:** PRD v0.1 (en construcción)
> **Autor:** Morgan (PM Agent) — AIOS
> **Fecha:** 2026-05-24
> **Estado:** Borrador en elaboración sección por sección
> **Brief de origen:** `docs/projects/apple-tech-blog/project-brief.md` v1.1
> **Idioma del producto:** Español-LATAM (léxico neutral compatible con España)

---

## 1. Goals and Background Context

### 1.1 Goals

- Lanzar un blog independiente en español-LATAM sobre Apple/tech con experiencia de lectura premium, móvil-first y velocidad top-tier (Core Web Vitals en p75 móvil).
- Construir autoridad editorial vía cobertura ágil + análisis con voz propia y contexto regional (LATAM + España), diferenciada de medios traductores/clickbait.
- Operar un panel super-admin propio que permita al dueño controlar el 100% del ciclo editorial (creación, programación, media, SEO, monetización) sin tocar código.
- Sostener una cadencia editorial mínima de **≥4 artículos/semana** durante los primeros 6 meses.
- Alcanzar **50.000 sesiones/mes** y **5.000 suscriptores de newsletter activos** al mes 12 post-lanzamiento.
- Generar **≥USD 1.000/mes de ingresos brutos** al mes 12 combinando AdSense, afiliados Amazon y sponsored.
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
- **FR24:** Cada artículo escrito con asistencia material de IA debe poder marcarse internamente desde admin y ese metadato debe quedar disponible (visible o auditable según se decida en UX).

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

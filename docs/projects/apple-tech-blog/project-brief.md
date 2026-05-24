# Project Brief: El Mate Digital

> **Documento:** Project Brief v1.1
> **Autor:** Atlas (Analyst Agent) — AIOS
> **Fecha:** 2026-05-24
> **Estado:** Validado por el dueño — listo para handoff a PM
> **Idioma del producto:** Español (es-LATAM como variante principal, con neutralidad léxica para España)
> **Nombre de trabajo:** El Mate Digital — identidad LATAM (ritual del mate) + foco Apple/tech
> **Dominio:** Por definir (candidatos: `elmatedigital.com` / `.tech` / `.lat`)

---

## Executive Summary

**El Mate Digital** es un blog independiente en español-LATAM dedicado a cubrir el ecosistema Apple y la actualidad tecnológica adyacente: noticias, rumores, análisis profundos, tutoriales y reseñas de producto. La metáfora del mate — ritual diario, compartido, cálido, sin apuros — guía el contraste con la cobertura tech tradicional: en lugar de titulares fríos al estilo *wire*, El Mate Digital ofrece lectura pausada, contexto regional y voz propia.

Inspirado en referentes como **La Manzana Mordida**, busca ofrecer cobertura ágil + análisis de calidad con una experiencia de lectura limpia, móvil-first y rápida.

El problema central a resolver es la **fragmentación y baja calidad editorial** que sufre el lector hispanohablante interesado en Apple: o consume medios generalistas con cobertura superficial, o recurre a fuentes en inglés. El blog se posiciona como **fuente confiable, ágil y monetizada de forma sana**, con tres pilares funcionales:

1. **Sitio público** optimizado para SEO y velocidad.
2. **Panel super-admin propio** para que el dueño controle 100% el contenido, publicación, media y configuración.
3. **Monetización en capas** (AdSense → afiliados Amazon → newsletter sponsored → membresía premium con Stripe) que escala con el tráfico.

La propuesta de valor: *"Apple en tu idioma, con tu ritmo. Mate de por medio."*

---

## Problem Statement

### Estado actual

- Los lectores hispanohablantes interesados en Apple consumen un mix de medios generalistas (Xataka, Genbeta, Applesfera), blogs específicos consolidados (La Manzana Mordida, iPadizate) y canales en YouTube/X.
- La cobertura suele estar dominada por **clickbait, traducciones directas de medios anglo y SEO oportunista** sin análisis propio.
- La experiencia móvil de varios referentes está **saturada de ads intrusivos** (interstitials, autoplay, layout shift), degradando la lectura.
- Los medios grandes priorizan el volumen sobre la profundidad: faltan tutoriales paso a paso, análisis de actualizaciones (iOS .x), y guías de compra contextualizadas para LATAM/España.

### Impacto del problema

- **Para el lector:** pierde tiempo filtrando ruido, paga con datos móviles ads pesados, no encuentra respuestas técnicas en español.
- **Para el ecosistema editorial:** la consolidación de pocos players sin competencia editorial fuerte estanca la calidad.
- **Oportunidad de mercado:** el público hispano de Apple crece (>650M hispanohablantes; Apple gana share en LATAM y España continúa madurando), pero la oferta de medios *independientes y bien hechos* es limitada.

### Por qué las soluciones existentes se quedan cortas

- Medios grandes optimizan para el modelo de display advertising programático masivo → conflicto de intereses con la experiencia del lector.
- Blogs solo-traducción no aportan voz editorial propia ni contexto regional (precios, disponibilidad, planes de operador).
- Newsletters de creadores individuales (Substack) cubren parcialmente pero sin la profundidad ni el SEO de un blog establecido.

### Urgencia

- **Ventana de adopción de Apple Intelligence en español** (2026): nuevas features llegando a Latinoamérica y España generan picos de búsqueda sin cobertura editorial profunda existente.
- **Saturación de plataformas centralizadas (Substack, Medium):** los lectores valoran nuevamente sitios independientes con identidad propia.
- **Cambios en SEO con IA generativa:** crear contenido evergreen + EEAT fuerte ahora es lo que rankeará en respuestas generativas en los próximos 2-3 años.

---

## Proposed Solution

### Concepto y enfoque

Un blog **headed-Next.js + Payload CMS** auto-hospedado, en español, con tres áreas funcionales conectadas:

1. **Front público** (Next.js 15 App Router con ISR): home con destacados/últimas, secciones por producto (iPhone, Mac, iPad, Watch, Vision, Servicios) y por tipo (Noticias, Rumores, Análisis, Tutoriales, Reseñas, Guías de compra), búsqueda, página de artículo con TOC + lecturas relacionadas, autor, comentarios, RSS, sitemap dinámico.
2. **Panel super-admin** (Payload CMS embebido en el mismo proyecto Next): editor rico con embeds, programación de publicación, drafts/versiones, gestión de categorías/tags/autores, media library con optimización automática, moderación de comentarios, configuración SEO por artículo, dashboard de métricas básicas.
3. **Capa de monetización modular**: slots de AdSense gestionables desde admin, sistema de enlaces afiliados Amazon con rastreo, doble opt-in de newsletter (Resend), gating de contenido premium con Stripe (fase 2).

### Diferenciadores clave

- **Voz editorial propia + contexto regional** (LATAM/España): no traducciones, análisis con criterio.
- **Experiencia de lectura premium**: Core Web Vitals top-tier (LCP <2s, CLS <0.05), tipografía cuidada, modo oscuro nativo, lectura cómoda en móvil.
- **Densidad de ads controlada**: máximo 2 slots above-the-fold + ads in-feed razonables. La opción premium remueve todos los ads.
- **Newsletter como producto, no afterthought**: dossier semanal curado, no resumen automático.
- **Transparencia editorial**: política clara de afiliados, separación visual entre contenido editorial y patrocinado.

### Por qué tendrá éxito

- El dueño es **el editor único** (al menos en MVP), lo que asegura coherencia de voz.
- Stack moderno permite **iteración rápida** y costos de operación bajos (<$50/mes hasta 100k pageviews/mes).
- Estrategia SEO **long-tail + topical authority** (clusters de contenido por producto/versión iOS) frente a competidores que priorizan trending.

### Visión de producto

A 24 meses: la referencia indispensable en español para entender qué pasa con Apple, con una comunidad de lectores fieles (newsletter >10k), una membresía premium sostenible (>500 miembros), y un brand reconocido en el nicho.

---

## Target Users

### Primary User Segment: Lector Apple Comprometido (“Power Reader”)

- **Perfil:** Hispano (España + LATAM), 25-45 años, profesional o estudiante, ingresos medios-altos, posee al menos 2 dispositivos Apple (iPhone + Mac/iPad/Watch).
- **Comportamiento actual:** Consume 3-5 fuentes de noticias tech diariamente, sigue cuentas Apple en X/YouTube, abre Reeder/Feedly, lee newsletters tech.
- **Pain points:**
  - “Quiero análisis, no titulares.”
  - “Estoy harto de ads que me sacan del artículo.”
  - “Me pierdo features de iOS por falta de tutoriales claros en español.”
  - “Antes de comprar quiero saber si vale la pena vs el modelo anterior y si está disponible en mi país.”
- **Goals:** Mantenerse al día sin saturarse, tomar mejores decisiones de compra, dominar mejor sus dispositivos, sentirse parte de una comunidad informada.

### Secondary User Segment: Curioso del Ecosistema (“Apple-Curious”)

- **Perfil:** Hispano, 18-35 años, considera entrar al ecosistema Apple (primer iPhone, primer Mac) o ya entró hace poco.
- **Comportamiento actual:** Busca en Google “mejor iPhone calidad precio”, mira reviews en YouTube, pregunta en Reddit/foros.
- **Pain points:**
  - No sabe qué modelo elegir, qué accesorios sirven, cómo migrar desde Android/Windows.
  - Encuentra info dispersa, contradictoria o desactualizada.
- **Goals:** Comprar con confianza, aprender lo básico rápido, evitar errores caros.

---

## Goals & Success Metrics

### Business Objectives

- **Alcanzar 50,000 sesiones/mes a los 12 meses** post-lanzamiento (combinando SEO + redes + newsletter).
- **Generar ≥$1,000 USD/mes en ingresos brutos al mes 12** (mix AdSense + afiliados Amazon + sponsored).
- **Construir lista de newsletter de 5,000 suscriptores activos al mes 12** (open rate ≥30%).
- **Publicar consistentemente:** ≥4 artículos/semana durante los primeros 6 meses.
- **Lanzar membresía premium en mes 9** con meta de 100 miembros pagos a los 12 meses.

### User Success Metrics

- **Tiempo medio en página > 2:30 min** en artículos largos (análisis, reseñas, tutoriales).
- **Bounce rate < 60%** en home y páginas de categoría.
- **Returning visitors ≥ 35%** del tráfico mensual al mes 9.
- **CTR newsletter ≥ 8%** y tasa de cancelación mensual < 1%.

### Key Performance Indicators (KPIs)

- **Pageviews/mes:** meta 150k en mes 12.
- **Sesiones orgánicas / total sesiones:** ≥ 65% (señal de salud SEO).
- **Core Web Vitals:** LCP < 2.0s, INP < 200ms, CLS < 0.05 en p75 móvil.
- **RPM (revenue per mille):** ≥ $4 USD combinado en mes 12.
- **Suscriptores newsletter / pageviews mensuales:** ≥ 3% (señal de engagement).
- **Conversion rate membresía premium:** ≥ 1% sobre lectores recurrentes en mes 12.
- **Coste operativo mensual de infra:** mantener < $80 USD hasta 100k pageviews/mes.

---

## MVP Scope

### Core Features (Must Have)

- **Publicación de artículos con editor rico:** soporte de Markdown/MDX o editor WYSIWYG (Payload Lexical), embeds de YouTube/X/tweet/code blocks, imágenes responsive con optimización.
- **Categorización y taxonomía:** categorías (producto: iPhone, Mac, iPad, Watch, Vision, Servicios) + tipos de contenido (noticia, rumor, análisis, tutorial, reseña, guía) + tags libres.
- **Páginas core:** Home (hero + destacados + últimas + por categoría), página de artículo (con TOC, autor, fecha, lectura relacionada, share), página de categoría, página de tag, página de autor, búsqueda básica, página About, Política de privacidad, Aviso legal, Política de afiliados.
- **SEO técnico:** sitemap.xml dinámico, robots.txt, canonical, Open Graph + Twitter Cards, schema.org Article+BreadcrumbList+Person, RSS feed por categoría y global, URL slugs editables, meta-description y meta-title editables por artículo.
- **Performance:** ISR con revalidación, optimización de imágenes (next/image o Cloudinary), fonts auto-hospedados, code splitting, prefetch inteligente.
- **Panel super-admin (Payload):** auth con email+password + 2FA, gestión de artículos (draft/scheduled/published), media library, gestión de usuarios (al inicio solo super-admin), gestión de categorías y tags, programación de publicación, preview de drafts.
- **Newsletter (doble opt-in):** integración Resend, formulario en sidebar y footer, página de archivo de newsletter, gestión de listas desde admin.
- **AdSense:** integración con slots dinámicos configurables (header, in-feed home, in-article cada N párrafos, sidebar, footer). Toggle on/off por artículo.
- **Afiliados Amazon básico:** sistema de shortlinks con tracking de clicks, disclaimer automático en artículos con afiliados.
- **Analytics:** Vercel Analytics + Plausible (sin cookies) o GA4 según preferencia.
- **Modo claro/oscuro** persistido en cliente.
- **i18n-ready** (aunque MVP solo español) — estructura preparada para futura expansión.
- **Infraestructura premium pre-instalada (no activada):** modelos de datos (`Subscription`, `Plan`, `Member`), gating helper `requiresMembership(post)`, integración Stripe stubbed con feature flag `PREMIUM_ENABLED=false`. Permite activar la membresía sin migraciones ni refactors cuando el tráfico/timing lo justifiquen.
- **Política editorial de uso de IA (visible públicamente):** página `/etica-editorial` declarando que la IA se usa como herramienta asistente (brainstorming, primer borrador, edición, traducción) pero **nunca como autor final**: cada artículo es revisado, corregido y firmado por un humano. Compromiso explícito: cero contenido publicado sin pasada editorial humana.

### Out of Scope for MVP

- **Activación de membresía premium** (la infra queda lista pero el checkout, gating activo y plan público se difieren a Fase 2, mes 6-9).
- **Comentarios públicos** — se difieren a Fase 2 para evitar carga de moderación sobre el fundador único y reducir superficie de spam/abuso durante la fase de bootstrapping editorial. Mientras tanto, se habilita engagement vía respuestas por email a la newsletter y menciones en redes.
- App móvil nativa (no es prioridad — PWA bien hecha es suficiente).
- Foros / comunidad estilo Discourse.
- Podcast (puede vincularse pero no producirse desde la plataforma).
- Multi-autor con flujo editorial complejo (revisión por pares) — para fase 3.
- Multi-idioma (inglés, portugués) — fase 3+.
- Ofertas/cuponera (estilo Chollometro) — posible expansión.
- Sistema de votación/karma estilo Hacker News.
- AdServer propio (Google Ad Manager, Mediavine) — recién cuando se cumplan thresholds de tráfico.
- Newsletter sponsored marketplace automatizado — manual al inicio.
- Sistema de notificaciones push web.

### MVP Success Criteria

El MVP se considera exitoso cuando:

1. Se publican consistentemente ≥3 artículos/semana durante 8 semanas seguidas sin fricciones en el flujo editor → publicación.
2. El sitio alcanza Lighthouse ≥90 en Performance/SEO/Accessibility en móvil.
3. Se alcanzan 5,000 sesiones/mes orgánicas (señal de que la base SEO funciona).
4. AdSense aprueba la cuenta y se monetizan los primeros $100.
5. La lista de newsletter llega a 500 suscriptores con open rate >35%.
6. El admin permite al dueño operar todo el ciclo editorial sin tocar código durante 30 días seguidos.

---

## Post-MVP Vision

### Phase 2 Features (Mes 6-12)

- **Activación de membresía premium con Stripe:** flip del flag `PREMIUM_ENABLED`, plan mensual + anual, gating de contenido premium (análisis exclusivos, newsletter VIP, sin ads). La infra ya está en MVP, en Fase 2 se activa UI pública + checkout + comunicación.
- **Sistema de comentarios v1:** sistema nativo en Payload con moderación + filtro anti-spam (Akismet/honeypot), políticas claras de conducta, posible requerimiento de cuenta para reducir abuso. Activación condicionada a tráfico ≥10k sesiones/mes (señal de audiencia que justifica el costo de moderación).
- **Newsletter sponsored builder:** template para enviar dossier semanal con slots de patrocinio gestionables.
- **Mejoras de SEO:** topic clusters automáticos, internal linking sugerido por IA, schema FAQ/HowTo, AMP opcional.
- **Sistema de "colecciones"/series** (ej: "Todo sobre iOS 19").
- **Lectura guardada / favoritos** para usuarios registrados (lectores).
- **Migración o adición a Mediavine/Ezoic** si tráfico ≥50k sesiones/mes.

### Long-term Vision (12-24 meses)

- **Multi-autor con flujo editorial completo** (roles: editor, autor, contributor, moderador).
- **Expansión de contenido:** podcast embebido + transcripciones, vídeo corto, directo en eventos Apple.
- **Multi-idioma:** inglés y/o portugués (clonar contenido evergreen + cobertura cruzada).
- **Comunidad propia:** foros ligeros tipo Discourse para miembros premium.
- **App PWA pulida** con offline reading y notificaciones push opt-in.
- **Marketplace de afiliados curado** (no solo Amazon: tiendas locales, refurbished, etc.).

### Expansion Opportunities

- **Verticales adyacentes** bajo el mismo paraguas: gaming Apple (Mac/Vision), productividad para creators, salud Apple (Watch).
- **Eventos / meetups** locales para miembros premium.
- **Cursos digitales** (Apple Intelligence en español, dominio de Shortcuts, etc.).
- **Sponsored content network**: ofrecer la audiencia a marcas de accesorios premium (boutique).

---

## Technical Considerations

### Platform Requirements

- **Target Platforms:** Web (responsive). Móvil-first (≥70% del tráfico esperado en móvil).
- **Browser/OS Support:** evergreens (Safari 16+, Chrome/Edge 110+, Firefox 110+). iOS Safari como referencia prioritaria.
- **Performance Requirements:**
  - LCP p75 móvil < 2.0s
  - INP p75 < 200ms
  - CLS p75 < 0.05
  - Time to First Byte < 600ms (favorecido por ISR + edge)
  - Lighthouse Performance ≥ 90 (móvil)

### Technology Preferences

- **Frontend:** **Next.js 15 (App Router)** + **TypeScript** + **Tailwind CSS** + **shadcn/ui** + Lucide icons.
- **Backend / CMS:** **Payload CMS 3.0** (corre dentro del mismo proyecto Next.js, comparte server actions y DB).
- **Database:** **PostgreSQL en Neon** (serverless, branching gratuito, plan free generoso). Alternativa: Supabase si se quiere RT/auth extra.
- **Hosting/Infrastructure:** **Vercel** (frontend + serverless functions + ISR + edge). Media en **Vercel Blob** o **Cloudinary** (preferencia: Cloudinary por mejor optimización on-the-fly).
- **Email:** **Resend** para transaccional + newsletter.
- **Payments (Fase 2):** **Stripe** (Checkout + Customer Portal + Webhooks).
- **Analytics:** **Vercel Analytics** + **Plausible** (sin cookies, GDPR-friendly).
- **Search:** Postgres FTS para MVP, evaluar **Algolia / Meilisearch** si crece.
- **Ads:** Google AdSense para MVP. Mediavine/Ezoic en Fase 3.

### Architecture Considerations

- **Repository Structure:** Repo nuevo dedicado, no monorepo dentro de aios-core. Single-app Next.js con Payload embebido (no microservicios). Estructura sugerida: `app/(public)`, `app/(payload)`, `collections/`, `lib/`, `components/`.
- **Service Architecture:** Monolito modular sobre Next.js + Payload. Servicios externos vía SDK (Resend, Stripe, Cloudinary, AdSense). Edge functions para tareas ligeras (redirects, ads serving).
- **Integration Requirements:**
  - Resend (newsletter + transaccional)
  - AdSense (script + slots)
  - Amazon Associates (link shortener + tracking)
  - Plausible/Vercel Analytics
  - Stripe (SDK + webhooks instalados desde MVP, activación Fase 2)
  - Akismet o equivalente (anti-spam comentarios — Fase 2 cuando se habiliten comentarios)
- **Security/Compliance:**
  - HTTPS forzado (Vercel default).
  - **GDPR + LOPDGDD (España) + LFPDPPP (México) / Habeas Data (Argentina)**: política de privacidad clara, cookie banner si se usan cookies de tracking, derecho al olvido en comentarios y newsletter.
  - **Cookie banner solo si necesario** (Plausible no requiere, AdSense personalizado sí).
  - **2FA obligatorio** para super-admin.
  - **Rate-limiting** en login admin, comentarios y newsletter signup.
  - **CSP headers** estrictos.
  - Backups automáticos diarios de DB (Neon lo cubre).
  - **DSAR endpoint** para solicitudes de datos personales.

---

## Constraints & Assumptions

### Constraints

- **Budget:** Bootstrap. Objetivo: operar primer año con < $80/mes en infra (Vercel Pro $20 + Neon $0-19 + Cloudinary $0-49 + Resend $0-20 + dominio). Servicios “premium” solo cuando ingresos lo justifiquen.
- **Timeline:** MVP en **8-12 semanas** desarrollando part-time. Lanzamiento soft seguido de iteración continua.
- **Resources:** Equipo de 1 (dueño = editor + dev + ops) en MVP. Posibilidad de freelance puntual para diseño/ilustración.
- **Technical:**
  - Sin equipo de DevOps → todo gestionado (Vercel + Neon).
  - Sin equipo legal → templates de política revisados por LLM + abogado puntual.
  - Sin infra de email transaccional propia → dependencia de Resend (acotada, no crítica).

### Key Assumptions

- El dueño puede mantener cadencia editorial de ≥4 posts/semana durante los primeros 6 meses.
- El tráfico orgánico crece de forma escalonada (no requiere campañas pagas grandes para alcanzar 50k sesiones).
- AdSense aprobará la cuenta en los primeros 3 meses (requisitos: contenido original, política de privacidad, About, sin política de monetización violada).
- Amazon Associates aprobará la cuenta tras las primeras 3 ventas en período de prueba.
- El mercado hispanohablante mantendrá interés alto en Apple (asumimos que Apple sigue lanzando productos relevantes anualmente).
- Payload 3.0 mantendrá estabilidad y dirección de producto compatible con Next.js durante los próximos 18 meses.
- Vercel mantendrá pricing razonable para tráfico orgánico de blog (no hay riesgo inminente de migración a self-hosted).

---

## Risks & Open Questions

### Key Risks

- **Saturación del nicho Apple en español:** competidores establecidos con dominio autorítario alto → **impacto:** SEO inicial lento. **Mitigación:** topical authority en sub-nichos (Apple Intelligence, Vision Pro, Apple en LATAM).
- **Burnout editorial del fundador único:** publicar 4x/semana solo es agotador → **impacto:** caída de cadencia ⇒ caída SEO. **Mitigación:** templates de noticias rápidas + colaboradores ocasionales + IA asistida (no generada al 100%) para borradores.
- **Dependencia de AdSense como ingreso inicial:** CPMs bajos en español + posibles suspensiones de cuenta → **impacto:** ingresos volátiles. **Mitigación:** diversificar pronto a afiliados + sponsored + premium.
- **Cambios en SEO por respuestas generativas (Google AI Overviews):** menos clicks orgánicos → **impacto:** caída de tráfico. **Mitigación:** invertir en brand + newsletter + community = audiencias propias.
- **Penalización por contenido duplicado/IA:** si se publican demasiados rewrites → **impacto:** sandbox / penalización. **Mitigación:** política editorial estricta de aporte propio en cada artículo.
- **Costos de infra escalando con tráfico:** Vercel y Cloudinary pueden subir rápido > 100k pageviews/mes → **impacto:** márgenes ajustados. **Mitigación:** cachés agresivas, CDN propio si hace falta, evaluar self-host en VPS si supera umbral económico.
- **Legal/GDPR:** cookies AdSense + tracking afiliados sin consentimiento bien gestionado → **impacto:** multa. **Mitigación:** CMP (Cookie Management Platform) certificada desde día 1.
- **Lock-in de Payload:** si Payload pivota o cambia licencia → **impacto:** migración costosa. **Mitigación:** mantener data en PostgreSQL estándar (Payload no es propietario del schema, podés exportar siempre).

### Resolved (decisiones del dueño, v1.1)

- ✅ **Nombre de trabajo:** **El Mate Digital** (LATAM + Apple). Dominio por confirmar.
- ✅ **Idioma:** español-LATAM como variante principal, con léxico neutral evitando coloquialismos que aíslen a España.
- ✅ **Política de uso de IA:** asistencia sí (brainstorming, primer borrador, edición, traducción); generación autónoma no. Cada artículo lo firma y revisa un humano. Esta política se publica en `/etica-editorial`.
- ✅ **Comentarios:** diferidos a Fase 2 (mes 6-9) cuando haya audiencia y costos de moderación se justifiquen. En MVP no se exponen.
- ✅ **Membresía premium:** infra preparada en MVP (collections + Stripe SDK + feature flag), activación pública diferida a Fase 2.
- ✅ **Cadencia editorial MVP:** ≥4 artículos/semana sostenidos durante 6 meses.

### Open Questions

- ¿Dominio final? Validar disponibilidad: `elmatedigital.com` vs `.tech` vs `.lat` vs combinaciones (`mate.tech`, `tomateundigital.com`).
- ¿Diseño visual? Branding sprint corto: paleta (apple-blanco + verde-mate + acento cálido), tipografía editorial (¿New York / Söhne / IBM Plex Serif?), logo (mate estilizado + manzana sutil).
- ¿Se acepta sponsored content desde el inicio o se espera a tener audiencia? Recomendación: esperar a 10k sesiones/mes.
- ¿Cómo se elige el slug de URLs? (¿Con fecha o sin fecha? Recomendado: sin fecha, evergreen.)
- ¿Frecuencia y formato de newsletter? (Semanal viernes parece sweet spot — confirmar nombre: ¿"El Mate del Viernes"?)
- ¿Lanzar PWA desde MVP o esperar?

### Areas Needing Further Research

- **Análisis competitivo profundo de La Manzana Mordida, Applesfera, iPadizate, Faq-Mac, Soy de Mac** (features visibles, monetización aparente, frecuencia, voz editorial, gaps detectables).
- **Análisis de keywords**: SEO research para identificar los clusters de mayor oportunidad por producto + intención.
- **Estudio de stacks**: validar Payload 3.0 vs alternativas (Sanity, Strapi, custom) con prototipo rápido si hay dudas serias.
- **Investigación de monetización**: CPMs reales en AdSense en español 2026, comisiones Amazon Associates LATAM/España.
- **Investigación legal**: requisitos exactos de CMP, política de cookies, declaraciones de afiliados en jurisdicciones objetivo.
- **Investigación de marca**: naming, paleta visual, tipografía editorial.

---

## Appendices

### A. Research Summary

Pendiente. Recomendado ejecutar antes de PRD:

- `*create-competitor-analysis` sobre La Manzana Mordida + 3 referentes adicionales.
- `*perform-market-research` sobre el mercado hispanohablante de medios tech / Apple.

### B. Stakeholder Input

- **Owner (dueño del proyecto):** Confirmó stack (Next.js + Payload), preferencia por repo separado, 3 pilares (público + admin + monetización), referente principal La Manzana Mordida.
- **Decisiones validadas en sesión 2026-05-24 (v1.1):**
  - Nombre de trabajo **El Mate Digital** (identidad LATAM + Apple).
  - Política IA: asistida + transparente, declarada públicamente.
  - Premium: infra en MVP, activación pública en Fase 2.
  - Comentarios: diferidos a Fase 2.
- Pendiente capturar: visión editorial detallada (voz/tono), restricciones temporales semanales del dueño para sostener cadencia 4/sem, identidad visual.

### C. References

- La Manzana Mordida — https://lamanzanamordida.net (referente principal)
- Payload CMS — https://payloadcms.com
- Next.js — https://nextjs.org
- Neon — https://neon.tech
- Resend — https://resend.com
- Cloudinary — https://cloudinary.com
- AdSense Program Policies — https://support.google.com/adsense/answer/48182
- Amazon Associates — https://afiliados.amazon.es / https://afiliados.amazon.com.mx
- Google Search Essentials — https://developers.google.com/search/docs/essentials

---

## Next Steps

### Immediate Actions

1. ~~**Validar este Project Brief** con el dueño~~ ✅ Hecho (v1.1).
2. **Validar disponibilidad de dominio** para `elmatedigital.com` y variantes.
3. **Branding sprint corto** (paleta, tipografía, logo concept mate+manzana).
4. **Disparar competitor analysis** (`*create-competitor-analysis` con La Manzana Mordida + 3 referentes).
5. **Disparar market research** (`*perform-market-research` sobre mercado hispano de medios tech/Apple).
6. **➡️ Handoff a PM (@pm / Morgan)** para generar el PRD a partir de este brief.
7. **Handoff a Architect (@architect)** post-PRD para diseñar arquitectura técnica de detalle.
8. **Crear repo nuevo** y registrar dominio cuando se confirme.

### PM Handoff

Este Project Brief proporciona el contexto completo para **El Mate Digital**. Por favor, inicie en **'PRD Generation Mode'**, revise el brief detalladamente y trabaje con el dueño para crear el PRD sección por sección según indique el template, solicitando aclaraciones o sugiriendo mejoras donde corresponda.

**Decisiones del dueño ya cerradas (no re-litigar salvo nueva información):**
- Nombre: **El Mate Digital**.
- Política IA: asistida + transparente.
- Premium: infra en MVP, activación Fase 2.
- Comentarios: Fase 2.
- Cadencia: ≥4 artículos/semana.

Focos críticos para el PRD:

- Detallar **funcional + UX** del panel super-admin (es el diferencial clave para el dueño).
- Definir **flujos de monetización end-to-end** (configuración → entrega → tracking → reporte).
- Especificar **modelo de datos editorial** (artículo, categoría, tag, autor, suscriptor, newsletter, afiliado, ad-slot, plus stubs para `Member`/`Subscription`/`Plan` de premium).
- Definir el **shape exacto del feature flag `PREMIUM_ENABLED`** y qué piezas se exponen en MVP (data models, helpers, SDK) vs. lo que sólo se enciende en Fase 2 (UI pública de planes, checkout, gating activo, gestión post-pago).
- Detallar **política editorial / página `/etica-editorial`**: declaración de uso de IA, política de afiliados, política de correcciones, autoría.
- Definir **épicas** y secuencia de entrega del MVP en sprints de 1-2 semanas.

---

*— Atlas, investigando a verdade 🔎*

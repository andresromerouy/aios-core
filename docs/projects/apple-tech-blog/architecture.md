# El Mate Digital — Fullstack Architecture Document

> **Documento:** Architecture v1.0
> **Autor:** Aria (Architect Agent) — AIOS
> **Fecha:** 2026-05-24
> **Estado:** ✅ Cerrado y listo para handoff a @sm + @dev
> **Insumos:** PRD v1.0 (`docs/projects/apple-tech-blog/prd.md`) · Brief v1.2 (`docs/projects/apple-tech-blog/project-brief.md`)
> **Tipo de proyecto:** Greenfield · monolito modular Next.js + Payload

---

## 1. Introducción

Este documento es el plano técnico definitivo de **El Mate Digital**. Traduce las 27 FR + 24 NFR + 6 épicas del PRD a una arquitectura ejecutable: stack versionado, modelo de datos completo, contratos de API, estrategia de cache/seguridad/observabilidad, y estructura de carpetas. Es el insumo del que se desprenden todas las decisiones de implementación en Epic 1-6.

### 1.1 Starter Template

**N/A — Greenfield.** No se usa starter template ni boilerplate de terceros. El scaffolding (Story 1.1) parte de `npx create-next-app@latest` + `pnpm create payload-app` siguiendo la guía oficial de Payload 3.0 para integración Next.js App Router.

### 1.2 Change Log

| Date       | Version | Description                                                | Author |
| ---------- | ------- | ---------------------------------------------------------- | ------ |
| 2026-05-24 | 1.0     | Arquitectura inicial completa — handoff PM → Architect    | Aria   |

---

## 2. High Level Architecture

### 2.1 Technical Summary

**El Mate Digital** se construye como un **monolito modular serverless** sobre Vercel: una única app Next.js 15 (App Router) con Payload CMS 3.0 embebido que comparte runtime, base de datos y deploy. El frontend público usa **server components con ISR + on-demand revalidate** para combinar rendimiento estático con frescura editorial; el admin es la app de Payload renderizada bajo `/admin` con su propio middleware de auth + 2FA TOTP. Los datos persisten en **PostgreSQL Neon** vía adapter Drizzle. Media servida desde **Cloudinary** con transformación on-the-fly. Email transaccional + broadcasts vía **Resend**. Pagos preparados con Stripe SDK detrás de feature flag `PREMIUM_ENABLED`. Jobs programados gestionados por **Vercel Cron Jobs**. Observabilidad combinada: Sentry (errores), Vercel Speed Insights (RUM), Plausible (analytics). Esta arquitectura logra los 50.000 sesiones/mes con costo <USD 80/mes (NFR4) y mantiene Core Web Vitals p75 móvil en LCP <2s / INP <200ms / CLS <0.05 (NFR1).

### 2.2 Platform and Infrastructure Choice

- **Plataforma:** **Vercel** (Hobby al inicio, upgrade a Pro cuando hagamos falta de Cron Jobs adicionales o más bandwidth).
- **Servicios clave:**
  - **Vercel:** hosting + edge runtime + serverless functions + ISR + Cron Jobs + Speed Insights + Logs.
  - **Neon:** PostgreSQL serverless + branching (gratis para preview deploys).
  - **Cloudinary:** media storage + transformación + CDN.
  - **Resend:** email transaccional + broadcasts.
  - **Stripe:** SDK + webhooks (stubbed en MVP).
  - **Upstash Redis:** rate-limiting distribuido (free tier ~10k req/día).
  - **Sentry:** error tracking (plan free para MVP).
  - **Plausible:** analytics público (es-LATAM GDPR-friendly, sin cookies).
- **Regiones:**
  - Vercel: `iad1` (US East, default) y `gru1` (São Paulo) para edge functions servidas desde LATAM.
  - Neon: región más cercana al target — sugerencia **`aws-us-east-2`** (Ohio) o **`aws-sa-east-1`** (São Paulo) según latencia medida. Decisión final al provisionar.
  - Cloudinary: CDN global automático.

### 2.3 Repository Structure

- **Estructura:** **Polyrepo / single Next.js app** — un repositorio dedicado (sugerido `el-mate-digital`), **NO monorepo**.
- **Tooling:** `pnpm` como package manager (lockfile rápido + workspaces si en el futuro se agrega un package de tipos compartido).
- **Rationale (override al template):** El proyecto tiene **un único deployable**. El template del Architect sugiere monorepo, pero acá Payload corre embebido en Next.js (no es un servicio separado). No hay packages que compartir entre frontend y backend porque comparten runtime. Monorepo (Turborepo/Nx) sería overengineering puro. Si en Fase 3+ se separa un servicio (ej: workers), recién ahí evaluar la migración a monorepo.

### 2.4 High Level Architecture Diagram

```mermaid
graph TB
    subgraph "Usuarios"
        U1[Lector Web Móvil]
        U2[Lector Web Desktop]
        U3[Suscriptor Newsletter]
        U4[Admin / Dueño]
    end

    subgraph "Edge & CDN"
        VCDN[Vercel Edge CDN]
        CCDN[Cloudinary CDN]
    end

    subgraph "Vercel — Next.js 15 App"
        FE["Public Frontend
        Server Components + ISR"]
        ADM["/admin
        Payload UI"]
        API["/api/* Routes
        Server-side handlers"]
        CRON["Vercel Cron
        scheduled-publish
        newsletter-send"]
    end

    subgraph "Datos"
        NEON[(Neon PostgreSQL)]
        CLD[Cloudinary Media]
        UPS[(Upstash Redis
        rate-limit)]
    end

    subgraph "Servicios Externos"
        RES[Resend
        Email]
        STR[Stripe
        Pagos — Fase 2]
        SEN[Sentry
        Errores]
        PLA[Plausible
        Analytics]
        ADS[Google AdSense]
        AMZ[Amazon Associates]
    end

    U1 --> VCDN
    U2 --> VCDN
    U3 --> VCDN
    U4 -->|/admin| ADM
    VCDN --> FE
    VCDN --> API
    FE -->|imágenes| CCDN
    CCDN --> CLD

    FE --> NEON
    ADM --> NEON
    API --> NEON
    API --> UPS
    API --> RES
    API --> STR
    CRON --> API
    CRON --> RES

    FE -.->|RUM| SEN
    FE -.->|Page views| PLA
    ADM -.->|Errors| SEN
    API -.->|Errors| SEN

    FE -->|Script async| ADS
    FE -->|Shortlink /go/| AMZ
```

### 2.5 Architectural Patterns

- **Modular Monolith:** Una única codebase Next.js + Payload organizada en módulos por dominio (posts, newsletter, monetization, premium). _Rationale:_ Simpler operations, atomic deploys, sin overhead de comunicación entre servicios, encaja con escala MVP (<100k pv/mes).
- **Server Components-First:** Next.js 15 App Router con server components por default; client components solo donde hace falta interactividad (theme toggle, search overlay, signup form, share buttons). _Rationale:_ Bundle JS mínimo, mejor TTI, SEO-friendly por SSR/SSG nativo.
- **ISR + On-Demand Revalidate:** Páginas públicas (`/`, `/articulo/[slug]`, `/categoria/[slug]`, etc.) generadas estáticamente con revalidación cada 60-300s + hooks de Payload disparan revalidación on-publish. _Rationale:_ Páginas servidas como estáticas (LCP <2s en p75 móvil) pero contenido fresco al publicar.
- **Repository Pattern (vía Payload):** Acceso a datos siempre vía `payload.find/findByID/create/update` — nunca SQL crudo desde frontend. _Rationale:_ Validación + auth + hooks centralizados; portabilidad si en el futuro migramos de Drizzle/Postgres.
- **Feature Flag Pattern:** Premium gobernado por `PREMIUM_ENABLED` (env var) + helper `requiresMembership()` que centraliza la lógica. _Rationale:_ Activar Fase 2 sin migraciones; testabilidad de ambos modos.
- **Edge Middleware para CSP + Rate-Limit + Geo:** Middleware Next.js intercepta requests para inyectar nonce CSP, aplicar rate-limit vía Upstash, detectar región para CMP. _Rationale:_ Defense in depth en el borde de la red, antes de tocar serverless.
- **Webhook + Event-Driven Light:** Stripe webhooks + Resend bounces → endpoints API que actualizan estado en DB. _Rationale:_ Loose coupling con proveedores externos; cero polling.
- **Adapter Pattern para Media:** `MediaAdapter` con implementación `CloudinaryAdapter` (default) intercambiable. _Rationale:_ Cambiar de proveedor (futuro Vercel Blob/S3) sin reescribir collections.

---

## 3. Tech Stack

> **Esta tabla es la única fuente de verdad de tecnologías y versiones.** Toda PR que cambie estas versiones requiere actualización explícita acá + aprobación.

| Category | Technology | Version | Purpose | Rationale |
|---|---|---|---|---|
| Lenguaje (full-stack) | **TypeScript** | 5.6+ | Strict mode en todo el proyecto | Type safety end-to-end; obligatorio por PRD NFR19 |
| Runtime | **Node.js** | 20 LTS | Runtime serverless en Vercel | LTS estable, compatible Payload 3.0 |
| Framework | **Next.js** | 15.x | App Router + RSC + ISR + middleware | PRD constraint |
| UI Library | **React** | 19.x | Componentes + RSC | Viene con Next.js 15 |
| CMS | **Payload CMS** | 3.x | Headless CMS embebido en Next.js | PRD constraint; Lexical editor + Drizzle |
| ORM | **Drizzle ORM** | embedded en Payload | Acceso a DB via Payload | Incluido en `@payloadcms/db-postgres` |
| DB | **PostgreSQL** (Neon) | 16.x | Persistencia principal | Serverless + branching + free tier |
| File Storage | **Cloudinary** | latest SDK | Media library + transform on-the-fly | Decisión confirmada por dueño |
| Email | **Resend** | latest SDK | Transactional + broadcasts | PRD constraint |
| Payments | **Stripe** | latest SDK | Subscriptions (Fase 2, stubbed en MVP) | PRD constraint |
| Cache distribuido | **Upstash Redis** | latest SDK | Rate-limiting (`@upstash/ratelimit`) | Free tier 10k req/día |
| Error Tracking | **Sentry** | `@sentry/nextjs` 8.x | Captura excepciones + breadcrumbs | Defensa baseline (Story 1.9) |
| RUM / CWV | **Vercel Speed Insights** | latest | Core Web Vitals reales | NFR22 |
| Analytics | **Plausible** | latest script | Pageviews + referrers + outbound | GDPR-friendly, sin cookies (NFR12-13) |
| CSS | **Tailwind CSS** | 3.4+ | Utility-first + dark mode | PRD constraint |
| Component lib | **shadcn/ui** | latest CLI | Componentes base accesibles (Radix) | PRD constraint |
| Iconos | **Lucide React** | latest | Set de íconos | PRD constraint |
| Fuentes | **next/font** | nativo | Auto-host + zero CLS | NFR1 |
| Logger | **Pino** | 9.x | Logger estructurado JSON | NFR21 |
| Validación | **Zod** | 3.x | Validación de payloads en API routes | Type-safe, comparte tipos con TS |
| API style | **Next.js Route Handlers + Payload REST** | 15.x | REST mixto: Payload auto + custom routes | Pragmático para CMS-driven app |
| Frontend Testing | **Vitest** | 1.x | Unit tests | PRD Section 4.3 |
| Component Testing | **@testing-library/react** | 16.x | Tests de componentes React | Convención estándar |
| Backend Testing | **Vitest** | 1.x | Unit tests de lib/, collections/ | Mismo runner que frontend |
| E2E Testing | **Playwright** | 1.x | Tests críticos end-to-end | NFR20 |
| Build / Bundler | **Turbopack** (Next dev) + **webpack** (Next build) | nativo | Build oficial Next 15 | No swap |
| Package Manager | **pnpm** | 9.x | Install rápido + workspaces ready | Estándar moderno |
| CI/CD | **GitHub Actions** + **Vercel Git Integration** | latest | Lint/typecheck/test en PR + deploy auto | Story 1.4 + 1.5 |
| Linting | **ESLint** | 9.x flat config | Calidad + reglas Next 15 | NFR19 |
| Format | **Prettier** | 3.x | Formato consistente | NFR19 |
| Git hooks | **Husky** + **lint-staged** | latest | Pre-commit lint+format | Story 1.1 opcional |
| Cron | **Vercel Cron Jobs** | nativo | Scheduled publish + newsletter send | Decisión confirmada |
| TOTP / 2FA | **otplib** | 12.x | TOTP para Payload auth (Story 1.6) | Estándar RFC 6238 |
| Slugify | **github-slugger** | latest | Slugs evergreen | Maneja unicode + es-LATAM |
| Sanitization | **DOMPurify** (server) | 3.x | Sanitizar HTML de Lexical | Defensa XSS NFR10 |
| Sintaxis código | **Shiki** | 1.x | Highlighting bloques `code` | Server-side, zero JS shipped |
| Schema validation runtime | **valibot** o **zod** | latest | Validación de body Lexical | Decisión libre del dev, recomendado Zod |

---

## 4. Data Models

> _16 collections de Payload + sus relaciones. Cada collection se documenta con su propósito, atributos clave y una interfaz TypeScript que la representa (auto-generable por Payload). Las relaciones se diagraman al final._

### 4.1 ERD Overview

```mermaid
erDiagram
    Users ||--o{ Posts : "creates (audit)"
    Users ||--|| Members : "may have (Fase 2)"

    Authors ||--o{ Posts : "writes"
    Categories ||--o{ Posts : "classifies"
    ContentTypes ||--o{ Posts : "typifies"
    Tags }o--o{ Posts : "tags"
    Media ||--o{ Posts : "featuredImage"
    Media ||--o{ Authors : "avatar"

    Posts ||--o{ AffiliateLinks : "may reference"

    Subscribers ||--o{ Newsletters : "receives (sent broadcasts)"
    Newsletters ||--|| Users : "createdBy"

    Plans ||--o{ Subscriptions : "instantiated as"
    Members ||--o{ Subscriptions : "owns"
    Members ||--|| Users : "is"

    StaticPages }o--|| Users : "lastEditedBy"

    AdSlots ||--|| Users : "lastModifiedBy"

    AuditLogs }o--|| Users : "actor"
```

### 4.2 Collections

#### 4.2.1 Users

Built-in Payload `users` collection con extensiones para 2FA.

**Purpose:** Autenticación admin (super-admin único en MVP; multi-rol preparado para Fase 3).

**Atributos clave:**
- `email` (text, único, requerido) — login
- `password` (managed by Payload, hashed)
- `role` (select: `super-admin`/`editor`/`author` — solo `super-admin` activo en MVP)
- `totpSecret` (text, encrypted) — secret TOTP
- `totpEnabled` (checkbox) — flag de enrolamiento completado
- `totpBackupCodes` (array of `{code: string, used: boolean}`) — 10 códigos
- `failedLoginAttempts` (number) — rate-limiting auxiliar
- `lockedUntil` (date) — lock temporal tras N intentos fallidos
- `createdAt`, `updatedAt` (auto)

```typescript
interface User {
  id: string;
  email: string;
  role: 'super-admin' | 'editor' | 'author';
  totpEnabled: boolean;
  totpBackupCodes: { code: string; used: boolean }[];
  failedLoginAttempts: number;
  lockedUntil: Date | null;
  createdAt: Date;
  updatedAt: Date;
}
```

**Relations:**
- 1-to-1 opcional con `Members` (Fase 2).

#### 4.2.2 Categories

**Purpose:** Taxonomía por producto Apple (FR5).

**Atributos clave:**
- `name` (text, requerido) — "iPhone", "Mac", etc.
- `slug` (text, único) — auto-generado, editable
- `description` (textarea) — para hero de la página de listado
- `order` (number) — ordenar visualmente en navigation
- `seoMeta` (group) — `metaTitle`, `metaDescription`

```typescript
interface Category {
  id: string;
  name: string;
  slug: string;
  description?: string;
  order: number;
  seoMeta?: { metaTitle?: string; metaDescription?: string };
  createdAt: Date;
  updatedAt: Date;
}
```

**Seed inicial:** 6 categorías (`iphone`, `mac`, `ipad`, `watch`, `vision`, `servicios`).

#### 4.2.3 ContentTypes

**Purpose:** Tipos de contenido (FR5).

**Atributos clave:** mismos que Categories (`name`, `slug`, `description`, `order`).

**Seed inicial:** 6 tipos (`noticia`, `rumor`, `analisis`, `tutorial`, `resena`, `guia-de-compra`).

#### 4.2.4 Tags

**Purpose:** Tags libres (FR5).

**Atributos clave:** `name`, `slug`.

**Sin seed inicial** — se crean inline desde el editor de Post.

#### 4.2.5 Authors

**Purpose:** Autores como entidad propia (Story 2.2).

**Atributos clave:**
- `name`, `slug`
- `bio` (richText)
- `avatar` (relación a `Media`)
- `email` (text, opcional, no público)
- `socialLinks` (array de `{platform: 'twitter'|'x'|'mastodon'|'linkedin'|'github'|'instagram'|'web', url: string}`)
- `seoMeta` (group)

```typescript
interface Author {
  id: string;
  name: string;
  slug: string;
  bio?: RichTextLexical;
  avatar?: Media;
  email?: string;
  socialLinks?: { platform: string; url: string }[];
  seoMeta?: { metaTitle?: string; metaDescription?: string };
  createdAt: Date;
  updatedAt: Date;
}
```

**Seed inicial:** 1 autor (dueño).

#### 4.2.6 Media

**Purpose:** Media library con storage Cloudinary (Story 2.3).

**Atributos clave:**
- `filename` (auto)
- `mimeType` (auto)
- `cloudinaryPublicId` (text) — el `public_id` de Cloudinary
- `cloudinaryUrl` (text) — URL base
- `width`, `height` (number) — dimensiones originales
- `alt` (text, **requerido** para a11y)
- `caption` (text, opcional)
- `credit` (text, opcional)
- `uploadedBy` (relación a `Users`)
- `tags` (array de strings, internal — para búsqueda en media library)

```typescript
interface Media {
  id: string;
  filename: string;
  mimeType: string;
  cloudinaryPublicId: string;
  cloudinaryUrl: string;
  width: number;
  height: number;
  alt: string;
  caption?: string;
  credit?: string;
  uploadedBy: User;
  tags?: string[];
  createdAt: Date;
}
```

**Hook `beforeChange`:** sube a Cloudinary, persiste public_id + URL + dimensiones.

**Helper de URL:** `getCloudinaryUrl(media, { width, format: 'auto', quality: 'auto' })` construye URLs con transformaciones.

#### 4.2.7 Posts

**Purpose:** El corazón editorial. Posts con ciclo de estados, SEO, taxonomía, premium-ready (Story 2.4 + FR22/24).

**Atributos clave:**
- `title` (text, requerido)
- `slug` (text, único, requerido, evergreen, auto-generado)
- `excerpt` (textarea, ≤200 chars)
- `body` (richText Lexical, configurado en Story 2.5)
- `featuredImage` (relación a `Media`, requerida para `published`)
- `author` (relación a `Authors`, requerida)
- `category` (relación a `Categories`, requerida)
- `contentType` (relación a `ContentTypes`, requerida)
- `tags` (relación many a `Tags`)
- `status` (select: `draft`/`scheduled`/`published`/`archived`, default `draft`)
- `publishedAt` (date, requerida para `scheduled`/`published`)
- `metaTitle` (text, ≤60 chars)
- `metaDescription` (text, ≤160 chars)
- `aiAssistedFlag` (checkbox, default false, **interno** — FR24)
- `hasAffiliateLinks` (checkbox, auto-detectado, **interno**) — para auto-disclaimer (Story 5.5)
- `isFeatured` (checkbox) — hero de home
- `disableAds` (checkbox) — sobreescribe slots para este post (FR20)
- `isPremium` (checkbox, default false) — Story 5.7 (no-op si `PREMIUM_ENABLED=false`)
- `readingTimeMinutes` (number, auto-calculado)
- `wordCount` (number, auto-calculado)
- `previewToken` (text, generado on-demand)
- `previewTokenExpiresAt` (date)
- `createdAt`, `updatedAt`, `publishedAt` (auto-managed)

```typescript
interface Post {
  id: string;
  title: string;
  slug: string;
  excerpt?: string;
  body: RichTextLexical;
  featuredImage?: Media;
  author: Author;
  category: Category;
  contentType: ContentType;
  tags?: Tag[];
  status: 'draft' | 'scheduled' | 'published' | 'archived';
  publishedAt?: Date;
  metaTitle?: string;
  metaDescription?: string;
  aiAssistedFlag: boolean;
  hasAffiliateLinks: boolean;
  isFeatured: boolean;
  disableAds: boolean;
  isPremium: boolean;
  readingTimeMinutes?: number;
  wordCount?: number;
  previewToken?: string;
  previewTokenExpiresAt?: Date;
  createdAt: Date;
  updatedAt: Date;
}
```

**Versionado habilitado:** `versions: { drafts: true, maxPerDoc: 50 }`.

**Hooks:**
- `beforeValidate`: si `status` ∈ `[scheduled, published]` valida `featuredImage`, `author`, `category`, `contentType`, `publishedAt` (futuro para `scheduled`).
- `beforeChange`: auto-generate `slug` si falta; calcular `wordCount` y `readingTimeMinutes`; escanear body buscando links a `/go/[slug]` y setear `hasAffiliateLinks`.
- `afterChange`: si `status` cambió a `published`, disparar `revalidatePath` en `/`, `/articulo/[slug]`, `/categoria/[slug]`, `/tipo/[slug]`, `/autor/[slug]`, `/sitemap.xml`, `/rss.xml`.

#### 4.2.8 StaticPages

**Purpose:** Páginas estáticas editables desde admin (Story 3.9 + 6.1).

**Atributos clave:**
- `title`, `slug` (único, no editable post-creación para los reservados)
- `body` (richText Lexical, config simplificada)
- `metaTitle`, `metaDescription`
- `isReserved` (checkbox, true para los 6 slugs reservados)

```typescript
interface StaticPage {
  id: string;
  title: string;
  slug: string;
  body: RichTextLexical;
  metaTitle?: string;
  metaDescription?: string;
  isReserved: boolean;
  updatedAt: Date;
}
```

**Seed:** 6 páginas (`about`, `etica-editorial`, `politica-de-privacidad`, `aviso-legal`, `politica-de-afiliados`, `contacto`) con placeholders.

#### 4.2.9 Subscribers

**Purpose:** Lista de suscriptores newsletter (Story 4.1).

**Atributos clave:**
- `email` (text, único, requerido, validado RFC)
- `status` (select: `pending`/`confirmed`/`unsubscribed`/`bounced`)
- `confirmationToken` (text, único, generado al alta)
- `confirmationTokenExpiresAt` (date — +48h)
- `confirmedAt` (date)
- `unsubscribedAt` (date)
- `unsubscribeToken` (text, único, persistente — para link en cada email)
- `bouncedAt` (date)
- `bounceReason` (text)
- `source` (select: `footer`/`sidebar`/`article-end`/`manual`/`other`)
- `consentVersion` (text — versión de política aceptada)
- `metadata` (json — IP de signup, userAgent, opcional)

```typescript
interface Subscriber {
  id: string;
  email: string;
  status: 'pending' | 'confirmed' | 'unsubscribed' | 'bounced';
  confirmationToken?: string;
  confirmationTokenExpiresAt?: Date;
  confirmedAt?: Date;
  unsubscribedAt?: Date;
  unsubscribeToken: string;
  bouncedAt?: Date;
  bounceReason?: string;
  source: string;
  consentVersion: string;
  metadata?: Record<string, unknown>;
  createdAt: Date;
  updatedAt: Date;
}
```

#### 4.2.10 Newsletters

**Purpose:** Ediciones de newsletter (Story 4.4).

**Atributos clave:**
- `title`, `slug`
- `subject` (text, ≤120 chars) — asunto del email
- `previewText` (text, ≤150 chars) — preheader
- `body` (richText Lexical, config email-safe)
- `status` (select: `draft`/`scheduled`/`sent`)
- `scheduledAt`, `sentAt` (dates)
- `recipientCount` (number, populated post-send)
- `bounceCount` (number, updated via webhook)
- `openRate` (number, opcional — si Resend lo expone)
- `createdBy` (relación a `Users`)

```typescript
interface Newsletter {
  id: string;
  title: string;
  slug: string;
  subject: string;
  previewText: string;
  body: RichTextLexical;
  status: 'draft' | 'scheduled' | 'sent';
  scheduledAt?: Date;
  sentAt?: Date;
  recipientCount?: number;
  bounceCount?: number;
  openRate?: number;
  createdBy: User;
  createdAt: Date;
  updatedAt: Date;
}
```

#### 4.2.11 AdSlots

**Purpose:** Configuración de slots AdSense (Story 5.1).

**Atributos clave:**
- `key` (text, único — `header`, `in-feed-home`, `in-article-mid`, `in-article-end`, `sidebar`, `footer`)
- `enabled` (checkbox global)
- `adsenseSlotId` (text — ID del slot en AdSense, vacío hasta aprobación)
- `description` (text)
- `targetPositions` (json — config como "after paragraph N")
- `dimensions` (group — `widthMin`, `heightMin` para skeleton placeholder)

```typescript
interface AdSlot {
  id: string;
  key: string;
  enabled: boolean;
  adsenseSlotId?: string;
  description?: string;
  targetPositions?: Record<string, unknown>;
  dimensions: { widthMin: number; heightMin: number };
  createdAt: Date;
  updatedAt: Date;
}
```

**Seed:** 6 slots por default (todos `enabled=false` hasta que tengamos cuenta AdSense aprobada).

#### 4.2.12 AffiliateLinks

**Purpose:** Shortlinks de afiliados (Story 5.4).

**Atributos clave:**
- `slug` (text, único — usado en `/go/[slug]`)
- `targetUrlTemplate` (text — Amazon URL completa, sin tag)
- `label` (text — descripción interna)
- `product` (text — nombre del producto para reporte)
- `region` (select: `es`/`mx`/`ar`/`us`/`co`/`cl`/`other`)
- `clickCount` (number, auto-increment)
- `lastClickAt` (date)
- `disabled` (checkbox)
- `createdBy` (relación a Users)

```typescript
interface AffiliateLink {
  id: string;
  slug: string;
  targetUrlTemplate: string;
  label: string;
  product?: string;
  region: 'es' | 'mx' | 'ar' | 'us' | 'co' | 'cl' | 'other';
  clickCount: number;
  lastClickAt?: Date;
  disabled: boolean;
  createdBy: User;
  createdAt: Date;
  updatedAt: Date;
}
```

**Helper:** `buildAffiliateUrl(link)` inserta el tag regional desde env vars (`AMAZON_TAG_ES`, etc.) al template.

#### 4.2.13 Plans (premium — Fase 2 activación)

**Purpose:** Planes de membresía (Story 5.6).

**Atributos clave:**
- `key` (text, único — `monthly`, `annual`)
- `name` (text)
- `priceCents` (number)
- `currency` (text, ISO 4217 — `USD`/`EUR`/`MXN`/`ARS`)
- `interval` (select: `month`/`year`)
- `stripePriceId` (text — vacío hasta Stripe live)
- `benefits` (richText)
- `active` (checkbox, default false)
- `order` (number)

```typescript
interface Plan {
  id: string;
  key: string;
  name: string;
  priceCents: number;
  currency: string;
  interval: 'month' | 'year';
  stripePriceId?: string;
  benefits: RichTextLexical;
  active: boolean;
  order: number;
}
```

**Seed:** 2 plans placeholder (`monthly` USD 5 / `annual` USD 50), `active=false`.

#### 4.2.14 Members (premium — Fase 2)

**Purpose:** Usuario premium con vínculo a Stripe.

**Atributos clave:**
- `user` (relación a `Users`, requerida)
- `status` (select: `pending`/`active`/`paused`/`canceled`/`expired`)
- `stripeCustomerId` (text)
- `currentSubscription` (relación a `Subscriptions`)
- `joinedAt` (date)
- `metadata` (json)

#### 4.2.15 Subscriptions (premium — Fase 2)

**Purpose:** Mirror de Stripe subscription (Story 5.6).

**Atributos clave:**
- `member` (relación a `Members`)
- `plan` (relación a `Plans`)
- `stripeSubscriptionId` (text)
- `status` (select — mirror de Stripe: `active`/`past_due`/`canceled`/`incomplete`/etc.)
- `startedAt`, `currentPeriodEnd`, `canceledAt` (dates)

#### 4.2.16 AuditLogs

**Purpose:** Audit trail para acciones críticas (Story 6.2 + FR27).

**Atributos clave:**
- `actor` (relación a `Users`)
- `action` (text — `login.success`/`login.failed`/`post.publish`/`subscriber.delete`/`adslot.toggle`/etc.)
- `entityType` (text — `Post`/`Subscriber`/`AdSlot`/etc.)
- `entityId` (text)
- `payload` (json — diff resumido o snapshot mínimo)
- `ipAddress` (text — **solo para acciones de seguridad**, p.ej. login)
- `timestamp` (date)

```typescript
interface AuditLog {
  id: string;
  actor: User;
  action: string;
  entityType?: string;
  entityId?: string;
  payload?: Record<string, unknown>;
  ipAddress?: string;
  timestamp: Date;
}
```

**Retención:** 90 días (cron job mensual purga `timestamp < now() - 90d`).

---

## 5. Database Schema (PostgreSQL DDL + Indexes)

> _Payload + Drizzle generan migraciones automáticamente. Esta sección documenta el schema esperado y los **indexes explícitos críticos** que el dev debe verificar (Drizzle no los genera automáticamente para FTS o queries específicas)._

### 5.1 Indexes críticos a crear vía migration manual

```sql
-- Posts: Full-Text Search en español (Story 3.5)
ALTER TABLE posts ADD COLUMN search_tsv tsvector
  GENERATED ALWAYS AS (
    setweight(to_tsvector('spanish', coalesce(title, '')), 'A') ||
    setweight(to_tsvector('spanish', coalesce(excerpt, '')), 'B') ||
    setweight(to_tsvector('spanish', coalesce(body_plain_text, '')), 'C')
  ) STORED;

CREATE INDEX idx_posts_search_tsv ON posts USING GIN (search_tsv);

-- Posts: queries frecuentes
CREATE INDEX idx_posts_status_published_at ON posts (status, published_at DESC) WHERE status = 'published';
CREATE INDEX idx_posts_category_published ON posts (category_id, published_at DESC) WHERE status = 'published';
CREATE INDEX idx_posts_content_type_published ON posts (content_type_id, published_at DESC) WHERE status = 'published';
CREATE INDEX idx_posts_author_published ON posts (author_id, published_at DESC) WHERE status = 'published';
CREATE UNIQUE INDEX idx_posts_slug ON posts (slug);
CREATE INDEX idx_posts_scheduled ON posts (status, published_at) WHERE status = 'scheduled'; -- para el cron

-- Subscribers: email único + búsqueda por token
CREATE UNIQUE INDEX idx_subscribers_email ON subscribers (lower(email));
CREATE INDEX idx_subscribers_confirmation_token ON subscribers (confirmation_token) WHERE confirmation_token IS NOT NULL;
CREATE INDEX idx_subscribers_unsubscribe_token ON subscribers (unsubscribe_token);
CREATE INDEX idx_subscribers_status ON subscribers (status);

-- AffiliateLinks: slug es la clave de redirect
CREATE UNIQUE INDEX idx_affiliate_links_slug ON affiliate_links (slug);

-- Tags: many-to-many con posts
CREATE INDEX idx_posts_tags_post ON posts_tags (post_id);
CREATE INDEX idx_posts_tags_tag ON posts_tags (tag_id);

-- AuditLogs: queries por timestamp + actor (limpieza + reporte)
CREATE INDEX idx_audit_logs_timestamp ON audit_logs (timestamp DESC);
CREATE INDEX idx_audit_logs_actor_timestamp ON audit_logs (actor_id, timestamp DESC);

-- Newsletters: scheduled lookup
CREATE INDEX idx_newsletters_status_scheduled ON newsletters (status, scheduled_at) WHERE status = 'scheduled';
```

### 5.2 Notas de schema

- **`body_plain_text`** es una columna auxiliar populated por hook `beforeChange` de Posts: stripped del Lexical JSON → texto plano para FTS.
- **Soft-deletes:** ninguna collection los necesita en MVP; eliminación es permanente y disparada solo desde admin.
- **Timestamps:** Payload los crea automáticamente.
- **JSON columns:** Payload usa `jsonb` para campos `richText`, `array`, `group`, `metadata`.

---

## 6. API Specification (Sitemap de Rutas)

### 6.1 Rutas públicas (Next.js App Router → server components)

| Path | Method | Cache | Descripción |
|---|---|---|---|
| `/` | GET | ISR 60s + on-demand | Home con hero + últimos posts |
| `/articulo/[slug]` | GET | ISR 300s + on-demand | Página de artículo |
| `/categoria/[slug]` | GET | ISR 120s + on-demand | Listado por categoría |
| `/tipo/[slug]` | GET | ISR 120s + on-demand | Listado por tipo |
| `/tag/[slug]` | GET | ISR 120s + on-demand | Listado por tag |
| `/autor/[slug]` | GET | ISR 300s + on-demand | Página de autor |
| `/buscar` | GET | dynamic (no cache) | Resultados de búsqueda con `?q=` |
| `/newsletter` | GET | ISR 300s | Archivo de newsletters + signup |
| `/newsletter/[slug]` | GET | ISR 600s | Edición de newsletter |
| `/newsletter/baja-confirmada` | GET | static | Confirmación de unsubscribe |
| `/preview/[postId]` | GET | dynamic | Preview de draft con `?token=` |
| `/about` | GET | ISR 3600s | Página estática |
| `/etica-editorial` | GET | ISR 3600s | Política editorial |
| `/politica-de-privacidad` | GET | ISR 3600s | Privacidad |
| `/aviso-legal` | GET | ISR 3600s | Aviso legal |
| `/politica-de-afiliados` | GET | ISR 3600s | Política afiliados |
| `/contacto` | GET | ISR 3600s | Contacto |
| `/preferencias-de-cookies` | GET | static | Gestión CMP |
| `/404` | GET | static | Error custom |
| `/sitemap.xml` | GET | ISR 3600s + on-demand | Sitemap |
| `/robots.txt` | GET | static | Robots |
| `/rss.xml` | GET | ISR 600s | RSS global |
| `/categoria/[slug]/rss.xml` | GET | ISR 600s | RSS por categoría |

### 6.2 Rutas API (Next.js Route Handlers)

| Path | Method | Auth | Rate Limit | Descripción |
|---|---|---|---|---|
| `/api/newsletter/subscribe` | POST | none | 10/min/IP | Alta suscriptor (FR18, Story 4.1) |
| `/api/newsletter/confirm` | GET | token | 60/min/IP | Confirmar opt-in (Story 4.2) |
| `/api/newsletter/resend-confirmation` | POST | rate-limit | 3/h/email | Reenviar email confirm |
| `/api/newsletter/unsubscribe` | GET/POST | token | none | Baja (Story 4.3) |
| `/api/newsletter/feedback` | POST | none | 5/min/IP | Feedback opcional de baja |
| `/api/search` | GET | none | 60/min/IP | FTS (Story 3.5) |
| `/api/preview/sign` | POST | admin | 30/h/user | Generar preview token (Story 2.7) |
| `/api/cron/publish-scheduled` | GET | vercel-cron | bypass | Publicar drafts (Story 2.6) |
| `/api/cron/send-scheduled-newsletters` | GET | vercel-cron | bypass | Envío newsletter scheduled (Story 4.5) |
| `/api/cron/cleanup-audit-logs` | GET | vercel-cron | bypass | Purga audit logs >90d |
| `/api/cron/cleanup-pending-subscribers` | GET | vercel-cron | bypass | Purga subscribers `pending` >48h |
| `/api/webhooks/resend` | POST | webhook-secret | bypass | Bounces/complaints |
| `/api/webhooks/stripe` | POST | webhook-secret | bypass | **503 si PREMIUM_ENABLED=false** (Story 5.6) |
| `/go/[slug]` | GET | none | 1000/min global | Redirect afiliado (Story 5.4) |
| `/api/admin/revalidate` | POST | admin | 60/min/user | On-demand revalidate manual |
| `/admin/*` | varios | session | Payload-managed | Panel admin |

### 6.3 Webhook spec: Stripe (preparado para Fase 2)

**Endpoint:** `POST /api/webhooks/stripe`

**MVP behavior (PREMIUM_ENABLED=false):**
- Verifica firma con `STRIPE_WEBHOOK_SECRET` (no falla seguridad si está vacío).
- Logs el evento en `AuditLogs` con `action=webhook.stripe.received_disabled`.
- Devuelve `503 { reason: "premium_disabled" }`.

**Fase 2 behavior (PREMIUM_ENABLED=true):**

Eventos a procesar:

| Evento | Acción |
|---|---|
| `customer.subscription.created` | Crear/update `Subscriptions`; setear `Member.status='active'` |
| `customer.subscription.updated` | Mirror status, periodos |
| `customer.subscription.deleted` | `status='canceled'`, `Member.status='canceled'` |
| `invoice.payment_failed` | `status='past_due'`, alertar |
| `invoice.payment_succeeded` | Confirm period extension |
| `customer.created` | Set `stripeCustomerId` en Member |
| `checkout.session.completed` | Reconciliar con Member pre-existente |

**Idempotencia:** check `stripeEventId` en tabla `StripeEventsProcessed` (creada en Fase 2) antes de actuar.

### 6.4 Webhook spec: Resend bounces

**Endpoint:** `POST /api/webhooks/resend`

Eventos: `email.bounced`, `email.complained`, `email.delivered`.

Acción: actualiza `Subscriber.status='bounced'` + `bouncedAt` + `bounceReason`.

---

## 7. Core Workflows (Sequence Diagrams)

### 7.1 Publicación de artículo programado

```mermaid
sequenceDiagram
    actor E as Editor (Admin)
    participant A as Payload Admin
    participant DB as Postgres
    participant C as Vercel Cron (/5 min)
    participant API as /api/cron/publish-scheduled
    participant CDN as Vercel CDN

    E->>A: Crea Post, status=draft
    E->>A: Setea status=scheduled, publishedAt=2026-06-01 10:00
    A->>DB: INSERT/UPDATE posts (status=scheduled)
    A-->>E: Confirmación

    Note over C: Cada 5 min
    C->>API: GET /api/cron/publish-scheduled
    API->>DB: SELECT posts WHERE status='scheduled' AND published_at <= now()
    DB-->>API: posts[]
    loop por cada post
        API->>DB: UPDATE posts SET status='published'
        API->>CDN: revalidatePath('/', '/articulo/[slug]', '/categoria/[cat]', '/sitemap.xml')
    end
    API-->>C: 200 OK { published: N }
```

### 7.2 Suscripción a newsletter con doble opt-in

```mermaid
sequenceDiagram
    actor L as Lector
    participant FE as Frontend (RSC + Form)
    participant API as /api/newsletter/subscribe
    participant DB as Postgres
    participant R as Resend
    participant API2 as /api/newsletter/confirm

    L->>FE: Submit email
    FE->>API: POST { email, source }
    API->>API: Validate email + rate-limit
    API->>DB: INSERT subscriber (status=pending, token=xxx)
    API->>R: Send email confirmación (template + link con token)
    R-->>L: Email entregado
    API-->>FE: 200 { message: 'check_email' }

    L->>API2: GET /api/newsletter/confirm?token=xxx
    API2->>DB: SELECT subscriber WHERE confirmation_token=xxx
    DB-->>API2: subscriber
    API2->>API2: Validate token expiry
    API2->>DB: UPDATE subscriber (status=confirmed, confirmed_at=now())
    API2-->>L: 302 redirect /newsletter?confirmed=1
```

### 7.3 Click en enlace afiliado

```mermaid
sequenceDiagram
    actor L as Lector
    participant FE as Frontend (artículo)
    participant GO as /go/[slug]
    participant DB as Postgres
    participant AZ as Amazon

    L->>FE: Click en link "Comprar iPhone 15 Pro"
    FE->>GO: GET /go/iphone-15-pro
    GO->>DB: SELECT affiliate_link WHERE slug='iphone-15-pro' AND disabled=false
    DB-->>GO: link
    GO->>GO: build URL with regional tag (AMAZON_TAG_ES/MX/...)
    GO->>DB: UPDATE affiliate_link SET click_count=click_count+1, last_click_at=now()
    GO-->>L: 302 redirect a Amazon con tag
    L->>AZ: GET amazon.es/.../tag=elmate-21
```

### 7.4 Render del home con gating premium (Fase 2 mode)

```mermaid
sequenceDiagram
    actor L as Lector
    participant CDN as Vercel CDN
    participant RSC as Home (Server Component)
    participant DB as Postgres
    participant H as requiresMembership()

    L->>CDN: GET /
    alt Cache hit
        CDN-->>L: HTML (ISR cached)
    else Cache miss / stale
        CDN->>RSC: Render
        RSC->>DB: SELECT posts WHERE status='published' ORDER BY published_at DESC LIMIT 12
        DB-->>RSC: posts[]
        loop por cada post
            RSC->>H: requiresMembership(post)
            alt PREMIUM_ENABLED=false
                H-->>RSC: false (sin gating)
            else PREMIUM_ENABLED=true && post.isPremium=true
                H-->>RSC: true → renderizar card con badge "Premium"
            end
        end
        RSC-->>CDN: HTML
        CDN-->>L: HTML
    end
```

### 7.5 Login admin con 2FA TOTP

```mermaid
sequenceDiagram
    actor U as Usuario admin
    participant A as Payload Admin
    participant DB as Postgres
    participant U2 as otplib

    U->>A: POST /admin/login { email, password }
    A->>DB: Verify credentials
    DB-->>A: user (totpEnabled=true)
    A-->>U: 200 { requireTotp: true, ticket: xxx }

    U->>A: POST /admin/login/totp { ticket, code }
    A->>DB: SELECT user WHERE ticket
    A->>U2: authenticator.verify({ token: code, secret: user.totpSecret })
    alt Valid
        U2-->>A: true
        A->>DB: Create session
        A-->>U: 302 → /admin (set cookie)
    else Invalid
        U2-->>A: false
        A->>DB: Increment failedLoginAttempts; lock if N>=5
        A-->>U: 401 { error: 'invalid_totp' }
    end
```

---

## 8. Frontend Architecture

### 8.1 Component Organization

```text
src/
├── app/                            # Next.js App Router
│   ├── (public)/                   # Layout público
│   │   ├── layout.tsx              # Header + Footer + Providers
│   │   ├── page.tsx                # Home
│   │   ├── articulo/[slug]/
│   │   │   └── page.tsx
│   │   ├── categoria/[slug]/
│   │   │   ├── page.tsx
│   │   │   └── rss.xml/route.ts
│   │   ├── tipo/[slug]/page.tsx
│   │   ├── tag/[slug]/page.tsx
│   │   ├── autor/[slug]/page.tsx
│   │   ├── buscar/page.tsx
│   │   ├── newsletter/
│   │   │   ├── page.tsx
│   │   │   └── [slug]/page.tsx
│   │   ├── about/page.tsx
│   │   ├── etica-editorial/page.tsx
│   │   ├── (static)/[slug]/page.tsx  # catch-all para static pages restantes
│   │   ├── sitemap.xml/route.ts
│   │   ├── robots.txt/route.ts
│   │   ├── rss.xml/route.ts
│   │   ├── go/[slug]/route.ts      # afiliados redirect
│   │   └── not-found.tsx           # 404 custom
│   ├── preview/[postId]/page.tsx   # preview de drafts
│   ├── (payload)/                  # admin de Payload
│   │   └── admin/[[...segments]]/page.tsx
│   ├── api/
│   │   ├── newsletter/
│   │   │   ├── subscribe/route.ts
│   │   │   ├── confirm/route.ts
│   │   │   ├── resend-confirmation/route.ts
│   │   │   └── unsubscribe/route.ts
│   │   ├── search/route.ts
│   │   ├── preview/sign/route.ts
│   │   ├── webhooks/
│   │   │   ├── resend/route.ts
│   │   │   └── stripe/route.ts
│   │   ├── cron/
│   │   │   ├── publish-scheduled/route.ts
│   │   │   ├── send-scheduled-newsletters/route.ts
│   │   │   ├── cleanup-audit-logs/route.ts
│   │   │   └── cleanup-pending-subscribers/route.ts
│   │   └── admin/revalidate/route.ts
│   ├── layout.tsx                  # Root layout (html, body, fonts)
│   └── globals.css
├── components/
│   ├── ui/                         # shadcn/ui components
│   ├── public/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── ArticleCard.tsx
│   │   ├── ArticleHero.tsx
│   │   ├── TableOfContents.tsx     # client
│   │   ├── ShareButtons.tsx        # client
│   │   ├── NewsletterSignupForm.tsx # client
│   │   ├── ThemeToggle.tsx         # client
│   │   ├── SearchOverlay.tsx       # client
│   │   ├── AdSlot.tsx
│   │   ├── AffiliateDisclaimer.tsx
│   │   └── LexicalRenderer/
│   │       ├── index.tsx
│   │       ├── blocks/
│   │       │   ├── ImageBlock.tsx
│   │       │   ├── YouTubeBlock.tsx
│   │       │   ├── XEmbedBlock.tsx
│   │       │   ├── CodeBlock.tsx
│   │       │   └── CalloutBlock.tsx
│   └── admin/                      # extensiones custom de Payload admin
├── collections/                    # Payload collections
│   ├── Users.ts
│   ├── Posts.ts
│   ├── Categories.ts
│   ├── ContentTypes.ts
│   ├── Tags.ts
│   ├── Authors.ts
│   ├── Media.ts
│   ├── StaticPages.ts
│   ├── Subscribers.ts
│   ├── Newsletters.ts
│   ├── AdSlots.ts
│   ├── AffiliateLinks.ts
│   ├── Plans.ts
│   ├── Members.ts
│   ├── Subscriptions.ts
│   └── AuditLogs.ts
├── lib/
│   ├── payload.ts                  # client + getPayloadHMR helper
│   ├── premium.ts                  # requiresMembership() + flag helpers
│   ├── stripe.ts                   # Stripe SDK client (stub-aware)
│   ├── resend.ts                   # Resend SDK + templates
│   ├── cloudinary.ts               # adapter + URL builder
│   ├── seo.ts                      # generateMetadata helpers + JSON-LD builders
│   ├── slug.ts
│   ├── rate-limit.ts               # Upstash Redis ratelimiters
│   ├── logger.ts                   # Pino instance
│   ├── audit-log.ts                # writeAuditLog() helper
│   ├── affiliate.ts                # buildAffiliateUrl()
│   ├── lexical/
│   │   ├── config.ts               # editorConfig
│   │   ├── extractPlainText.ts
│   │   ├── detectAffiliateLinks.ts
│   │   └── sanitize.ts
│   └── revalidate.ts               # wrappers de revalidatePath/Tag
├── hooks/                          # Payload hooks compartidos
│   ├── posts/
│   │   ├── beforeChange.ts
│   │   └── afterChange.ts
│   └── ...
├── middleware.ts                   # CSP nonce + rate-limit + locale + theme cookie
├── payload.config.ts               # Payload config raíz
└── styles/
    └── tokens.css                  # Design tokens (CSS vars)
```

### 8.2 State Management

**Filosofía:** server-state-first. Mínimo client state.

- **Server state:** React Server Components + Next.js cache (`fetch`, `cache`, `unstable_cache`, ISR). Nunca hay store global de "datos del servidor" en cliente.
- **Client state local:** `useState` para overlays, modals, formularios. Sin Zustand/Jotai/Redux.
- **Client state global mínimo:** `next-themes` para dark/light (cookies + classList).
- **URL state:** filtros, paginación, búsqueda usando `searchParams` (server-side legible).
- **localStorage (cliente):** últimas búsquedas, prefs de UI menores.

### 8.3 Routing

App Router file-system based — la estructura en 8.1 ya define las rutas. Cosas clave:

- **`(public)` layout group:** mantiene URLs limpias (sin `/public/` prefix).
- **`(payload)` layout group:** aísla layout de admin (no inherit del Header/Footer público).
- **Conflict resolution:** rutas dinámicas tienen prioridad jerárquica. `/[slug]` en `(static)/` resuelve si ningún match anterior aplica.
- **Middleware protege `/preview/*` y `/admin/*`:** middleware verifica token (preview) o session (admin).

### 8.4 Frontend Services Layer

**No hay HTTP client traditional** — RSC accede a Payload directamente:

```typescript
// lib/payload.ts
import { getPayloadHMR } from '@payloadcms/next/utilities';
import config from '@/payload.config';

export const getPayload = () => getPayloadHMR({ config });

// app/(public)/articulo/[slug]/page.tsx
import { getPayload } from '@/lib/payload';

export default async function ArticlePage({ params }: { params: { slug: string } }) {
  const payload = await getPayload();
  const { docs } = await payload.find({
    collection: 'posts',
    where: { and: [{ slug: { equals: params.slug } }, { status: { equals: 'published' } }] },
    limit: 1,
    depth: 2,
  });

  if (!docs[0]) notFound();

  return <ArticleView post={docs[0]} />;
}
```

**Para client components** que necesitan hacer fetch (ej: signup form): usar `fetch()` directo a los `/api/` route handlers.

---

## 9. Backend Architecture (Payload + Route Handlers)

### 9.1 Payload Collections Pattern

Cada collection en `src/collections/` exporta una `CollectionConfig`:

```typescript
// src/collections/Posts.ts
import { CollectionConfig } from 'payload';
import { beforeChangePost } from '@/hooks/posts/beforeChange';
import { afterChangePost } from '@/hooks/posts/afterChange';

export const Posts: CollectionConfig = {
  slug: 'posts',
  admin: {
    useAsTitle: 'title',
    defaultColumns: ['title', 'status', 'category', 'publishedAt', 'updatedAt'],
    group: 'Editorial',
  },
  access: {
    read: ({ req }) => {
      // Public reads: solo published
      if (!req.user) return { status: { equals: 'published' } };
      // Authenticated admins: full
      return true;
    },
    create: ({ req }) => Boolean(req.user),
    update: ({ req }) => Boolean(req.user),
    delete: ({ req }) => req.user?.role === 'super-admin',
  },
  versions: { drafts: true, maxPerDoc: 50 },
  hooks: {
    beforeChange: [beforeChangePost],
    afterChange: [afterChangePost],
  },
  fields: [
    /* ... ver Data Models ... */
  ],
};
```

### 9.2 Cron Functions

Cada cron es un Next.js Route Handler protegido por `vercel-cron` auth:

```typescript
// app/api/cron/publish-scheduled/route.ts
import { NextRequest } from 'next/server';
import { getPayload } from '@/lib/payload';
import { revalidatePath } from 'next/cache';
import { logger } from '@/lib/logger';

export async function GET(req: NextRequest) {
  if (req.headers.get('authorization') !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response('Unauthorized', { status: 401 });
  }

  const payload = await getPayload();
  const now = new Date();

  const { docs: scheduled } = await payload.find({
    collection: 'posts',
    where: { and: [{ status: { equals: 'scheduled' } }, { publishedAt: { less_than_equal: now.toISOString() } }] },
    limit: 50,
  });

  let publishedCount = 0;
  for (const post of scheduled) {
    try {
      await payload.update({
        collection: 'posts',
        id: post.id,
        data: { status: 'published' },
      });
      publishedCount++;
    } catch (err) {
      logger.error({ err, postId: post.id }, 'publish-scheduled failed');
    }
  }

  return Response.json({ publishedCount });
}
```

**Configuración en `vercel.json`:**

```json
{
  "crons": [
    { "path": "/api/cron/publish-scheduled", "schedule": "*/5 * * * *" },
    { "path": "/api/cron/send-scheduled-newsletters", "schedule": "*/5 * * * *" },
    { "path": "/api/cron/cleanup-pending-subscribers", "schedule": "0 3 * * *" },
    { "path": "/api/cron/cleanup-audit-logs", "schedule": "0 4 1 * *" }
  ]
}
```

### 9.3 Authentication and Authorization

- **Admin:** Payload native auth (email+password) + TOTP via `otplib` custom field + endpoint.
- **Public users:** ninguna sesión en MVP (los suscriptores newsletter NO loguean).
- **Premium users (Fase 2):** Payload `Users` con role `member`; session via Payload + custom routes para `/cuenta`.
- **Webhook auth:** verificación HMAC con secret en env (Stripe + Resend).
- **Cron auth:** `Authorization: Bearer ${CRON_SECRET}` — Vercel Cron lo inyecta automáticamente.

---

## 10. Unified Project Structure

Ver Sección 8.1 (Component Organization) — esa es la estructura completa.

Archivos adicionales en raíz:

```
el-mate-digital/
├── src/                            # (ya documentado en 8.1)
├── public/                         # static assets (logo, OG default image, favicons)
├── tests/
│   ├── unit/                       # Vitest unit
│   ├── integration/                # Vitest integration (Payload collections)
│   └── e2e/                        # Playwright
├── scripts/
│   ├── seed.ts                     # seed inicial de DB
│   └── migrate-static-pages.ts     # crear las 6 reserved static pages
├── .github/
│   └── workflows/
│       ├── ci.yml                  # lint + typecheck + unit + integration
│       └── e2e.yml                 # Playwright (solo en main)
├── .env.example
├── next.config.mjs                 # Next + Payload + CSP base
├── payload.config.ts               # → src/payload.config.ts
├── tailwind.config.ts
├── tsconfig.json                   # strict: true
├── package.json
├── pnpm-lock.yaml
├── vercel.json                     # cron schedules + headers
└── README.md
```

---

## 11. Development Workflow

### 11.1 Prerequisites

```bash
# Versiones requeridas
node --version    # >= 20.x LTS
pnpm --version    # >= 9.x
git --version     # >= 2.40

# Cuentas necesarias
# - Vercel (con GitHub conectado)
# - Neon (proyecto creado)
# - Cloudinary (cuenta free)
# - Resend (cuenta + API key)
# - Sentry (proyecto creado)
# - Upstash Redis (DB creada)
# Stripe (cuenta para Fase 2, no obligatoria en MVP)
```

### 11.2 Initial Setup

```bash
git clone git@github.com:owner/el-mate-digital.git
cd el-mate-digital

pnpm install
cp .env.example .env.local
# Editar .env.local con credenciales

pnpm payload migrate            # crea schema en Neon
pnpm tsx scripts/seed.ts        # categorías, tipos, autor inicial, slots, plans, static pages
```

### 11.3 Development Commands

```bash
# Dev (Turbopack)
pnpm dev                        # localhost:3000 (frontend + /admin)

# Type checking
pnpm typecheck

# Lint
pnpm lint
pnpm lint:fix
pnpm format

# Tests
pnpm test                       # Vitest unit + integration
pnpm test:e2e                   # Playwright
pnpm test:e2e:ui                # Playwright con UI

# Build
pnpm build

# Payload migrations
pnpm payload migrate
pnpm payload migrate:create     # generar nueva migration

# Seed
pnpm tsx scripts/seed.ts
```

### 11.4 Environment Variables

```bash
# .env.local — REQUERIDAS

# Next.js / Payload
NEXT_PUBLIC_SITE_URL=http://localhost:3000   # production: https://el-mate-digital.vercel.app
PAYLOAD_SECRET=<random 32-byte hex>
PAYLOAD_PUBLIC_SERVER_URL=http://localhost:3000

# DB
DATABASE_URL=postgresql://user:pass@host/dbname

# Cloudinary
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME=...        # client builds URLs

# Resend
RESEND_API_KEY=re_...
RESEND_FROM_EMAIL=hola@elmatedigital.com     # placeholder hasta tener dominio
RESEND_WEBHOOK_SECRET=...                    # generado en Resend dashboard

# Upstash Redis (rate limit)
UPSTASH_REDIS_REST_URL=...
UPSTASH_REDIS_REST_TOKEN=...

# Sentry
SENTRY_DSN=...
NEXT_PUBLIC_SENTRY_DSN=...

# Stripe (Fase 2)
STRIPE_SECRET_KEY=sk_test_...                # placeholder ok en MVP
STRIPE_WEBHOOK_SECRET=whsec_...              # placeholder ok en MVP
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...

# AdSense (cuando aprueben)
NEXT_PUBLIC_ADSENSE_PUBLISHER_ID=ca-pub-...  # vacío en dev

# Amazon Affiliate tags
AMAZON_TAG_ES=elmate-21
AMAZON_TAG_MX=elmate-20
AMAZON_TAG_AR=elmate-20
AMAZON_TAG_US=elmate-20

# Feature flags
PREMIUM_ENABLED=false

# Cron auth
CRON_SECRET=<random 32-byte hex>

# Logger
LOG_LEVEL=info                               # dev: debug
```

---

## 12. Deployment Architecture

### 12.1 Deployment Strategy

- **Frontend + Backend (mismo deployable):**
  - **Plataforma:** Vercel
  - **Build:** `pnpm build` (Next.js production build)
  - **Output:** Next.js standalone + edge functions
  - **Trigger:** push a `main` (production) o cualquier branch (preview)

- **CDN:**
  - Vercel Edge Network (automático).
  - Cloudinary CDN para media.

- **Database:**
  - Neon production branch para `main`.
  - Neon dev branch por cada feature branch (configurar Neon-Vercel integration).

### 12.2 CI/CD Pipeline

**`.github/workflows/ci.yml` (corre en PR + push a main):**

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
        with: { version: 9 }
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: 'pnpm' }
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck
      - run: pnpm test
```

**`.github/workflows/e2e.yml` (solo en main):**

```yaml
name: E2E
on:
  push:
    branches: [main]

jobs:
  playwright:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: 'pnpm' }
      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps
      - run: pnpm test:e2e
        env:
          E2E_BASE_URL: ${{ secrets.VERCEL_PRODUCTION_URL }}
```

### 12.3 Environments

| Environment | URL | Branch | DB | Purpose |
|---|---|---|---|---|
| Development | `localhost:3000` | local | Neon dev branch (o local Postgres) | Dev local |
| Preview | `el-mate-digital-<pr>.vercel.app` | feature branch | Neon preview branch | Review PR |
| Production | `el-mate-digital.vercel.app` (luego dominio) | `main` | Neon production | Live |

---

## 13. Security and Performance

### 13.1 Content Security Policy (CSP) Detallada

Política estricta con nonces dinámicos, AdSense, CMP y embeds permitidos. Implementada en `middleware.ts`:

```typescript
// middleware.ts (extracto)
import { NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const nonce = Buffer.from(crypto.randomUUID()).toString('base64');
  const cspHeader = `
    default-src 'self';
    script-src 'self' 'nonce-${nonce}' 'strict-dynamic'
      https://pagead2.googlesyndication.com
      https://*.googletagservices.com
      https://*.googlesyndication.com
      https://fundingchoicesmessages.google.com
      https://plausible.io
      https://*.sentry.io;
    style-src 'self' 'unsafe-inline';
    img-src 'self' data: blob:
      https://res.cloudinary.com
      https://*.gstatic.com
      https://*.googleusercontent.com
      https://pbs.twimg.com;
    font-src 'self' data:;
    connect-src 'self'
      https://*.sentry.io
      https://plausible.io
      https://api.resend.com
      https://*.upstash.io
      https://api.stripe.com;
    frame-src
      https://www.youtube.com
      https://www.youtube-nocookie.com
      https://platform.twitter.com
      https://*.x.com
      https://js.stripe.com;
    object-src 'none';
    base-uri 'self';
    form-action 'self';
    frame-ancestors 'none';
    upgrade-insecure-requests;
  `.replace(/\s+/g, ' ').trim();

  const requestHeaders = new Headers(request.headers);
  requestHeaders.set('x-nonce', nonce);
  requestHeaders.set('Content-Security-Policy', cspHeader);

  const response = NextResponse.next({ request: { headers: requestHeaders } });
  response.headers.set('Content-Security-Policy', cspHeader);
  return response;
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

**Notas críticas:**
- AdSense requiere `strict-dynamic` para sus scripts dinámicos. La combinación `nonce + strict-dynamic` es **soportada por AdSense** y considerada segura.
- `style-src 'unsafe-inline'` — Tailwind hot-reload genera inline styles; en producción podríamos eliminarlo, evaluar al hacer Story 6.3.
- Google Funding Choices CMP corre desde `fundingchoicesmessages.google.com`, allowlist obligatoria.

### 13.2 Headers de seguridad adicionales

```typescript
// next.config.mjs (extracto)
async headers() {
  return [{
    source: '/(.*)',
    headers: [
      { key: 'Strict-Transport-Security', value: 'max-age=31536000; includeSubDomains; preload' },
      { key: 'X-Frame-Options', value: 'DENY' },
      { key: 'X-Content-Type-Options', value: 'nosniff' },
      { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
      { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
    ],
  }];
}
```

### 13.3 Rate Limiting

Implementado vía `@upstash/ratelimit` + Redis. Helpers en `lib/rate-limit.ts`:

```typescript
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const redis = Redis.fromEnv();

export const limiters = {
  newsletterSubscribe: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(10, '1 m') }),
  newsletterResendConfirm: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(3, '1 h') }),
  search: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(60, '1 m') }),
  affiliateRedirect: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(1000, '1 m'), prefix: 'global' }),
  preview: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(60, '1 h') }),
  adminLogin: new Ratelimit({ redis, limiter: Ratelimit.slidingWindow(5, '15 m') }),
};
```

### 13.4 Performance Optimization

- **Bundle size:** target inicial home + artículo <120 KB de JS gzipped (RSC ayuda a mantenerlo bajo). Validación periódica vía Vercel Bundle Analyzer.
- **Loading:** RSC + ISR + on-demand revalidate; `next/image` con `priority` solo en hero LCP image.
- **Fonts:** `next/font` con `display: 'swap'` y subsets `latin` + `latin-ext` (sin chino/cirílico).
- **Cache strategy:**
  - **ISR** para todo el público (revalidate por defecto cada 60-3600s según ruta — ver 6.1).
  - **`revalidatePath` on-demand** disparado en `afterChange` de Posts/Newsletters/StaticPages.
  - **`fetch` cache** en server components vía `next: { revalidate: N, tags: [...] }`.
  - **DB query cache:** `unstable_cache` para queries pesadas (top categorías, featured posts) con `tags: ['posts']` invalidados al publicar.
  - **Browser cache:** `Cache-Control: public, max-age=60, stale-while-revalidate=300` en respuestas dinámicas.

---

## 14. Testing Strategy

### 14.1 Testing Pyramid

```text
              E2E (Playwright)
             / 5-7 tests críticos \
        Integration tests (Vitest)
       / 15-25 tests de hooks + APIs \
   Frontend Unit (Vitest + Testing Library)
  / 60-80% coverage en lib/ y components/ \
       Backend Unit (Vitest)
   / 70-80% coverage en collections/, hooks/ \
```

### 14.2 Test Organization

```
tests/
├── unit/
│   ├── lib/
│   │   ├── premium.test.ts             # requiresMembership con flag on/off
│   │   ├── affiliate.test.ts           # buildAffiliateUrl
│   │   ├── slug.test.ts
│   │   └── seo.test.ts
│   └── components/
│       ├── ArticleCard.test.tsx
│       ├── LexicalRenderer.test.tsx
│       └── NewsletterSignupForm.test.tsx
├── integration/
│   ├── collections/
│   │   ├── posts.test.ts               # hooks beforeChange/afterChange
│   │   └── subscribers.test.ts         # validation, status transitions
│   └── api/
│       ├── newsletter-subscribe.test.ts
│       ├── search.test.ts
│       └── webhook-stripe-disabled.test.ts
└── e2e/
    ├── publish-flow.spec.ts            # draft → schedule → published
    ├── newsletter-signup.spec.ts       # form → confirm → archive
    ├── affiliate-click.spec.ts         # /go/[slug] redirect + counter
    ├── article-render-with-embeds.spec.ts
    └── premium-gating-flag-off.spec.ts
```

### 14.3 Test Example: requiresMembership con flag

```typescript
// tests/unit/lib/premium.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { requiresMembership } from '@/lib/premium';

describe('requiresMembership', () => {
  beforeEach(() => vi.unstubAllEnvs());

  it('returns false when PREMIUM_ENABLED=false', () => {
    vi.stubEnv('PREMIUM_ENABLED', 'false');
    expect(requiresMembership({ isPremium: true } as any)).toBe(false);
  });

  it('returns false when PREMIUM_ENABLED=true but post.isPremium=false', () => {
    vi.stubEnv('PREMIUM_ENABLED', 'true');
    expect(requiresMembership({ isPremium: false } as any)).toBe(false);
  });

  it('returns true when PREMIUM_ENABLED=true and post.isPremium=true', () => {
    vi.stubEnv('PREMIUM_ENABLED', 'true');
    expect(requiresMembership({ isPremium: true } as any)).toBe(true);
  });
});
```

---

## 15. Coding Standards

### 15.1 Critical Rules (mandatorias para PRs)

- **TypeScript strict:** `any` prohibido; preferir `unknown` + type guards.
- **Sin `process.env` directo:** usar `lib/env.ts` con validación Zod al boot.
- **Acceso a datos vía Payload:** nunca SQL crudo en frontend o API routes (solo en migrations explícitas).
- **API routes responden con `Response.json()`** y siempre incluyen `{ error: { code, message } }` en errores.
- **Logs estructurados con `logger`:** sin `console.log/error` en código que llegue a `main`.
- **Audit log obligatorio:** acciones admin críticas (publish, unpublish, subscriber delete, adslot toggle, login success/failed) llaman `writeAuditLog()`.
- **CSP nonce:** scripts inline necesarios usan el nonce del middleware via `headers().get('x-nonce')`.
- **Tests requeridos por flujo crítico** (NFR20) — el PR no mergea si falta el test E2E correspondiente.
- **Sanitización Lexical:** todo HTML que se renderice del body de Posts/Newsletters/StaticPages pasa por `sanitize()`.

### 15.2 Naming Conventions

| Elemento | Convención | Ejemplo |
|---|---|---|
| Componentes React | PascalCase, archivo igual al nombre | `ArticleCard.tsx` |
| Hooks custom | camelCase, prefijo `use` | `useTheme.ts` |
| Server actions | camelCase | `subscribeToNewsletter.ts` |
| API routes | kebab-case | `/api/newsletter/resend-confirmation` |
| Collections Payload | PascalCase singular | `Posts`, `AdSlots` |
| Slugs (collection) | kebab-case plural | `posts`, `ad-slots` |
| DB tables | snake_case plural | `posts`, `ad_slots` |
| Env vars | UPPER_SNAKE_CASE | `PREMIUM_ENABLED` |
| Tests | `<file>.test.ts` o `<file>.spec.ts` (e2e) | `premium.test.ts` |

---

## 16. Error Handling Strategy

### 16.1 Error Response Format (estándar API)

```typescript
interface ApiError {
  error: {
    code: string;            // ej 'NEWSLETTER_INVALID_TOKEN'
    message: string;         // texto user-friendly en español
    details?: Record<string, unknown>;
    timestamp: string;       // ISO 8601
    requestId: string;       // UUID para tracing
  };
}
```

### 16.2 Frontend Error Handling

- **`error.tsx`** por segmento de App Router renderiza fallback amigable + opción de retry + reporte automático a Sentry.
- **`global-error.tsx`** captura errores en root layout.
- **`not-found.tsx`** custom (con voz de marca).
- **Formularios:** errores inline al campo, con `aria-invalid` + `aria-describedby`.
- **Toast notifications:** vía `sonner` (recomendado) para acciones admin.

### 16.3 Backend Error Handling

```typescript
// lib/api-error.ts
export class ApiError extends Error {
  constructor(
    public code: string,
    public message: string,
    public statusCode: number = 500,
    public details?: Record<string, unknown>,
  ) {
    super(message);
  }
}

// en route handler
export async function POST(req: Request) {
  try {
    const data = schema.parse(await req.json());
    // ...
    return Response.json({ ok: true });
  } catch (err) {
    if (err instanceof ApiError) {
      return Response.json({ error: { code: err.code, message: err.message, details: err.details, timestamp: new Date().toISOString(), requestId: crypto.randomUUID() } }, { status: err.statusCode });
    }
    logger.error({ err }, 'unhandled api error');
    Sentry.captureException(err);
    return Response.json({ error: { code: 'INTERNAL_ERROR', message: 'Algo salió mal', timestamp: new Date().toISOString(), requestId: crypto.randomUUID() } }, { status: 500 });
  }
}
```

---

## 17. Monitoring and Observability

### 17.1 Monitoring Stack

- **Errores client + server:** Sentry (`@sentry/nextjs`).
- **RUM + CWV:** Vercel Speed Insights.
- **Pageviews + referrers:** Plausible.
- **Logs:** Vercel Logs (estructurados Pino JSON).
- **Uptime externo (opcional Fase 2):** Better Uptime / UptimeRobot.

### 17.2 Key Metrics

| Categoría | Métrica | Fuente | Threshold/Objetivo |
|---|---|---|---|
| CWV | LCP p75 móvil | Vercel Speed Insights | <2.0s (NFR1) |
| CWV | INP p75 móvil | Vercel Speed Insights | <200ms (NFR1) |
| CWV | CLS p75 móvil | Vercel Speed Insights | <0.05 (NFR1) |
| Backend | Tiempo de respuesta /api/* | Vercel Logs | <500ms p95 |
| Errors | Error rate | Sentry | <0.5% requests |
| Editorial | Posts publicados/sem | Payload + custom dashboard | ≥4 (NFR24) |
| Newsletter | Bounce rate | Resend webhook + dashboard | <5% |
| Newsletter | Open rate | Resend | >30% (objetivo) |
| Afiliados | Click count / artículo | DB | tracked |

---

## 18. Seeds Plan

Script `scripts/seed.ts` corre tras `pnpm payload migrate` y crea:

```typescript
// scripts/seed.ts (estructura)
async function seed() {
  const payload = await getPayload();

  // 1. Usuario super-admin inicial (si no existe)
  await ensureUser({ email: process.env.SEED_ADMIN_EMAIL, role: 'super-admin' });

  // 2. Categories
  await seedMany('categories', [
    { name: 'iPhone', slug: 'iphone', order: 1 },
    { name: 'Mac', slug: 'mac', order: 2 },
    { name: 'iPad', slug: 'ipad', order: 3 },
    { name: 'Watch', slug: 'watch', order: 4 },
    { name: 'Vision', slug: 'vision', order: 5 },
    { name: 'Servicios', slug: 'servicios', order: 6 },
  ]);

  // 3. ContentTypes
  await seedMany('contentTypes', [
    { name: 'Noticia', slug: 'noticia', order: 1 },
    { name: 'Rumor', slug: 'rumor', order: 2 },
    { name: 'Análisis', slug: 'analisis', order: 3 },
    { name: 'Tutorial', slug: 'tutorial', order: 4 },
    { name: 'Reseña', slug: 'resena', order: 5 },
    { name: 'Guía de compra', slug: 'guia-de-compra', order: 6 },
  ]);

  // 4. Authors (dueño placeholder)
  await seedOne('authors', { name: 'Dueño', slug: 'dueño', bio: lexicalEmpty(), socialLinks: [] });

  // 5. AdSlots (default disabled hasta aprobación AdSense)
  await seedMany('adSlots', [
    { key: 'header', enabled: false, dimensions: { widthMin: 320, heightMin: 50 } },
    { key: 'in-feed-home', enabled: false, dimensions: { widthMin: 300, heightMin: 250 } },
    { key: 'in-article-mid', enabled: false, dimensions: { widthMin: 300, heightMin: 250 } },
    { key: 'in-article-end', enabled: false, dimensions: { widthMin: 300, heightMin: 250 } },
    { key: 'sidebar', enabled: false, dimensions: { widthMin: 300, heightMin: 250 } },
    { key: 'footer', enabled: false, dimensions: { widthMin: 320, heightMin: 50 } },
  ]);

  // 6. Plans (premium placeholders, active=false)
  await seedMany('plans', [
    { key: 'monthly', name: 'Plan mensual', priceCents: 500, currency: 'USD', interval: 'month', active: false, benefits: lexicalEmpty(), order: 1 },
    { key: 'annual', name: 'Plan anual', priceCents: 5000, currency: 'USD', interval: 'year', active: false, benefits: lexicalEmpty(), order: 2 },
  ]);

  // 7. StaticPages reservadas con placeholders
  await seedMany('staticPages', [
    { title: 'Sobre nosotros', slug: 'about', isReserved: true, body: lexicalPlaceholder('Próximamente') },
    { title: 'Ética editorial', slug: 'etica-editorial', isReserved: true, body: lexicalPlaceholder('Próximamente') },
    { title: 'Política de privacidad', slug: 'politica-de-privacidad', isReserved: true, body: lexicalPlaceholder('Próximamente') },
    { title: 'Aviso legal', slug: 'aviso-legal', isReserved: true, body: lexicalPlaceholder('Próximamente') },
    { title: 'Política de afiliados', slug: 'politica-de-afiliados', isReserved: true, body: lexicalPlaceholder('Próximamente') },
    { title: 'Contacto', slug: 'contacto', isReserved: true, body: lexicalPlaceholder('Próximamente') },
  ]);
}
```

---

## 19. Pre-Epic 1 Risks & Investigation Tasks

> _Riesgos técnicos que el dev debe validar antes/durante Epic 1 (Foundation) para evitar reescribir después._

| # | Riesgo | Validación temprana | Mitigación si falla |
|---|---|---|---|
| R1 | **Compatibilidad Cloudinary plugin con Payload 3.0** — la API de plugins cambió entre v2 y v3 | Spike de 2h en Story 1.2 antes de Story 2.3: crear test collection con upload y verificar que se sube a Cloudinary | Fallback **Vercel Blob** + integración manual con next/image |
| R2 | **CSP estricta + AdSense convivencia** | Story 6.3: configurar CSP final con AdSense activo en preview y validar que ads renderizan | Aflojar a `script-src` con `'unsafe-inline'` específico (peor pero funcional) o explorar AdSense CSP recommended config |
| R3 | **X/Twitter embeds fragilidad** — la widget oficial de X cambia periódicamente | Story 2.5: implementar el bloque XEmbed con fallback explícito a "Ver tweet en X" si falla | Servir blockquote estático con link, sin iframe |
| R4 | **Vercel Cron en Hobby tiene límite de 2 crons** | Si los 4 crons planificados no caben, upgrade a Pro o consolidar en un único cron con dispatcher | Consolidar en `/api/cron/run-all` con dispatch interno |
| R5 | **Postgres FTS en español con acentos/plurales** — comportamiento exacto puede sorprender | Story 3.5: tests con queries reales ("iPhone 15", "análisis", "Watch") | Combinar `to_tsvector('spanish', unaccent(...))` o caer a `pg_trgm` para fuzzy |
| R6 | **Resend deliverability sin dominio** — `onresend.com` puede llegar a spam | Validar con 5-10 inboxes reales (Gmail, Hotmail, Yahoo, iCloud) durante Epic 4 | Acelerar registro de dominio si se observa <80% inbox placement |
| R7 | **Payload 3.0 + Next 15 + RSC** — combinación reciente con bugs conocidos | Story 1.2: spike de instalación + arrancar admin + hacer 1 collection con CRUD básico | Pin de versiones a un build estable conocido (consultar Payload GitHub issues) |

---

## 20. Architectural Decision Records (ADRs) resumidos

| ADR | Decisión | Por qué |
|---|---|---|
| ADR-01 | Polyrepo (single Next.js app) | 1 deployable, sin packages a compartir |
| ADR-02 | Modular monolith vs microservicios | MVP <100k pv/mo, simplifica operación |
| ADR-03 | Next.js 15 App Router + RSC default | SEO + perf + bundle mínimo |
| ADR-04 | Payload CMS 3.0 embebido | 1 deploy, share runtime/DB, ecosistema TS |
| ADR-05 | PostgreSQL en Neon | Free tier + branching + portabilidad |
| ADR-06 | Cloudinary para media | On-the-fly transforms + AVIF/WebP auto |
| ADR-07 | Vercel Cron para jobs | Free, nativo, suficiente para 4 crons |
| ADR-08 | ISR + on-demand revalidate | Estático con frescura editorial |
| ADR-09 | Upstash Redis para rate-limit | Distributed, free tier, simple SDK |
| ADR-10 | Premium con feature flag desde MVP | Activación Fase 2 sin migraciones |
| ADR-11 | CSP estricta con nonces + strict-dynamic | Defense in depth, AdSense-compatible |
| ADR-12 | TypeScript strict + Zod en boundaries | Type safety + runtime validation |
| ADR-13 | Vitest + Playwright (no Jest) | Velocidad + ESM nativo Next 15 |
| ADR-14 | pnpm como package manager | Lockfile rápido + workspaces ready |

---

## 21. Checklist Results Report

### 21.1 Architect Self-Validation

| # | Item | Estado |
|---|---|---|
| ERD completo de las 16 collections | ✅ Sección 4 |
| Sitemap de rutas (público + admin + API) | ✅ Sección 6 |
| Schema SQL + indexes críticos | ✅ Sección 5 |
| Decisión Cloudinary vs Vercel Blob | ✅ Cloudinary (confirmado con dueño) |
| Estrategia cron jobs | ✅ Vercel Cron, 4 schedules definidos |
| Cache + ISR + on-demand revalidate | ✅ Sección 13.4 + tabla 6.1 |
| Spec webhooks Stripe (Fase 2 ready) | ✅ Sección 6.3 |
| CSP estricta + AdSense + CMP coexistencia | ✅ Sección 13.1 |
| Plan de seeds | ✅ Sección 18 |
| Riesgos técnicos pre-Epic 1 | ✅ Sección 19 con 7 riesgos + mitigaciones |
| ADRs documentados | ✅ Sección 20 con 14 decisiones |
| Stack table con versiones | ✅ Sección 3 |
| Estructura de proyecto detallada | ✅ Sección 8.1 + 10 |
| Auth flow (incluido 2FA TOTP) | ✅ Sección 7.5 + 9.3 |
| Testing strategy concreta | ✅ Sección 14 |
| Coding standards mínimos críticos | ✅ Sección 15 |
| Error handling unificado | ✅ Sección 16 |
| Monitoring stack | ✅ Sección 17 |

### 21.2 Final Decision

✅ **READY FOR @sm (SM) y @dev (Developer Agent).**

El documento provee:
- Stack exacto con versiones.
- 16 collections con TS interfaces.
- Schema SQL + indexes (incluido FTS español).
- Sitemap completo de 25+ rutas públicas + 15+ API/cron.
- 5 workflows críticos diagramados.
- Estructura completa de carpetas Next 15 + Payload 3.
- CSP estricta concreta con allowlist AdSense.
- 7 riesgos pre-Epic 1 con validaciones tempranas y fallbacks.
- 14 ADRs justificados.

El @sm puede ahora transformar las 48 stories del PRD en historias formales sized; el @dev puede ejecutar Story 1.1 sin ambigüedad arquitectónica.

---

## 22. Next Steps

### 22.1 Handoff a @sm (Scrum Master)

```
@sm (River) — modo "Story Refinement & Sprint Planning" para El Mate Digital.

Insumos:
- PRD v1.0:          docs/projects/apple-tech-blog/prd.md
- Architecture v1.0: docs/projects/apple-tech-blog/architecture.md (este documento)
- Brief v1.2:        docs/projects/apple-tech-blog/project-brief.md

Objetivo:
1. Refinar las 48 stories del PRD aplicando el detalle técnico de la arquitectura.
2. Agregar story points (Fibonacci 1/2/3/5/8/13) estimando con base en complejidad.
3. Detectar stories que requieren sub-split según los criterios del PRD.
4. Proponer una secuencia de sprints de 1-2 semanas para los Epics 1-6.
5. Identificar dependencias cross-épica que deban paralelizarse o secuenciarse.

Foco prioritario: Epic 1 (Foundation & Canary) — para que el dev pueda
arrancar inmediatamente con un sprint plan claro.
```

### 22.2 Handoff a @dev (Developer Agent) — post-@sm

```
@dev — modo "Implement Epic 1" para El Mate Digital.

Insumos:
- PRD v1.0:          docs/projects/apple-tech-blog/prd.md
- Architecture v1.0: docs/projects/apple-tech-blog/architecture.md
- Stories refinadas por @sm

Empezar por:
- Story 1.1 (scaffold Next.js + TS + Tailwind + shadcn/ui + Lucide)
- Crear repo nuevo `el-mate-digital` (separado de aios-core)

Constraints clave:
- TypeScript strict
- pnpm como package manager
- Estructura de carpetas en Sección 10 de architecture.md
- Versiones exactas en Sección 3 (tech stack)
- Antes de avanzar a Story 2.3 (Media), validar R1 (Cloudinary + Payload 3) — Sección 19
- Antes de avanzar a Story 1.6 (2FA), revisar 9.3 (auth architecture)
```

---

*— Aria, arquitetando o futuro 🏗️*

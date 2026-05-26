<!--
SYNC IMPACT REPORT
==================
Version change: TEMPLATE → 1.0.0 (initial ratification)
Bump rationale: First concrete constitution materialised from placeholder template;
treated as a MINOR-level introduction of all governance content (no prior numbered
version to break compatibility against).

Modified principles (placeholder → concrete):
  - [PRINCIPLE_1_NAME] → I. Code Quality & Type Safety
  - [PRINCIPLE_2_NAME] → II. Test-First Development (NON-NEGOTIABLE)
  - [PRINCIPLE_3_NAME] → III. User Experience Consistency
  - [PRINCIPLE_4_NAME] → IV. Accessibility by Default (WCAG 2.1 AA)
  - [PRINCIPLE_5_NAME] → V. Performance & Real-Time Responsiveness

Added sections:
  - Technology Constraints (locked stack derived from cinema-app-docs/techstack.md)
  - Development Workflow & Quality Gates
  - Governance

Removed sections: none

Templates requiring updates:
  - .specify/templates/plan-template.md          ⚠ pending review (Constitution Check
    section should explicitly enumerate the five gates: code quality, TDD, UX
    consistency, accessibility, performance — currently generic)
  - .specify/templates/spec-template.md          ✅ no change required (functional
    spec template is principle-agnostic)
  - .specify/templates/tasks-template.md         ⚠ pending review (ensure task
    categories cover a11y audits, perf budgets, and contract/integration tests)
  - .specify/templates/checklist-template.md     ✅ no change required
  - .github/agents/speckit.*.agent.md            ✅ no change required (agents read
    this constitution at runtime)

Deferred / follow-up TODOs: none

Source-of-truth documents consulted:
  - cinema-app-docs/stakeholder-req.md (Sofia / AD persona, functional scope,
    success criteria)
  - cinema-app-docs/techstack.md (architecture, a11y target, perf target, security)
-->

# Cinema Pre-Production App Constitution

## Core Principles

### I. Code Quality & Type Safety

End-to-end TypeScript is MANDATORY across `apps/web`, `apps/api`, and any
`packages/shared` workspace. The project MUST run TypeScript in `strict` mode;
disabling `strict`, `noImplicitAny`, or `strictNullChecks` is forbidden without
an approved Complexity Tracking entry. Validation schemas (Zod) MUST be the
single source of truth for any data crossing a trust boundary (HTTP request,
WebSocket message, persisted record) and MUST be shared between client and
server via `packages/shared` — duplicate hand-written types are not allowed.

Every change MUST pass ESLint and Prettier with zero errors before merge. Public
modules and exported functions MUST have explicit return types. Dead code,
commented-out blocks, and `any` casts require justification in the PR
description. Rationale: the target users are creative professionals, not
engineers — silent runtime errors directly damage trust in the "single source of
truth" promise the product makes.

### II. Test-First Development (NON-NEGOTIABLE)

Tests MUST be written before the implementation they cover and MUST fail at
least once (Red) before being made to pass (Green). The required test layers
are:

- **Unit (Vitest)**: pure logic, reducers, Zod schemas, utility functions.
- **Component (React Testing Library)**: every component exposing user
  interaction or conditional rendering.
- **Contract**: every REST endpoint and every WebSocket event MUST have a
  contract test exercising the Zod schema on both sides.
- **Integration**: any flow touching Postgres, Redis, or Blob Storage MUST have
  an integration test using a disposable container or in-memory equivalent.
- **End-to-End (Playwright)**: the critical paths — create project, create
  department space, add checklist item, mark item complete, add comment, attach
  document link — MUST be covered.

Coverage floor: 80% statements for `apps/api` and `packages/shared`; component
tests for 100% of interactive UI components. CI MUST block merges that drop
coverage below the floor or skip tests (`.skip`, `.only`, commented `it`).

### III. User Experience Consistency

All UI MUST be composed from the agreed design system: **Tailwind CSS** tokens
+ **shadcn/ui** primitives on top of **Radix UI**. Ad-hoc styling via inline
styles, raw CSS files, or competing component libraries is forbidden. Icons MUST
come from **Lucide React** (SVG); raster icons and emojis-as-UI are not allowed.

Navigation structure MUST mirror the product's mental model: project → workspace
→ department. Every department space MUST expose the same five regions in the
same order: **objectives, next steps, stages, progress, documents** (per
stakeholder requirement §4.1). Status, progress, and role indicators MUST use
the shared `<Status>`, `<Progress>`, and `<RoleBadge>` components — never
re-implemented per screen.

Copy defaults to **PT-BR** via `react-i18next`. No user-facing string may be
hard-coded; all strings live in translation files so that an EN locale can be
added without code changes. Forms MUST use React Hook Form + Zod with inline,
field-level error messages — toast-only validation is not acceptable.

### IV. Accessibility by Default (WCAG 2.1 AA)

Every interactive element MUST be reachable and operable by keyboard alone, with
a visible focus indicator. Components MUST be built on Radix primitives (or
shadcn/ui wrappers thereof) to inherit WAI-ARIA semantics; bespoke
`<div onClick>` controls are forbidden. Color contrast MUST meet WCAG 2.1 AA
(4.5:1 text, 3:1 large text and UI components).

Automated gate: `@axe-core/react` MUST run in development and report zero
violations on every Playwright E2E run; CI MUST fail on any new violation. A
**Lighthouse Accessibility score ≥ 95** is required for the public routes
(login, project home, department space) and is enforced in CI. Images and icons
that convey meaning MUST have `alt` text or `aria-label`; decorative assets MUST
be marked `aria-hidden`.

### V. Performance & Real-Time Responsiveness

Performance budgets are contractual, not aspirational:

- **TTI < 3 s** on a mid-tier 4G connection for the project home and department
  space routes (measured by Lighthouse in CI).
- **Initial JS payload < 250 KB gzipped** per route; route-level code splitting
  via `React.lazy` + `Suspense` is REQUIRED for non-critical screens.
- **API p95 latency < 300 ms** for read endpoints, **< 500 ms** for writes,
  measured against the staging environment.
- **WebSocket event delivery p95 < 1 s** end-to-end (publisher → subscriber)
  for checklist updates, comments, and presence — the "quasi real-time"
  collaboration promise of the product.
- Data-fetching MUST go through **TanStack Query**; raw `fetch`/`axios` calls in
  components are not allowed. Server state and client state MUST remain
  separated (TanStack Query vs. Zustand).
- Database access MUST use **Prisma** with connection pooling enabled. Any
  query identified as N+1 in code review MUST be fixed before merge.

Regressions against any budget MUST be either fixed or formally accepted via a
Complexity Tracking entry in the affected feature's plan.

## Technology Constraints

The stack defined in `cinema-app-docs/techstack.md` is the locked baseline for
this product. Substitutions require a constitution amendment.

- **Frontend**: React 18 + TypeScript, Vite, Tailwind CSS, shadcn/ui, Radix UI,
  Lucide React icons, TanStack Query, Zustand, React Hook Form + Zod, React
  Router, TipTap (rich text), Socket.IO client and/or Y.js (collaboration).
- **Backend**: Node.js 20 LTS, Fastify, TypeScript, Prisma, Zod, Socket.IO
  and/or Hocuspocus, Auth.js or Lucia with OAuth/OIDC (Google, GitHub,
  Microsoft), CASL or equivalent RBAC, BullMQ for background jobs.
- **Data & infra**: PostgreSQL 16 (primary), Redis (cache, pub/sub, queues),
  Azure Blob Storage (or S3-compatible) for files, Postgres full-text search at
  MVP.
- **Hosting (target)**: Azure Static Web Apps (frontend), Azure Container Apps
  (API + WebSocket), Azure Database for PostgreSQL Flexible Server, Azure
  Managed Redis (`Balanced_B1`), Azure Blob Storage, Microsoft Entra External
  ID, Application Insights + Log Analytics, Azure Key Vault + Managed Identity.
- **Security baseline (MUST)**: HTTPS/TLS 1.2+, OAuth 2.0/OIDC login, `HttpOnly`
  + `SameSite=Lax` session cookies, CSRF tokens on state-changing routes, rate
  limiting per IP and per user, RBAC per project/department, audit log of
  sensitive events, secrets in Key Vault (never in source), LGPD compliance
  (consent capture, account deletion, data export).
- **Repository layout**: monorepo using **pnpm workspaces** (or Turborepo) with
  `apps/web`, `apps/api`, and `packages/shared` (Zod schemas, shared types,
  domain constants).

## Development Workflow & Quality Gates

1. **Branching**: all work happens on feature branches created via the
   `speckit.git.feature` hook. Direct commits to `main` are forbidden.
2. **Pull requests** MUST include: linked spec, summary of changes, test
   evidence (CI green), and an explicit checklist confirming the five Core
   Principles are upheld (or pointing to the Complexity Tracking entry that
   justifies a deviation).
3. **CI gates (blocking merge)**: typecheck, ESLint, Prettier, unit + component
   + contract + integration tests, Playwright E2E on critical paths, axe-core
   on E2E runs, Lighthouse perf + a11y budgets on the three public routes,
   `pnpm audit` (high severity blocks).
4. **Observability**: every API route MUST emit structured logs (request id,
   user id, route, duration, status) to Application Insights; the frontend MUST
   report unhandled errors to Sentry.
5. **Specs-first**: no implementation PR is reviewed without a corresponding
   spec under `specs/<feature>/` produced via the Spec-Kit workflow.
6. **Definition of Done** for a feature: all five Core Principles satisfied,
   spec updated to reflect any in-flight changes, docs/i18n keys added, perf
   and a11y budgets verified in CI.

## Governance

This constitution supersedes ad-hoc conventions, individual preferences, and
any conflicting guidance in `README.md` or runtime agent files. Where this
document and `cinema-app-docs/techstack.md` disagree, this constitution wins
and the techstack doc MUST be reconciled in the same PR.

**Amendment procedure**: amendments are proposed via a PR that (a) edits this
file, (b) updates the Sync Impact Report at the top, (c) lists every dependent
template touched, and (d) is approved by at least one maintainer. Amendments
that introduce or remove a Core Principle, or that loosen a NON-NEGOTIABLE
rule, require explicit stakeholder sign-off (AD persona representative or
product owner).

**Versioning policy** (semantic):

- **MAJOR**: removal or backward-incompatible redefinition of a Core Principle
  or governance rule.
- **MINOR**: addition of a new principle, section, or materially expanded rule.
- **PATCH**: wording, clarification, typo, or non-semantic refinement.

**Compliance review**: every PR reviewer MUST verify the five Core Principle
gates. The Spec-Kit `plan-template.md` Constitution Check section is the
canonical checklist; reviewers MAY block merges on unjustified deviations.
Quarterly, the maintainers MUST run a constitution audit (sample 5 recent PRs,
confirm gates were applied) and record the result in `cinema-app-docs/`.

**Runtime guidance**: agents and contributors SHOULD consult
`.github/agents/speckit.*.agent.md` for workflow guidance and
`cinema-app-docs/techstack.md` for the locked technology baseline.

**Version**: 1.0.0 | **Ratified**: 2026-05-26 | **Last Amended**: 2026-05-26

# Stack Técnica — Requisitos Não Funcionais

> Documento técnico derivado dos requisitos não funcionais do app de pré-produção audiovisual (Sofia / AD).
> Foco: **web app**, **usabilidade**, **acessibilidade**, **organização visual** e **colaboração quase em tempo real**.

---

## 1. Visão Geral da Arquitetura

```
┌─────────────────────────────────────────────────────────────┐
│  Cliente (Browser)                                          │
│  React + TypeScript + Vite + Tailwind + shadcn/ui           │
└───────────────┬─────────────────────────────────────────────┘
                │ HTTPS / WSS
┌───────────────▼─────────────────────────────────────────────┐
│  API Layer                                                  │
│  Node.js + Fastify (REST) + WebSocket (colaboração)         │
└───────────────┬─────────────────────────────────────────────┘
                │
┌───────────────▼─────────────────────────────────────────────┐
│  Dados & Infra                                              │
│  PostgreSQL · Redis · Blob Storage · Auth (OAuth/OIDC)      │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Frontend (Web)

| Camada | Tecnologia | Justificativa |
|---|---|---|
| Framework | **React 18 + TypeScript** | Padrão maduro, ecossistema rico, tipagem estática reduz bugs. |
| Build tool | **Vite** | Dev server rápido, HMR instantâneo. |
| Estilo | **Tailwind CSS** | Design system consistente, estética clean, produtividade alta. |
| Componentes UI | **shadcn/ui** + **Radix UI** | Componentes acessíveis (WAI-ARIA) prontos, customizáveis. |
| Ícones / Assets | **SVG (Lucide React)** | Vetoriais, escaláveis, qualidade profissional. |
| Estado servidor | **TanStack Query (React Query)** | Cache, sincronização e revalidação automática. |
| Estado cliente | **Zustand** | Leve, simples, sem boilerplate. |
| Formulários | **React Hook Form + Zod** | Validação tipada e performática. |
| Roteamento | **React Router** | Padrão do ecossistema React. |
| Editor de texto rico | **TipTap** (sobre ProseMirror) | Para resumos, anotações e documentos colaborativos. |
| Real-time client | **Socket.IO client** ou **Y.js** | Colaboração quase em tempo real (CRDT com Y.js para edição compartilhada). |

**Requisitos atendidos:** usabilidade, organização visual, acessibilidade (Radix), colaboração em tempo real.

---

## 3. Backend (API)

| Camada | Tecnologia | Justificativa |
|---|---|---|
| Runtime | **Node.js 20 LTS** | Mesma linguagem do front (produtividade), maduro. |
| Framework HTTP | **Fastify** | Alta performance, plugins, schemas JSON nativos. |
| Linguagem | **TypeScript** | Tipagem ponta-a-ponta com o front. |
| ORM | **Prisma** | Migrations versionadas, schema declarativo, type-safe. |
| Validação | **Zod** | Compartilhada com o front. |
| WebSocket | **Socket.IO** ou **Hocuspocus** (servidor Y.js) | Atualizações em tempo real e edição colaborativa. |
| Autenticação | **Auth.js (NextAuth)** ou **Lucia** + **OAuth (Google/GitHub)** | Login simples para usuários não-técnicos. |
| Autorização | **CASL** ou políticas próprias (RBAC por projeto/departamento) | Controle por papel (AD, diretor, chefe, membro). |
| Background jobs | **BullMQ** (sobre Redis) | Notificações, processamento de uploads, e-mails. |

**Requisitos atendidos:** colaboração em tempo (quase) real, organização por papéis.

---

## 4. Banco de Dados e Armazenamento

| Recurso | Tecnologia | Uso |
|---|---|---|
| Banco principal | **PostgreSQL 16** | Projetos, departamentos, tarefas, checklists, comentários, papéis. |
| Cache / Pub-Sub | **Redis** | Sessões, presença online, filas de jobs, pub/sub do Socket.IO. |
| Arquivos | **Azure Blob Storage** (ou S3 compatível) | Moodboards, planilhas, anexos. |
| Busca textual | **PostgreSQL full-text search** (MVP) → Meilisearch (futuro) | Procurar documentos/tarefas no projeto. |

---

## 5. Tempo Real e Colaboração

| Cenário | Tecnologia |
|---|---|
| Presença ("quem está online no projeto") | Socket.IO + Redis pub/sub |
| Notificações in-app (comentários, conclusões) | Socket.IO eventos + persistência em Postgres |
| Edição compartilhada de documentos | **Y.js + Hocuspocus** (CRDT) |
| Atualização de checklist em tempo real | Socket.IO room por projeto/departamento |

---

## 6. Acessibilidade (a11y)

- Componentes baseados em **Radix UI** (conformidade WAI-ARIA).
- Verificação automatizada com **axe-core** + **@axe-core/react** em dev.
- Auditoria periódica com **Lighthouse** (meta: score ≥ 95 em Accessibility).
- Suporte a navegação por teclado e leitores de tela em todas as telas principais.
- Contraste mínimo **WCAG 2.1 AA**.

---

## 7. Hospedagem e Infraestrutura

### Opção recomendada (Azure)

| Componente | Serviço Azure |
|---|---|
| Frontend estático | **Azure Static Web Apps** |
| API + WebSocket | **Azure Container Apps** (suporta WebSocket nativamente) |
| Banco | **Azure Database for PostgreSQL — Flexible Server** |
| Cache | **Azure Managed Redis** (`Balanced_B1`) |
| Arquivos | **Azure Blob Storage** |
| Identidade | **Microsoft Entra External ID** (login social) |
| Observabilidade | **Application Insights** + **Log Analytics** |
| Segredos | **Azure Key Vault** + **Managed Identity** |

### Alternativa simples (MVP rápido)

| Componente | Serviço |
|---|---|
| Frontend | Vercel |
| API | Railway / Render / Fly.io |
| Banco | Neon (Postgres serverless) |
| Cache | Upstash Redis |
| Arquivos | Cloudflare R2 |

---

## 8. Qualidade, CI/CD e DevEx

| Área | Ferramenta |
|---|---|
| Lint / Format | **ESLint** + **Prettier** |
| Testes unitários | **Vitest** |
| Testes de componente | **React Testing Library** |
| Testes E2E | **Playwright** |
| Validação de tipos | **TypeScript strict mode** |
| Versionamento | **Git + GitHub** |
| CI/CD | **GitHub Actions** |
| Containerização | **Docker** (API) + Static Web App (front) |
| IaC | **Bicep** (Azure) ou **Terraform** |
| Monitoramento erros front | **Sentry** |

---

## 9. Segurança

- **HTTPS obrigatório** (TLS 1.2+).
- **OAuth 2.0 / OIDC** para login (Google, GitHub, Microsoft).
- **JWT** ou sessões em cookie `HttpOnly` + `SameSite=Lax`.
- **CSRF protection** via tokens em rotas state-changing.
- **Rate limiting** (Fastify plugin) por IP e por usuário.
- **RBAC**: papéis por projeto (AD, diretor, chefe de depto, membro, leitor).
- **Auditoria**: log de eventos sensíveis (criação/remoção de itens).
- **Secrets** fora do código (Key Vault / variáveis de ambiente).
- Conformidade com **LGPD** (consentimento, exclusão de conta, exportação de dados).

---

## 10. Performance e Escalabilidade

- **SSR/SPA híbrido** opcional (Vite SPA basta para MVP).
- **CDN** para assets estáticos (Azure Front Door / Cloudflare).
- **Connection pooling** no Postgres (**PgBouncer** ou pool nativo do Prisma).
- **Cache** de queries frequentes em Redis.
- **Lazy loading** de rotas e componentes pesados (React.lazy + Suspense).
- **Imagens otimizadas**: WebP/AVIF + lazy load nativo.
- Meta de **TTI < 3s** em conexão 4G mediana.

---

## 11. Internacionalização

- **i18next** + **react-i18next**.
- Idioma padrão: **PT-BR** (público-alvo brasileiro).
- Estrutura preparada para adicionar **EN** sem refatoração.

---

## 12. Resumo: Mapeamento Requisito → Tecnologia

| Requisito Não Funcional | Tecnologia/Estratégia |
|---|---|
| **Usabilidade** (interface clean, sem treino) | React + Tailwind + shadcn/ui + design system consistente |
| **Acessibilidade** (web, qualquer navegador) | PWA-ready, Radix UI (WAI-ARIA), responsivo Tailwind |
| **Organização visual** (departamentos claros) | Layout por workspace/projeto/depto, navegação lateral |
| **Colaboração quase em tempo real** | Socket.IO + Y.js (CRDT) + Redis pub/sub |
| **Segurança e privacidade** | OAuth/OIDC, RBAC, Key Vault, LGPD-compliant |
| **Escalabilidade** | Container Apps + Postgres Flexible + Redis + CDN |

---

## 13. Próximos Passos Sugeridos

1. Validar o stack com a equipe (custo Azure vs. alternativa Vercel/Neon).
2. Definir **MVP enxuto**: projetos, departamentos, checklists, comentários, upload de links.
3. Adiar para v2: edição colaborativa (Y.js), busca avançada (Meilisearch), notificações push.
4. Criar repositório monorepo (**Turborepo** ou **pnpm workspaces**) separando `apps/web`, `apps/api`, `packages/shared`.
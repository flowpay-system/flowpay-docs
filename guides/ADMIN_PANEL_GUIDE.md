<!-- markdownlint-disable MD003 MD007 MD013 MD022 MD023 MD025 MD029 MD032 MD033 MD034 -->

```text
========================================
        FLOWPAY · ADMIN PANEL GUIDE
========================================
Status: ACTIVE
Surface: /admin
Runtime: flowpay-app + flowpay-api
========================================
```

## ⟠ Objetivo

Definir o contrato operacional do painel admin do FlowPay
no estado atual do ecossistema.

Este guia substitui a leitura legada de painel estático em
`public/admin/*`.

────────────────────────────────────────

## ⧉ Topologia

```text
flowpay.cash
└── marketing institucional
    └── redireciona /admin para app.flowpay.cash/admin

app.flowpay.cash
└── aplicação Vue 3 / PWA
    └── entrega a interface de admin em /admin

api.flowpay.cash
└── Cloudflare Worker
    ├── POST /api/admin/auth/login
    ├── GET  /api/admin/auth/session
    ├── POST /api/admin/auth/logout
    ├── GET  /api/admin/users
    ├── POST /api/admin/users
    └── GET  /api/admin/metrics
```

Leitura curta:
- o marketing não hospeda o painel admin
- a app transacional entrega a interface
- a API edge sustenta autenticação, métricas e moderação

────────────────────────────────────────

## ⨷ Acesso

```text
Produção
└── https://app.flowpay.cash/admin

Marketing
└── https://flowpay.cash/admin
    └── redireciona para a app

Local app
└── http://localhost:5173/admin
```

Contrato de entrada:
- acesse `/admin` na app
- a sessão admin é validada pela API edge
- sem sessão válida, o fluxo retorna para `/login`

────────────────────────────────────────

## ⍟ Autenticação

O painel usa autenticação por senha administrada no worker.

```text
Credencial obrigatória
└── ADMIN_PASSWORD

Cookie de sessão
└── flowpay_admin_session
```

Fluxo:
1. o operador envia a senha para
   `POST /api/admin/auth/login`
2. a API cria a sessão admin por cookie
3. a app valida presença da sessão com
   `GET /api/admin/auth/session`
4. o logout encerra a sessão em
   `POST /api/admin/auth/logout`

────────────────────────────────────────

## ◬ Capacidades Atuais

```text
▓▓▓ ADMIN CAPABILITIES
────────────────────────────────────────
└─ visão de métricas operacionais
└─ fila de usuários pendentes
└─ aprovação, rejeição e suspensão de contas
└─ paginação de usuários
└─ feedback visual para ações administrativas
```

Estado real da interface:
- a tela vive em `flowpay-app/src/views/AdminView.vue`
- a rota protegida vive em `flowpay-app/src/router/index.ts`
- o backend admin vive em `flowpay-api/src/worker.ts`

────────────────────────────────────────

## ⧖ Desenvolvimento Local

Pré-requisitos:
- Node.js 20+
- `pnpm`

Comandos no `flowpay-app`:

```bash
pnpm install
pnpm dev
pnpm check
```

Contrato local:
- `pnpm dev` sobe a app Vue em modo de desenvolvimento
- a rota de admin fica disponível em `/admin`
- configure `VITE_API_BASE_URL` para apontar ao edge correto

Se a sessão admin falhar, valide primeiro:
- `ADMIN_PASSWORD` no worker
- conectividade com `https://api.flowpay.cash`
- resposta de `GET /api/admin/auth/session`

────────────────────────────────────────

## ⍟ Produção

```text
App runtime
└── Railway

API runtime
└── Cloudflare Workers

Admin base URL
└── https://app.flowpay.cash/admin
```

Checklist de produção:
- `ADMIN_PASSWORD` configurado no worker
- `app.flowpay.cash` servindo a app correta
- `api.flowpay.cash` respondendo endpoints admin
- redirecionamento de `flowpay.cash/admin` preservado

────────────────────────────────────────

## ⨂ Verificação Rápida

```bash
curl -i https://api.flowpay.cash/api/admin/auth/session
curl -i -X POST https://api.flowpay.cash/api/admin/auth/logout
```

Validação manual:
1. abrir `https://app.flowpay.cash/admin`
2. autenticar com a senha administrativa vigente
3. confirmar carregamento de métricas e usuários
4. validar ação de aprovação ou suspensão

────────────────────────────────────────

## ◲ Anti-Drift

Este documento fica incorreto se voltar a afirmar qualquer um
dos pontos abaixo:

- que o painel vive em `public/admin/*`
- que `/admin` é servido diretamente pelo marketing
- que o fluxo local principal depende de `make`
- que o painel opera fora da app Vue

```text
▓▓▓ NΞØ MELLØ
────────────────────────────────────────
Core Architect · NΞØ Protocol
neo@neoprotocol.space

"Code is law. Expand until
chaos becomes protocol."

Security by design.
Exploits find no refuge here.
────────────────────────────────────────
```

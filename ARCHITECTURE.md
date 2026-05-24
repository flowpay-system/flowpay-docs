<!-- markdownlint-disable MD003 MD007 MD013 MD022 MD023 MD025 MD029 MD032 MD033 MD034 -->

> Fonte canônica de topologia e arquitetura do protocolo.
> Última atualização: Mar/2026 — Stack em produção.

```text
========================================
         FLOWPAY · ARCHITECTURE
========================================
Status: ACTIVE
Model: Híbrido (Railway + Cloudflare)
========================================
```

## ⟠ Visão Global

Modelo híbrido baseado em execução edge e servidores SSR.
Dois serviços operam no Railway (SSR Node.js) enquanto um Worker
processa regras de negócio na borda (Cloudflare).

────────────────────────────────────────

## ⧉ Domínios e Responsabilidades

```text
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
┃ MAPA DE SERVIÇOS
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
┃ [flowpay.cash]
┃ Host/Stack : Railway (Node.js SSR) + Astro 5
┃ Repo       : flowpay-marketing
┃ Função     : Landing page, institucional, SEO, CTA de cadastro
┃
┃ [api.flowpay.cash]
┃ Host/Stack : Cloudflare Workers + D1 (SQLite)
┃ Repo       : flowpay-api
┃ Função     : API edge, auth, sessão, PIX, webhook, painel, saque
┃
┃ [app.flowpay.cash]
┃ Host/Stack : Railway (Node.js estático) + Vue 3 PWA
┃ Repo       : flowpay-app
┃ Função     : Dashboard do vendedor, checkout público, magic-link
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

────────────────────────────────────────

## ⨷ Diagrama de Componentes

```text
┌─────────────────────────────────────────────────────────────────────┐
│  Browser / PWA                                                      │
│                                                                     │
│  flowpay.cash        app.flowpay.cash                               │
│  (Astro SSR)         (Vue 3 SPA/PWA)                                │
│       │                    │                                        │
│       └────────────────────┘                                        │
│                    │  fetch + credentials: include                  │
└────────────────────┼────────────────────────────────────────────────┘
                     │
          ┌──────────▼──────────┐
          │  api.flowpay.cash   │   Cloudflare Workers (edge global)
          │                     │
          │  • Auth / sessão    │──── cookie flowpay_session
          │  • Magic-link       │──── Resend API (email)
          │  • Cobrança PIX     │──── Woovi / OpenPIX API
          │  • Webhook Woovi    │◄─── POST /api/webhook (HMAC)
          │  • Payment Buttons  │
          │  • Saque vendedor   │──── Woovi Transfer API
          │  • Admin panel      │
          │  • NEO Nexus events │──── POST nexus.neoprotocol.space
          └──────────┬──────────┘
                     │
          ┌──────────▼──────────┐
          │  Cloudflare D1      │   SQLite flowpay_api_prod
          │                     │
          │  users / sessions   │
          │  orders / buttons   │
          │  withdrawals        │
          │  audit_logs         │
          └─────────────────────┘
```

────────────────────────────────────────

## ⍟ Fluxo de Pagamento PIX (Ponta a Ponta)

```text
Comprador                  App (Vue 3)           API Edge (CF)          Woovi
    │                          │                      │                    │
    │── acessa /checkout/:id ──►│                      │                    │
    │                          │── GET buttons/:id ───►│                    │
    │                          │◄── dados do link ────│                    │
    │── preenche CPF + email ──►│                      │                    │
    │                          │── POST /charge ──────►│                    │
    │                          │                      │── POST /charge ────►│
    │                          │                      │◄── QR + copia-cola ─│
    │◄── QR Code + countdown ──│                      │                    │
    │── paga PIX ──────────────────────────────────────────────────────────►│
    │                          │                      │◄── POST /webhook ──│
    │                          │                      │  (HMAC validado)   │
    │                          │                      │── UPDATE order     │
    │                          │                      │   status=APPROVED  │
    │◄── email confirmação ────────────────────────── │  (Resend)          │
vendedor◄─ email notificação ──────────────────────── │  (Resend)          │
```

────────────────────────────────────────

## ◬ Detalhamento por Serviço

### ⟁ 1. flowpay-marketing (Railway)

- **Runtime:** Node.js (Servidor SSR do Astro)
- **Build:** Execução de `pnpm run build` seguida de `node ./dist...`
- **CI/CD:** Push na branch `main` ativa deploy automático no Railway
- **Environment:** Nenhum secret sensível exigido.
  Configura apenas `PUBLIC_API_BASE_URL` e `PUBLIC_APP_URL` (opcionais).
- **Rede:** A porta HTTP é injetada automaticamente pelo Railway via `$PORT`.

### ⟁ 2. flowpay-api (Cloudflare Workers)

- **Runtime:** V8 isolates (latência global na faixa de ~5ms)
- **Database:** Cloudflare D1 através de binding `DB` (`flowpay_api_prod`)
- **Deploy:** Executado via `pnpm run deploy` (wrangler deploy)
- **Variáveis Públicas:** Declaradas em `wrangler.jsonc` no bloco `[vars]`
- **Secrets:** Injetados via wrangler (ver `.env.example` interno)
- **Migrations:** Diretório `migrations/d1/`.
  Aplicadas via `wrangler d1 migrations apply`.

### ⟁ 3. flowpay-app (Railway)

- **Runtime:** Node.js fornecendo healthcheck (`server.mjs`) e assets
- **Build:** `pnpm run build` gerando a pasta `dist/` servida via node
- **CI/CD:** Push na `main` ativa deploy no Railway
- **Healthcheck:** Rota dedicada em `GET /health`
- **Environment:** Variáveis de build `VITE_API_BASE_URL` e `VITE_APP_URL`
- **Rede:** A porta é injetada automaticamente.

────────────────────────────────────────

## ⨂ Integração NEO Nexus (Event Orchestration)

Após a confirmação e liquidação do pagamento, o Worker emite eventos
diretamente para o sistema de orquestração global Nexus:

```text
CF Worker ──POST /webhook──► Nexus (nexus.neoprotocol.space)
          X-Nexus-Signature: HMAC-SHA256(NEXUS_SECRET_NEW)

Nexus ──► reactores (payment.received → liquidação, mint, comms)
```

**Política de Rotação:**
A assinatura exige `NEXUS_SECRET_NEW` ativo.
Durante janelas de migração, `NEXUS_SECRET_OLD` permanece aceito provisoriamente.

────────────────────────────────────────

## ◧ Sessão e Autenticação

- **Magic-link:** Email com token OTP de uso único dispara flow
  POST para `/api/auth/verify`. Emite cookie HTTP-Only.
- **Cookie Stateful:** Chamado `flowpay_session`, escopado para `.flowpay.cash`
  com TTL configurado para 7 dias corridos.
- **Admin Root:** Login via POST para `/api/admin/login` enviando credencial.
  Resulta na emissão do cookie `flowpay_admin`.
- **Serviço-a-Serviço:** Comunicação autenticada pelo cabeçalho HTTP fixo
  `X-API-Key: FLOWPAY_INTERNAL_API_KEY`.

────────────────────────────────────────

> Mudanças profundas de lógica ou banco pertencem ao `flowpay-api`.
> Alterações em SEO ou vitrine institucional vão para `flowpay-marketing`.
> Atualizações de checkout ou painel cabem ao `flowpay-app`.

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

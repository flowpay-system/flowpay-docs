# 🏛️ Topologia e Arquitetura - FlowPay

Este documento descreve a topologia atual da infraestrutura FlowPay, que opera em um modelo **Híbrido (Railway + Cloudflare)**.

## 🌐 Visão Geral dos Domínios

| Domínio / Rota | Hospedagem | Repositório | Tecnologias | Função |
| :--- | :--- | :--- | :--- | :--- |
| `flowpay.cash` | **Railway** | `flowpay-marketing` | Astro (SSR), Node.js | Front-end Público, Landing page, Institucional, Fluxo básico de Auth UI |
| `api.flowpay.cash` | **Cloudflare** | `flowpay-api` | Cloudflare Workers, D1 | Back-end API edge principal, Autenticação, Banco de Dados, Webhooks |
| `app.flowpay.cash` | **Railway** | `flowpay-app` | Vue 3 (PWA, Vite) | Dashboard do usuário, Checkout, Carteira |

---

## 🛠️ Detalhamento da Infraestrutura

### 1. Front-end Institucional (Marketing)
- **Local:** Railway (`railway.json` vive no repositório `flowpay-marketing`)
- **Runtime:** Node.js (Servidor SSR do Astro)
- **Motivo:** O Astro em modo SSR precisa rodar em um contêiner Node (Railway, neste caso) para garantir SEO perfeito, roteamento dinâmico seguro das landing pages e renderização sob demanda.
- **CI/CD:** Automático do GitHub para o Railway (ao dar push na branch `main`).

### 2. Back-end (API Edge & Autenticação)
- **Local:** Cloudflare Workers (`wrangler.toml` vive no repositório `flowpay-api`)
- **Database:** Cloudflare D1 (`flowpay_api_prod`)
- **Motivo:** A API migrou para edge para rodar 100% em Cloudflare Workers (foi extraída do monolito inicial). Isso provê latência global quase zero, enorme segurança e escala automática infinita.
- **Responsabilidades:**
  - Emissão de magic-links por email (usando Resend via remetente `team@flowpay.cash`).
  - Sessões seguras via cookie cross-subdomain (`credentials: include`).
  - Recepção de Webhook da Woovi e processamentos de Pix.
  - Endpoints administrativos e criação de cobranças.
  - Autenticação service-to-service via `X-API-Key` (`FLOWPAY_INTERNAL_API_KEY`).
  - Emissão de eventos para NEO Nexus (`payment.received`, `user.registration_pending`).
  - Suporte a rotação de secrets para assinatura outbound.
  - Endpoints públicos de checkout via Payment Buttons.

### 3. Front-end Operacional (User Dashboard & PWA)
- **Local:** Railway (`server.mjs` + `dist/` em `flowpay-app`)
- **Runtime:** Node.js (build estático Vite com server de healthcheck próprio)
- **Framework:** Vue 3 com Vue Router (PWA-enabled)
- **Rotas operacionais:** `/login`, `/auth/verify`, `/dashboard`, `/checkout/:buttonId`
- **Comunicação:** Consume `https://api.flowpay.cash` com `credentials: include` (cookie cross-subdomain)
- **Motivo:** Dashboard transacional requer state reativo em tempo real, cache PWA para modo offline, e deploy ágil.
- **CI/CD:** Automático do GitHub para o Railway (ao dar push na branch `main`).
- **Status:** Operacional (Mar/2026)

---

## 🔗 Integração com NEO Nexus (Event Orchestration)

A arquitetura implementa um fluxo de eventos descentralizados:

1. **Payment Received Flow:**
   - FlowPay (Cloudflare) recebe webhook de confirmação da Woovi
   - FlowPay valida assinatura HMAC Woovi
   - FlowPay emite evento para NEO Nexus via `sendNexusWebhook()`
   - Header: `X-Nexus-Signature` (HMAC-SHA256 com NEXUS_SECRET)

2. **Nexus Processing:**
   - Nexus valida signature do FlowPay
   - Nexus dispara reactores registrados (ex: payment-to-mint)
   - Reactores chamam serviços externos (Smart Factory para mint)

3. **Result Notification:**
   - Smart Factory retorna via webhook para Nexus
   - Nexus dispara mint-to-notify reactor
   - Neobot recebe evento via WebSocket e notifica usuário

**Deployment:** Railway (`nexus.neoprotocol.space`)
**Security:** HMAC-SHA256 com secret rotation (`NEXUS_SECRET_NEW`/`NEXUS_SECRET_OLD`)

---

## 🔄 Fluxo de Comunicação

A aplicação trabalha de forma descentralizada baseada em APIs cliente-servidor:
1. O usuário acessa de forma pública `flowpay.cash` (Front entregue pelo Railway).
2. O usuário preenche o email e pede para fazer login.
3. O front-end dispara chamadas `fetch`/AJAX por debaixo dos panos diretamente para `https://api.flowpay.cash/...` (borda Cloudflare).
4. O Cloudflare Worker executa a lógica, confere informações do banco relacional D1 e responde ao browser com os Headers e Cookies aplicáveis, firmando a sessão do cliente.

*Sempre que houver alteração de rotas ou dados cruciais de sistema transacional, o desenvolvimento deve envolver a estrutura em Cloudflare (`flowpay-api`). Mudanças visuais de páginas e SEO focam no repositório `flowpay-marketing` rodando via Railway.*

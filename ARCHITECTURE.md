# 🏛️ Topologia e Arquitetura - FlowPay

Este documento descreve a topologia atual da infraestrutura FlowPay, que opera em um modelo **Híbrido (Railway + Cloudflare)**.

## 🌐 Visão Geral dos Domínios

| Domínio / Rota | Hospedagem | Repositório | Tecnologias | Função |
| :--- | :--- | :--- | :--- | :--- |
| `flowpay.cash` | **Railway** | `flowpay-marketing` | Astro (SSR), Node.js | Front-end Público, Landing page, Institucional, Fluxo básico de Auth UI |
| `api.flowpay.cash` | **Cloudflare** | `flowpay-api` | Cloudflare Workers, D1 | Back-end API edge principal, Autenticação, Banco de Dados, Webhooks |
| `app.flowpay.cash` | *(A confirmar)* | `flowpay-app` | Vue / React (PWA) | Aplicação interna/Dashboard do usuário |

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
- **Motivo:** A API foi extraída do monolito para rodar 100% na "Edge network" da Cloudflare. Isso provê latência global quase zera, enorme segurança e escala automática infinita.
- **Responsabilidades:**
  - Emissão de magic-links por email (usando Resend via remetente `team@flowpay.cash`).
  - Sessões seguras e validações SIWE (Sign in with Ethereum).
  - Recepção de Webhook da Woovi e processamentos de Pix.
  - Endpoints administrativos e criação de cobranças.

---

## 🔄 Fluxo de Comunicação

A aplicação trabalha de forma descentralizada baseada em APIs cliente-servidor:
1. O usuário acessa de forma pública `flowpay.cash` (Front entregue pelo Railway).
2. O usuário preenche o email e pede para fazer login.
3. O front-end dispara chamadas `fetch`/AJAX por debaixo dos panos diretamente para `https://api.flowpay.cash/...` (borda Cloudflare).
4. O Cloudflare Worker executa a lógica, confere informações do banco relacional D1 e responde ao browser com os Headers e Cookies aplicáveis, firmando a sessão do cliente.

*Sempre que houver alteração de rotas ou dados cruciais de sistema transacional, o desenvolvimento deve envolver a estrutura em Cloudflare (`flowpay-api`). Mudanças visuais de páginas e SEO focam no repositório `flowpay-marketing` rodando via Railway.*

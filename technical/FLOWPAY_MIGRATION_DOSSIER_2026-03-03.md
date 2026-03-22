# FLOWPAY Migration Dossier
Data: 3 de março de 2026
Responsável: NEØ MELLØ

## 1) Resumo executivo
FlowPay saiu do modo monolítico acoplado em `flowpay.cash/api/*` e entrou em modo edge dedicado com Cloudflare Worker em `api.flowpay.cash/*`.

O que muda no jogo:
- A API agora tem superfície própria, deploy próprio e banco próprio (D1).
- Marketing já aponta para `api.flowpay.cash`.
- Sessão por magic-link está operando em produção com Resend.
- Camada administrativa e webhook Woovi foram migradas para o worker.
- O contrato com repositórios dependentes mudou e precisa ser sincronizado.

## 2) Topologia atual
### Domínios
- `flowpay.cash`: marketing institucional (Astro).
- `app.flowpay.cash`: aplicação transacional (repositório `flowpay-app`, operacional Mar/2026 -- Vue 3 PWA no Railway).
- `api.flowpay.cash`: API edge (Cloudflare Worker `flowpay-api`).

### Infra API
- Runtime: Cloudflare Workers
- Banco: D1 (`flowpay_api_prod`, id `dad1458d-cdcf-4705-9090-d6c45a3c4e84`)
- Rota: `api.flowpay.cash/*`
- workers.dev: `flowpay-api.flowpay-system.workers.dev`

## 3) Mudanças por repositório
## `flowpay-api`
Commits principais:
- `f47a770` bootstrap worker
- `38bc762` auth registro + magic-link em D1
- `95d12e5` dashboard endpoints em D1
- `d06b98e` admin + webhook + SIWE em D1
- `d34b4cf` create-charge + charge status

Principais entregas:
- Migrações D1:
  - [`0001_init.sql`](/Users/nettomello/neomello/flowpay/flowpay-api/migrations/d1/0001_init.sql)
  - [`0002_auth_sessions.sql`](/Users/nettomello/neomello/flowpay/flowpay-api/migrations/d1/0002_auth_sessions.sql)
  - [`0003_dashboard.sql`](/Users/nettomello/neomello/flowpay/flowpay-api/migrations/d1/0003_dashboard.sql)
  - [`0004_admin_webhook_siwe.sql`](/Users/nettomello/neomello/flowpay/flowpay-api/migrations/d1/0004_admin_webhook_siwe.sql)
- Endpoints ativos no worker:
  - `GET /health`, `GET /api/health`
  - `POST /api/auth/registro`
  - `POST /api/auth/magic-start`
  - `POST /api/auth/magic-verify`
  - `POST /api/auth/logout`
  - `POST /api/auth/siwe-challenge`
  - `POST /api/auth/siwe-verify`
  - `GET /api/user/status`
  - `GET /api/user/buttons`
  - `POST /api/user/buttons`
  - `GET /api/user/metrics`
  - `POST /api/create-charge`
  - `GET /api/charge/:id`
  - `GET /api/charge/:id/stream`
  - `POST /api/admin/auth/login`
  - `GET /api/admin/auth/session`
  - `POST /api/admin/auth/logout`
  - `GET /api/admin/users`
  - `POST /api/admin/users`
  - `GET /api/admin/orders`
  - `POST /api/admin/orders`
  - `GET /api/admin/metrics`
  - `GET /api/admin/logs`
  - `POST /api/webhook`

## `flowpay-marketing`
Commits principais:
- `9bb0c62` site público
- `ef83c66` login/verify apontando para `api.flowpay.cash`
- `f317ae9` registro apontando para `api.flowpay.cash`

Mudanças funcionais:
- Login e cadastro já usam API edge.
- Rotas `/admin` redirecionam para `https://app.flowpay.cash/admin...`.

## `flowpay-app`
Estado atual:
- Repositório com frontend operacional (Vue 3 PWA) deployado no Railway em `app.flowpay.cash`.
- Consome `api.flowpay.cash` como base de API.

## `flowpay-infra`
Estado atual:
- Repositório preparado para centralizar DNS/deploy/runbooks.
- Arquivos legados de raiz foram arquivados em `archive/monolith-root`.

## 4) Contratos operacionais novos
## Contrato de entrada para frontends e serviços
- Base URL de API: `https://api.flowpay.cash`
- Cookies de sessão:
  - `flowpay_session` para auth de usuário por magic-link
  - `flowpay_admin_session` para sessão admin
- CORS aceito:
  - `https://flowpay.cash`
  - `https://app.flowpay.cash`

## Segredos obrigatórios no Worker
- `RESEND_API_KEY`
- `ADMIN_PASSWORD`
- `WOOVI_API_KEY`
- `WOOVI_WEBHOOK_SECRET`

Importante:
- Esses segredos foram copiados de `/Users/nettomello/neomello/flowpay/flowpay-marketing/.env` em 3 de março de 2026 e aplicados no worker.

## 5) O que quebrou do legado e precisa alinhamento no ecossistema
Breaking changes para consumidores antigos:
- Chamadas para `https://flowpay.cash/api/...` não são mais o contrato preferencial.
- Contrato oficial agora é `https://api.flowpay.cash/api/...`.
- `GET /api/charge/:id/stream` já existe no edge como canal SSE
  para acompanhamento em tempo real.

Mudanças de integração NEO Protocol:
- No legado, havia notificação explícita de Nexus (`PAYMENT_RECEIVED`) dentro do webhook/polling.
- Na versão edge atual, essa notificação não foi portada ainda.
- Consequência prática: repositórios que dependem do evento Nexus de pagamento podem ficar cegos até reintegração.

## 6) Riscos abertos
## Risco alto
- SIWE está em modo de compatibilidade sem verificação criptográfica completa da assinatura (`signature_verified: false`).
- Isso mantém a UX viva no login, mas não deve ser tratado como autenticação forte para autorização sensível.

## Risco médio
- SSE de status de cobrança existe no edge, mas consumidores antigos
  ainda podem continuar presos ao modelo de polling por drift de docs.
- Fluxo de fallback via `GET /api/charge/:id` continua válido quando
  o cliente não puder manter stream aberto.

## Risco médio
- Integração Nexus/NEO ainda não reconectada no novo webhook edge.

## 7) Provas de operação em produção
Validações executadas em 3 de março de 2026:
- `GET https://api.flowpay.cash/health` com `ok: true`.
- `POST /api/auth/magic-start` funcionando para usuário registrado.
- `POST /api/auth/siwe-challenge` e `POST /api/auth/siwe-verify` respondendo `200`.
- `POST /api/admin/auth/login` respondendo `200` após secret aplicado.
- `POST /api/create-charge` respondendo `200` após `WOOVI_API_KEY`.

## 8) Plano de sincronização para todos os projetos dependentes
## Etapa 1 (imediata)
- Atualizar todos os clientes para usar `https://api.flowpay.cash` como base única.
- Remover referências a `flowpay.cash/api` em SDKs, bots e workers satélites.

## Etapa 2 (24h)
- Reintroduzir contrato de evento para Nexus no worker edge:
  - evento: `PAYMENT_RECEIVED`
  - gatilho: `charge.paid`/`charge.confirmed` validado no webhook
- Padronizar payload para os consumidores do ecossistema.

## Etapa 3 (48h)
- Consolidar consumo de `GET /api/charge/:id/stream` nos clientes
  que precisam de tempo real.
- Encerrar pontos de fallback do legado em jobs e documentação.

## 9) Mensagem pronta para broadcast no NEO Protocol
```
FlowPay atualizou sua arquitetura no dia 3 de março de 2026.

Nova fonte de verdade da API:
https://api.flowpay.cash

Resumo da mudança:
1. Auth (registro + magic-link) migrou para Cloudflare Worker + D1.
2. Endpoints de dashboard e admin migraram para a mesma camada edge.
3. Webhook Woovi e create-charge também migraram.
4. flowpay.cash/api deixou de ser o contrato principal.

Ações para todos os repositórios dependentes:
1. Trocar base URL para https://api.flowpay.cash.
2. Revisar integrações que consumiam eventos Nexus vindos do legado.
3. Preferir /api/charge/:id/stream para UX em tempo real e manter
   polling apenas como fallback.

Contato de coordenação:
NEØ MELLØ
```

# FLOWPAY Dependency Contract
Data de corte: 3 de março de 2026

## 1) Contrato canônico
Toda integração entre repositórios com FlowPay deve assumir:
- API base: `https://api.flowpay.cash`
- App base: `https://app.flowpay.cash`
- Marketing: `https://flowpay.cash`

## 2) Interface HTTP oficial
## Auth
- `POST /api/auth/registro`
- `POST /api/auth/magic-start`
- `POST /api/auth/magic-verify`
- `POST /api/auth/logout`
- `POST /api/auth/siwe-challenge`
- `POST /api/auth/siwe-verify` (compatibilidade)

## Usuário vendedor
- `GET /api/user/status`
- `GET /api/user/buttons`
- `POST /api/user/buttons`
- `GET /api/user/metrics`

## Pagamentos
- `POST /api/create-charge`
- `GET /api/charge/:id`
- `POST /api/webhook`

## Admin
- `POST /api/admin/auth/login`
- `GET /api/admin/auth/session`
- `POST /api/admin/auth/logout`
- `GET /api/admin/users`
- `POST /api/admin/users`
- `GET /api/admin/orders`
- `POST /api/admin/orders`
- `GET /api/admin/metrics`
- `GET /api/admin/logs`

## 3) Interface de dados (D1)
Tabelas centrais:
- `users`
- `audit_logs`
- `auth_tokens`
- `sessions`
- `payment_buttons`
- `orders`
- `admin_sessions`
- `siwe_nonces`
- `wallet_sessions`

## 4) Compatibilidade e lacunas
## Compatível
- Fluxo registro + magic-link.
- Fluxo admin básico.
- Fluxo de criação de cobrança e leitura de status.

## Parcial
- SIWE retorna sucesso operacional, porém sem verificação criptográfica forte da assinatura.

## Não disponível
- `GET /api/charge/:id/stream` (SSE) ainda não implementado no edge.

## Descontinuado como contrato primário
- Chamadas diretas para `https://flowpay.cash/api/*`.

## 5) Secrets obrigatórios por ambiente
- `RESEND_API_KEY`
- `ADMIN_PASSWORD`
- `WOOVI_API_KEY`
- `WOOVI_WEBHOOK_SECRET`

Variáveis de ambiente (não secret) no `wrangler.toml`:
- `CORS_ALLOW_ORIGINS`
- `APP_BASE_URL`
- `RESEND_FROM`

## 6) Impacto para repositórios consumidores
## Repositórios de frontend
Ação:
- Trocar `API_BASE` para `https://api.flowpay.cash`.
- Tratar `401` em `GET /api/user/status` como fluxo normal sem sessão.

## Repositórios de automação e bots (NEXUS/NEOBOT)
Ação:
- Revisar ingestão de eventos de pagamento.
- No estado atual, a emissão de evento Nexus não foi portada para o worker edge.
- Se o bot depende de `PAYMENT_RECEIVED`, deve operar com fallback por polling até a reintegração.

## Repositórios de observabilidade/analytics
Ação:
- Mover coleta para `api.flowpay.cash`.
- Ajustar parsing para `audit_logs` no D1.

## 7) Smoke test mínimo para qualquer consumidor
```bash
curl -sS https://api.flowpay.cash/health

curl -sS -X POST https://api.flowpay.cash/api/auth/magic-start \
  -H "Content-Type: application/json" \
  --data '{"email":"seu-email@dominio.com"}'

curl -sS -X POST https://api.flowpay.cash/api/create-charge \
  -H "Content-Type: application/json" \
  --data '{"wallet":"0x0000000000000000000000000000000000000000","valor":10,"moeda":"BRL","id_transacao":"smoke_contract_001"}'

curl -sS https://api.flowpay.cash/api/charge/smoke_contract_001
```

## 8) Política de sincronização entre repositórios
- Mudança de contrato de API exige:
  - atualização em `flowpay-docs`
  - commit de adaptação em cada consumidor
  - validação de smoke test por domínio consumidor
- Sem isso, o ecossistema volta para o padrão antigo de acoplamento implícito.

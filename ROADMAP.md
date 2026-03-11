# 🗺️ FLOWPay - Roadmap do Protocolo NΞØ

```text
========================================
         F L O W P A Y - ROADMAP
========================================
      PROTOCOLO NΞØ - FASES 2 A 12
      Atualizado: Mar/2026
========================================
```

> **Nota de classificação (Mar/2026):**
> - "Implementado" = em produção no runtime principal (`src/worker.ts`)
> - "Capacidade latente" = código portado em `services/`, mas NÃO plugado no Worker
> - "Pendente" = não iniciado
>
> A API roda em **Cloudflare Workers** (não Pages). Frontend em **Railway**.

## ▓▓▓ FASE 2: BLINDAGEM & HARDENING -- PARCIAL

────────────────────────────────────────
**Objetivo:** Matar o erro do checkout e elevar o padrão de segurança.

- [x] CSP configurada no marketing
- [x] HMAC webhook validation (timingSafeEqual)
- [x] Secret rotation outbound (NEXUS_SECRET_NEW/OLD)
- [ ] Self-host de libs críticas (parcial)
- [ ] SRI em assets externos
- [ ] 0 uso de unsafe-eval (verificar marketing)

**Metas:** Sem bloqueios CSP, TTI < 2s.
**Status:** Segurança core implementada. CSP/SRI pendentes no frontend.

## ▓▓▓ FASE 3: CHECKOUT MODULAR -- IMPLEMENTADO (core)

────────────────────────────────────────
**Objetivo:** Desacoplar meios de pagto.

- [x] Endpoint `/api/create-charge` com Woovi (PIX)
- [x] Payment Buttons (checkout publico via botoes)
- [x] Webhook idempotente c/ validacao de assinatura Woovi
- [x] Service-to-service auth (`X-API-Key` / `FLOWPAY_INTERNAL_API_KEY`)
- [x] Emissao Nexus (`payment.received`) pos-confirmacao
- [ ] Adapter pattern formal (Pix/Cripto) -- capacidade latente em `services/crypto/`
- [ ] Interface unica PaymentProvider -- idem

**Metas:** 99.9% de sucesso no fluxo.
**Status:** PIX operacional em producao. Cripto como capacidade latente.

## ▓▓▓ FASE 4: TRANSPARÊNCIA VIVA -- PENDENTE

────────────────────────────────────────
**Objetivo:** Confiança por auditoria.

- [ ] Log público (redigido) de txs
- [ ] Playground de webhooks
- [x] Status de serviços (health probe em `/api/webhook/health`)
- [ ] Latência em real-time

**Metas:** 0 dúvidas de suporte.
**Status:** Health probe implementado. Demais itens pendentes.

## ▓▓▓ FASE 5: AUTO-CUSTÓDIA UX -- PENDENTE

────────────────────────────────────────
**Objetivo:** Usuário é dono da chave.

- [ ] Wizard "Sua chave, sua regra"
- [ ] Carteira seed fora do app
- [ ] Copy educativa in-product
- [ ] Bloqueio de capturas/prints

**Metas:** >70% entendem auto-custódia.
**Status:** Não iniciado. Depende de wallet integration (capacidade latente em `services/wallet/`).

## ▓▓▓ FASE 6: SPEC PÚBLICA & OPEN -- PENDENTE

────────────────────────────────────────
**Objetivo:** Ecossistema replicável.

- [ ] SPEC.md (fluxos e eventos)
- [ ] SECURITY.md & CONTRIBUTING.md
- [ ] Licença permissiva (MIT)
- [ ] No keys, no data policy

**Metas:** Primeiro PR externo.
**Status:** Não iniciado.

## ▓▓▓ FASE 7: RESILIÊNCIA -- PARCIAL (capacidade latente)

────────────────────────────────────────
**Objetivo:** Melhora sob stress.

- [ ] Fila de reconciliação (jobs) -- capacidade latente em `services/settlement/`
- [ ] Circuit breaker p/ providers
- [ ] Chaos tests automatizados
- [ ] Fallback Pix/Cripto -- capacidade latente em `services/crypto/`

**Metas:** MTTR < 10min.
**Status:** Código de settlement e crypto portado mas NÃO integrado ao Worker.

## ▓▓▓ FASE 8: PERF & CUSTOS -- PENDENTE

────────────────────────────────────────
**Objetivo:** Rápido e barato.

- [x] Edge runtime (Cloudflare Workers — latência <50ms)
- [ ] Bundle splitting (frontend)
- [ ] Métrica de custo por tx
- [ ] Alertas de custos anômalos

**Metas:** p95 < 2.5s.
**Status:** Edge runtime já entrega latência excelente. Métricas pendentes.

## ▓▓▓ FASE 9: DEVEX & SDKS -- PENDENTE

────────────────────────────────────────
**Objetivo:** Integração em minutos.

- [ ] SDK JS (@flowpay/sdk)
- [ ] Exemplos Next.js & Vanilla
- [ ] CLI: flowpay init/verify

**Metas:** Tempo integração < 30min.
**Status:** Não iniciado.

## ▓▓▓ FASE 10: LANÇAMENTO NΞØ -- PENDENTE

────────────────────────────────────────
**Objetivo:** Protocolo, não produto.

- [ ] VSL curta (60-90s)
- [x] Página Marketing (flowpay.cash)
- [ ] Post técnico "Start Here"

**Metas:** 1º cohort de integração.
**Status:** Site marketing ativo. Conteúdo técnico pendente.

## ▓▓▓ FASE 11: GOVERNANÇA MÍNIMA -- PENDENTE

────────────────────────────────────────
**Objetivo:** Decisões transparentes.

- [ ] RFCs leves via issues
- [ ] Roadmap público (Kanban)
- [ ] Policy de divulgação coordenada

**Metas:** Time de review < 72h.
**Status:** Não iniciado.

## ▓▓▓ FASE 12: EXPANSÕES -- PENDENTE

────────────────────────────────────────
**Objetivo:** Satélites, não monólitos.

- [ ] Novos On-ramps & Stablecoins -- capacidade latente em `services/crypto/`
- [ ] Plugins No-code (Embeds)
- [ ] Badge de verificação cripto

**Metas:** Adoção cross-stack.
**Status:** Código de conversão cripto portado mas NÃO integrado.

## ▓▓▓ NΞØ MELLØ

────────────────────────────────────────
Core Architect · NΞØ Protocol
<neo@neoprotocol.space>

"Code is law. Expand until
 chaos becomes protocol."

**Security by design.**
Exploits find no refuge here.
────────────────────────────────────────

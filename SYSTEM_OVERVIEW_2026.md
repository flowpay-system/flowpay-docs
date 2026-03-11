<!-- markdownlint-disable MD003 MD007 MD013 MD022 MD023 MD025 MD029 MD032 MD033 MD034 -->
```text
========================================
     FLOWPay - SYSTEM OVERVIEW
========================================
Audit Phase: MAR/2026 (atualizado)
Status: SECURE & OPERATIONAL
========================================
```

Este documento detalha o ecossistema
FLOWPay, auditoria de segurança e
o roadmap de evolução técnica.

▓▓▓ TOPOLOGIA ATUAL
────────────────────────────────────────
- flowpay.cash .......... Marketing (Railway/Astro)
- app.flowpay.cash ...... Dashboard/Checkout (Railway/Vue 3)
- api.flowpay.cash ...... API Edge (Cloudflare Workers + D1)

▓▓▓ ARQUITETURA & FLOW
────────────────────────────────────────
O FLOWPay atua como bridge entre
BRL (PIX) e ativos digitais (USDT).

└─ Money Flow:
   └─ Marketing (Astro) redireciona para App
   └─ App (Vue 3) coleta wallet e dados
   └─ API Edge cria cobrança via Woovi
   └─ Webhook confirma recebimento
   └─ Nexus orquestra eventos (payment.received)
   └─ Liquidação assistida (USDT)
   └─ Execução On-chain via Viem

▓▓▓ SECURITY AUDIT (JAN/2026)
────────────────────────────────────────
[####] Precisão BigInt ............ OK
[####] Timing Attacks Shield ....... OK
[####] XSS Toast Protection ....... OK
[####] Prod Debug Disabled ......... OK
[####] Wallet Checksum ............ OK
[####] Real USDT Transfer ......... OK

────────────────────────────────────────
REVISÃO TÉCNICA
────────────────────────────────────────
- Aritmética baseada em centavos
  evita erros de rounding em BRL.
- Assinaturas HMAC validadas com
  timingSafeEqual no Webhook.
- Implementação real de transferência
  substitui placeholders/mocks.

▓▓▓ STATUS DO SISTEMA
────────────────────────────────────────
[####] Checkout UI ................ OK
[####] Woovi Integration .......... OK
[####] Webhook Security ........... OK
[####] USDT Transfer .............. OK
[####] Logs & Debug ............... OK
[####] Cloudflare D1 Database ..... OK
[####] App Transacional (Vue 3) ... OK
[####] Nexus Event Emission ....... OK
[####] Service-to-Service Auth .... OK
[####] Secret Rotation ............ OK

▓▓▓ INFRAESTRUTURA PERSISTENTE (LIVE)
────────────────────────────────────────
[####] Cloudflare D1 Database ........ OK
       (ciclo de vida de ordens, sessões, botões)
[####] App.flowpay.cash (Railway) .... OK
       (Vue 3 PWA, Dashboard operacional)
[####] Nexus Event Integration ....... OK
       (payment.received emitido para orquestração)

▓▓▓ TECH DEBT & GAPS
────────────────────────────────────────
└─ O que falta:
   └─ Admin Dashboard UI
      Endpoints /api/admin/* existem, sem interface web.
   └─ Liquidity API (Real-time)
      Taxas dinâmicas por rota (custo fixo por tx implementado).
   └─ Liquidity Monitor & Alerts
      Alertas automatizados de saldo baixo (manual por hora).
   └─ Services Latentes
      blockchain/, crypto/, settlement/, wallet/, monitor/
      existem em código mas NÃO estão integrados ao Worker.

RECOMENDAÇÃO: O sistema é estável e pronto para
produção com PIX/Cripto. Escala contínua exige
Dashboard Admin UI e alertas automatizados.

▓▓▓ NΞØ MELLØ
────────────────────────────────────────
Core Architect · NΞØ Protocol
neo@neoprotocol.space

"Code is law. Expand until
 chaos becomes protocol."

Security by design.
Exploits find no refuge here.
────────────────────────────────────────

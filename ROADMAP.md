<!-- markdownlint-disable MD003 MD007 MD013 MD022 MD023 MD025 MD029 MD032 MD033 MD034 -->

> **Status:** active  
> **Synced:** 2026-03-22  
> **Source of truth:** GitHub Project `flowpay-system/projects/1`

```text
========================================
        FLOWPAY · ROADMAP 2026
========================================
       Canonical surface: Project V2
========================================
```

## ⟠ Objetivo

Traduzir o Project operacional para uma leitura estratégica,
sem inventar uma segunda realidade documental.

Se este arquivo divergir do Project,
o Project vence.

────────────────────────────────────────

## ⧉ Snapshot

```text
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┓
┃ ITEM                                 ┃ VALOR        ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╋━━━━━━━━━━━━━━┫
┃ Project                              ┃ Unified 2026 ┃
┃ Total de itens                       ┃ 22           ┃
┃ Done                                 ┃ 12           ┃
┃ Todo                                 ┃ 10           ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━┛
```

```text
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━┳━━━━━━┓
┃ REPO                                 ┃ DONE ┃ OPEN ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╋━━━━━━╋━━━━━━┫
┃ flowpay-docs                         ┃ 1    ┃ 3    ┃
┃ flowpay-api                          ┃ 4    ┃ 3    ┃
┃ flowpay-app                          ┃ 4    ┃ 0    ┃
┃ flowpay-marketing                    ┃ 3    ┃ 0    ┃
┃ flowpay-infra                        ┃ 0    ┃ 4    ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┻━━━━━━┻━━━━━━┛
```

────────────────────────────────────────

## ⨷ Fila Ativa

```text
▓▓▓ MARÇO
────────────────────────────────────────
2026-03-21  flowpay-docs#2   P1
Sync docs with current edge, app and webhook contract

2026-03-24  flowpay-infra#1  P1
Create staging and production separation with rollback runbooks

2026-03-31  flowpay-infra#2  P1
Establish security ops baseline for secrets, backups and WAF

▓▓▓ ABRIL
────────────────────────────────────────
2026-04-04  flowpay-api#4    P2
Expand seller metrics and export endpoints

2026-04-11  flowpay-api#5    P2
Enforce real CPF/CNPJ and disposable email validation

2026-04-11  flowpay-infra#3  P1
Add observability and alerting for webhook, retry backlog and uptime

2026-04-18  flowpay-infra#4  P2
Provision QuickNode production setup and provider monitoring

▓▓▓ MAIO
────────────────────────────────────────
2026-05-01  flowpay-api#7    P2
Automate Proof-of-Execution anchoring on Base

2026-05-02  flowpay-docs#3   P2
Publish open protocol baseline docs

2026-05-09  flowpay-docs#4   P2
Establish RFC flow and coordinated disclosure policy
```

**Navegação operacional:**
- [flowpay-docs#2](https://github.com/flowpay-system/flowpay-docs/issues/2)
- [flowpay-infra#1](https://github.com/flowpay-system/flowpay-infra/issues/1)
- [flowpay-infra#2](https://github.com/flowpay-system/flowpay-infra/issues/2)
- [flowpay-api#4](https://github.com/flowpay-system/flowpay-api/issues/4)
- [flowpay-api#5](https://github.com/flowpay-system/flowpay-api/issues/5)
- [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)
- [flowpay-infra#4](https://github.com/flowpay-system/flowpay-infra/issues/4)
- [flowpay-api#7](https://github.com/flowpay-system/flowpay-api/issues/7)
- [flowpay-docs#3](https://github.com/flowpay-system/flowpay-docs/issues/3)
- [flowpay-docs#4](https://github.com/flowpay-system/flowpay-docs/issues/4)

────────────────────────────────────────

## ◬ Entregas

```text
▓▓▓ MARÇO
────────────────────────────────────────
2026-03-19  flowpay-docs#1
└─ bootstrap do board unificado e backlog vivo

2026-03-21  flowpay-marketing#1
└─ remoção de unsafe-eval e dívida de CSP/CDN

2026-03-24  flowpay-api#1
└─ fila durável de retry para bridge e providers

2026-03-28  flowpay-api#2
└─ verificação SIWE real no edge worker

2026-03-28  flowpay-app#1
└─ persistência de checkout em refresh e recovery

▓▓▓ ABRIL
────────────────────────────────────────
2026-04-04  flowpay-api#3
└─ SSE para status de charge em tempo real

2026-04-10  flowpay-marketing#2
└─ UX de registro alinhada a KYC e approval states

2026-04-11  flowpay-app#2
└─ tracking realtime e estados robustos de pagamento

2026-04-11  flowpay-app#3
└─ dashboard seller com métricas e export actions

2026-04-25  flowpay-api#6
└─ provider abstraction e crypto flow com QuickNode

▓▓▓ MAIO
────────────────────────────────────────
2026-05-15  flowpay-app#4
└─ checkout cripto agnóstico e autocustódia guiada

2026-05-29  flowpay-marketing#3
└─ launch assets e Start Here público
```

────────────────────────────────────────

## ⧇ Mapa de Fases

### ⍟ Phase 2 · Blindagem & Hardening

> **Status:** partial

```text
ENTREGUE
└─ flowpay-marketing#1
   remoção de unsafe-eval e dívida de CSP

└─ flowpay-api#2
   SIWE real no edge worker

EM ABERTO
└─ flowpay-infra#2
   baseline operacional de segurança
```

### ⍟ Phase 3 · Checkout Modular

> **Status:** implemented

```text
ENTREGUE
└─ flowpay-app#1
   persistência de checkout

└─ flowpay-api#3
   stream SSE para charge status

└─ flowpay-app#2
   estados robustos de pagamento

└─ flowpay-api#6
   provider abstraction e crypto flow ativo
```

### ⍟ Phase 4 · Transparência Viva

> **Status:** partial

```text
ENTREGUE
└─ SSE + integração de estados no app
   para transparência de charge em tempo real

EM ABERTO
└─ flowpay-docs#2
   sincronização documental com contrato atual

└─ flowpay-infra#3
   observabilidade e alertas operacionais
```

### ⍟ Phase 5 · Auto-Custódia UX

> **Status:** implemented

```text
ENTREGUE
└─ flowpay-app#4
   checkout cripto agnóstico
   + UX guiada de autocustódia
```

### ⍟ Phase 6 · Spec Pública & Open

> **Status:** todo

```text
EM ABERTO
└─ flowpay-docs#3
   SPEC.md + SECURITY.md + CONTRIBUTING.md
   + LICENSE + no-keys/no-data policy
```

### ⍟ Phase 7 · Resiliência

> **Status:** partial

```text
ENTREGUE
└─ flowpay-api#1
   fila durável de retry

EM ABERTO
└─ flowpay-infra#1
   separação stage/prod e rollback

└─ flowpay-infra#3
   alertas e telemetria
```

### ⍟ Phase 8 · Perf & Custos

> **Status:** partial

```text
ENTREGUE
└─ edge runtime continua como baseline produtiva

EM ABERTO
└─ flowpay-api#4
   expansão de métricas e exports

└─ flowpay-infra#3
   visibilidade operacional e alertas
```

### ⍟ Phase 9 · DevEx & SDKs

> **Status:** todo

```text
SEM ITEM ATIVO
└─ nenhum card do Project trata SDK ou CLI ainda
```

### ⍟ Phase 10 · Lançamento NΞØ

> **Status:** partial

```text
ENTREGUE
└─ flowpay-marketing#3
   launch assets e Start Here público

EM ABERTO
└─ convergência entre docs canônicas
   e narrativa pública
```

### ⍟ Phase 11 · Governança Mínima

> **Status:** partial

```text
ENTREGUE
└─ roadmap público já opera no GitHub Project

EM ABERTO
└─ flowpay-docs#4
   RFC flow e disclosure coordenado
```

### ⍟ Phase 12 · Expansões

> **Status:** partial

```text
ENTREGUE
└─ a camada cripto deixou de ser capacidade latente

EM ABERTO
└─ flowpay-infra#4
   setup produtivo de QuickNode

└─ flowpay-api#7
   automação de Proof-of-Execution
```

────────────────────────────────────────

## ◯ Princípio Operacional

FlowPay não será mais gerido por um checklist monolítico
descolado da execução.

O Project é o grafo operacional.
Este arquivo é a camada de tradução estratégica.

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

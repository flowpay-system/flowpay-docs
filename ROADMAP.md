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
2026-03-19  flowpay-docs#1
Bootstrap unified roadmap board and normalize live backlog

2026-03-21  flowpay-marketing#1
Remove unsafe-eval and CDN debt from marketing CSP path

2026-03-24  flowpay-api#1
Add durable D1 retry queue for bridge and provider failures

2026-03-28  flowpay-api#2
Implement full SIWE signature verification in edge worker

2026-03-28  flowpay-app#1
Persist checkout state across refresh and route recovery

2026-04-04  flowpay-api#3
Add SSE endpoint for realtime charge status

2026-04-10  flowpay-marketing#2
Align registration UX with KYC and approval states

2026-04-11  flowpay-app#2
Integrate realtime charge tracking and robust payment states

2026-04-11  flowpay-app#3
Complete seller dashboard with expanded metrics and export actions

2026-04-25  flowpay-api#6
Activate provider abstraction and QuickNode-backed crypto flow

2026-05-15  flowpay-app#4
Add provider-agnostic crypto checkout and self-custody UX

2026-05-29  flowpay-marketing#3
Publish launch assets and public Start Here content
```

────────────────────────────────────────

## ⧇ Mapa de Fases

### ⍟ Phase 2 · Blindagem & Hardening

> **Status:** partial

- entregue:
  - `unsafe-eval` e dívida de CSP atacados em
    [flowpay-marketing#1](https://github.com/flowpay-system/flowpay-marketing/issues/1)
  - SIWE real entregue em
    [flowpay-api#2](https://github.com/flowpay-system/flowpay-api/issues/2)
- em aberto:
  - baseline operacional de segurança em
    [flowpay-infra#2](https://github.com/flowpay-system/flowpay-infra/issues/2)

### ⍟ Phase 3 · Checkout Modular

> **Status:** implemented

- entregue:
  - persistência de checkout em
    [flowpay-app#1](https://github.com/flowpay-system/flowpay-app/issues/1)
  - stream SSE em
    [flowpay-api#3](https://github.com/flowpay-system/flowpay-api/issues/3)
  - estados robustos no app em
    [flowpay-app#2](https://github.com/flowpay-system/flowpay-app/issues/2)
  - abstração de provider e fluxo cripto em
    [flowpay-api#6](https://github.com/flowpay-system/flowpay-api/issues/6)

### ⍟ Phase 4 · Transparência Viva

> **Status:** partial

- entregue:
  - transparência de status em tempo real por SSE
    e integração de estados no app
- em aberto:
  - sincronização documental em
    [flowpay-docs#2](https://github.com/flowpay-system/flowpay-docs/issues/2)
  - observabilidade operacional em
    [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### ⍟ Phase 5 · Auto-Custódia UX

> **Status:** implemented

- entregue:
  - checkout cripto agnóstico e UX guiada de autocustódia em
    [flowpay-app#4](https://github.com/flowpay-system/flowpay-app/issues/4)

### ⍟ Phase 6 · Spec Pública & Open

> **Status:** todo

- em aberto:
  - [flowpay-docs#3](https://github.com/flowpay-system/flowpay-docs/issues/3)

### ⍟ Phase 7 · Resiliência

> **Status:** partial

- entregue:
  - fila durável de retry em
    [flowpay-api#1](https://github.com/flowpay-system/flowpay-api/issues/1)
- em aberto:
  - separação stage/prod e rollback em
    [flowpay-infra#1](https://github.com/flowpay-system/flowpay-infra/issues/1)
  - alertas e telemetria em
    [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### ⍟ Phase 8 · Perf & Custos

> **Status:** partial

- entregue:
  - edge runtime permanece como baseline produtiva
- em aberto:
  - expansão de métricas em
    [flowpay-api#4](https://github.com/flowpay-system/flowpay-api/issues/4)
  - visibilidade operacional em
    [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### ⍟ Phase 9 · DevEx & SDKs

> **Status:** todo

- nenhum item ativo do Project trata SDK ou CLI ainda

### ⍟ Phase 10 · Lançamento NΞØ

> **Status:** partial

- entregue:
  - assets de lançamento e Start Here público em
    [flowpay-marketing#3](https://github.com/flowpay-system/flowpay-marketing/issues/3)
- em aberto:
  - convergência final entre docs canônicas e narrativa pública

### ⍟ Phase 11 · Governança Mínima

> **Status:** partial

- entregue:
  - o roadmap público já opera no GitHub Project
- em aberto:
  - RFC flow e disclosure coordenado em
    [flowpay-docs#4](https://github.com/flowpay-system/flowpay-docs/issues/4)

### ⍟ Phase 12 · Expansões

> **Status:** partial

- entregue:
  - a camada cripto deixou de ser capacidade latente
- em aberto:
  - setup produtivo de QuickNode em
    [flowpay-infra#4](https://github.com/flowpay-system/flowpay-infra/issues/4)
  - automação de Proof-of-Execution em
    [flowpay-api#7](https://github.com/flowpay-system/flowpay-api/issues/7)

────────────────────────────────────────

## ◯ Princípio Operacional

FlowPay não será mais gerido por um checklist monolítico
descolado da execução.

O Project é o grafo operacional.
Este arquivo é a camada de tradução estratégica.

```text
▓▓▓ FLOWPAY SYSTEM
────────────────────────────────────────
Roadmap documental ancorado no Project
────────────────────────────────────────
```

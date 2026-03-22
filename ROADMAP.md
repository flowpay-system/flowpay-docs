# FLOWPay Roadmap 2026

> ⚠️ **CONTEXTO HISTÓRICO — NÃO É SOURCE-OF-TRUTH ATIVA**
>
> Este documento foi a referência de planejamento até Mar/2026.
> O **backlog e execução ativos** agora vivem no [FlowPay Unified Roadmap 2026](https://github.com/orgs/flowpay-system/projects/1) — essa é a superfície canônica de execução.
> Consulte [`PROJECT_CONVENTIONS.md`](./PROJECT_CONVENTIONS.md) para as convenções do board, definição de milestones e workflow de backlog.
> Este arquivo é mantido como **registro histórico das fases planejadas**.

```text
========================================
         FLOWPAY · ROADMAP
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
> Snapshot synced on: `2026-03-22`
>
> Rule: if this document diverges from the GitHub Project, the Project wins.

## Snapshot

- Total tracked items: `22`
- Done: `12`
- Todo: `10`
- Active repositories in the roadmap:
  - `flowpay-docs`: `1 done`, `3 open`
  - `flowpay-api`: `4 done`, `3 open`
  - `flowpay-app`: `4 done`, `0 open`
  - `flowpay-marketing`: `3 done`, `0 open`
  - `flowpay-infra`: `0 done`, `4 open`

## Active Queue

| Target | Repo | Issue | Priority | Outcome |
| --- | --- | --- | --- | --- |
| 2026-03-21 | `flowpay-docs` | [#2 Sync docs with current edge, app and webhook contract](https://github.com/flowpay-system/flowpay-docs/issues/2) | `P1` | Remove contradictions between code, docs and current runtime contract |
| 2026-03-24 | `flowpay-infra` | [#1 Create staging and production separation with rollback runbooks](https://github.com/flowpay-system/flowpay-infra/issues/1) | `P1` | Separate environments and establish rollback discipline |
| 2026-03-31 | `flowpay-infra` | [#2 Establish security ops baseline for secrets, backups and WAF](https://github.com/flowpay-system/flowpay-infra/issues/2) | `P1` | Move security posture from implied to operational |
| 2026-04-04 | `flowpay-api` | [#4 Expand seller metrics and export endpoints](https://github.com/flowpay-system/flowpay-api/issues/4) | `P2` | Strengthen dashboard intelligence and export surface |
| 2026-04-11 | `flowpay-api` | [#5 Enforce real CPF/CNPJ and disposable email validation](https://github.com/flowpay-system/flowpay-api/issues/5) | `P2` | Replace syntactic registration with real validation |
| 2026-04-11 | `flowpay-infra` | [#3 Add observability and alerting for webhook, retry backlog and uptime](https://github.com/flowpay-system/flowpay-infra/issues/3) | `P1` | Make failures visible before users find them |
| 2026-04-18 | `flowpay-infra` | [#4 Provision QuickNode production setup and provider monitoring](https://github.com/flowpay-system/flowpay-infra/issues/4) | `P2` | Turn crypto provider support into a production-grade surface |
| 2026-05-01 | `flowpay-api` | [#7 Automate Proof-of-Execution anchoring on Base](https://github.com/flowpay-system/flowpay-api/issues/7) | `P2` | Operationalize Proof-of-Execution cadence |
| 2026-05-02 | `flowpay-docs` | [#3 Publish open protocol baseline docs](https://github.com/flowpay-system/flowpay-docs/issues/3) | `P2` | Ship `SPEC.md`, `SECURITY.md`, `CONTRIBUTING.md`, `LICENSE` and no-keys/no-data policy |
| 2026-05-09 | `flowpay-docs` | [#4 Establish RFC flow and coordinated disclosure policy](https://github.com/flowpay-system/flowpay-docs/issues/4) | `P2` | Make governance legible before external contribution scales |

## Delivered So Far

| Target | Repo | Issue | What landed |
| --- | --- | --- | --- |
| 2026-03-19 | `flowpay-docs` | [#1 Bootstrap unified roadmap board and normalize live backlog](https://github.com/flowpay-system/flowpay-docs/issues/1) | The GitHub Project became the canonical execution surface |
| 2026-03-21 | `flowpay-marketing` | [#1 Remove unsafe-eval and CDN debt from marketing CSP path](https://github.com/flowpay-system/flowpay-marketing/issues/1) | CSP debt reduction, self-host direction and `unsafe-eval` removal moved from intent to delivery |
| 2026-03-24 | `flowpay-api` | [#1 Add durable D1 retry queue for bridge and provider failures](https://github.com/flowpay-system/flowpay-api/issues/1) | Retry backlog stopped living as theory and became durable state |
| 2026-03-28 | `flowpay-api` | [#2 Implement full SIWE signature verification in edge worker](https://github.com/flowpay-system/flowpay-api/issues/2) | Wallet auth stopped being theater and gained real cryptographic verification |
| 2026-03-28 | `flowpay-app` | [#1 Persist checkout state across refresh and route recovery](https://github.com/flowpay-system/flowpay-app/issues/1) | Checkout stopped forgetting itself on refresh |
| 2026-04-04 | `flowpay-api` | [#3 Add SSE endpoint for realtime charge status](https://github.com/flowpay-system/flowpay-api/issues/3) | Realtime charge streaming became part of the edge contract |
| 2026-04-10 | `flowpay-marketing` | [#2 Align registration UX with KYC and approval states](https://github.com/flowpay-system/flowpay-marketing/issues/2) | Registration narrative aligned to real approval states |
| 2026-04-11 | `flowpay-app` | [#2 Integrate realtime charge tracking and robust payment states](https://github.com/flowpay-system/flowpay-app/issues/2) | App consumed realtime status and hardened payment-state UX |
| 2026-04-11 | `flowpay-app` | [#3 Complete seller dashboard with expanded metrics and export actions](https://github.com/flowpay-system/flowpay-app/issues/3) | Seller dashboard refinement moved ahead of docs |
| 2026-04-25 | `flowpay-api` | [#6 Activate provider abstraction and QuickNode-backed crypto flow](https://github.com/flowpay-system/flowpay-api/issues/6) | Crypto provider abstraction stopped being latent code and became active contract |
| 2026-05-15 | `flowpay-app` | [#4 Add provider-agnostic crypto checkout and self-custody UX](https://github.com/flowpay-system/flowpay-app/issues/4) | Self-custody UX entered the product surface |
| 2026-05-29 | `flowpay-marketing` | [#3 Publish launch assets and public Start Here content](https://github.com/flowpay-system/flowpay-marketing/issues/3) | Launch narrative and public onboarding content shipped |

## Strategic Phase Map

The Project is repo-shaped because execution happens in repos. The protocol narrative below exists to keep strategic continuity.

### Phase 2: Blindagem & Hardening

**Status:** `PARTIAL`

- Done:
  - Marketing CSP debt removal and `unsafe-eval` cleanup shipped via [flowpay-marketing#1](https://github.com/flowpay-system/flowpay-marketing/issues/1)
  - SIWE verification shipped via [flowpay-api#2](https://github.com/flowpay-system/flowpay-api/issues/2)
- Open:
  - Security ops baseline remains active in [flowpay-infra#2](https://github.com/flowpay-system/flowpay-infra/issues/2)

### Phase 3: Checkout Modular

**Status:** `IMPLEMENTED`

- Done:
  - Checkout state recovery via [flowpay-app#1](https://github.com/flowpay-system/flowpay-app/issues/1)
  - Realtime charge stream via [flowpay-api#3](https://github.com/flowpay-system/flowpay-api/issues/3)
  - Realtime payment states in app via [flowpay-app#2](https://github.com/flowpay-system/flowpay-app/issues/2)
  - Provider abstraction and crypto flow via [flowpay-api#6](https://github.com/flowpay-system/flowpay-api/issues/6)

### Phase 4: Transparência Viva

**Status:** `PARTIAL`

- Done:
  - Realtime charge transparency shipped through SSE and app state integration
- Open:
  - Contract and operational visibility still depend on [flowpay-docs#2](https://github.com/flowpay-system/flowpay-docs/issues/2) and [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### Phase 5: Auto-Custódia UX

**Status:** `IMPLEMENTED`

- Done:
  - Provider-agnostic crypto checkout and guided self-custody UX shipped via [flowpay-app#4](https://github.com/flowpay-system/flowpay-app/issues/4)

### Phase 6: Spec Pública & Open

**Status:** `TODO`

- Open:
  - [flowpay-docs#3](https://github.com/flowpay-system/flowpay-docs/issues/3)

### Phase 7: Resiliência

**Status:** `PARTIAL`

- Done:
  - Durable retry queue via [flowpay-api#1](https://github.com/flowpay-system/flowpay-api/issues/1)
- Open:
  - Staging and rollback discipline via [flowpay-infra#1](https://github.com/flowpay-system/flowpay-infra/issues/1)
  - Observability and alerting via [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### Phase 8: Perf & Custos

**Status:** `PARTIAL`

- Done:
  - Core edge runtime remains the production baseline
- Open:
  - Metrics and operational cost visibility still depend on [flowpay-api#4](https://github.com/flowpay-system/flowpay-api/issues/4) and [flowpay-infra#3](https://github.com/flowpay-system/flowpay-infra/issues/3)

### Phase 9: DevEx & SDKs

**Status:** `TODO`

- No active Project item tracks SDK or CLI delivery yet.

### Phase 10: Lançamento NΞØ

**Status:** `PARTIAL`

- Done:
  - Launch assets and public Start Here content shipped via [flowpay-marketing#3](https://github.com/flowpay-system/flowpay-marketing/issues/3)
- Open:
  - Canonical docs still need synchronization before launch narrative and protocol contract fully converge

### Phase 11: Governança Mínima

**Status:** `PARTIAL`

- Done:
  - Public roadmap operation exists in the GitHub Project
- Open:
  - RFC flow and coordinated disclosure still depend on [flowpay-docs#4](https://github.com/flowpay-system/flowpay-docs/issues/4)

### Phase 12: Expansões

**Status:** `PARTIAL`

- Done:
  - Crypto provider abstraction and self-custody UX are no longer latent
- Open:
  - QuickNode production setup via [flowpay-infra#4](https://github.com/flowpay-system/flowpay-infra/issues/4)
  - Proof-of-Execution automation via [flowpay-api#7](https://github.com/flowpay-system/flowpay-api/issues/7)

## Operating Principle

FlowPay is no longer managed as a single monolithic checklist pretending to be strategy. The Project is the operational graph. This file is the strategic translation layer.

# FLOWPAY · PIX > CRYPTO EXECUTION DOSSIER (2026-04-02)

Status: strategic working draft  
Owner: NEØ MELLØ  
Scope: `flowpay-docs` control plane only  
Goal: registrar desenho de execução para `PIX -> liquidação programável -> cripto` com viabilidade, risco e rota de implementação.

---

## 1) Síntese Executiva

O legado tinha a intuição correta e a implementação parcialmente pronta: separar `pagamento`, `liquidação`, `prova` e `entrega`.

O mercado 2025-2026 reduziu drasticamente o custo de montar esse sistema com segurança:

- Bridge já documenta `BRL virtual accounts` com `br_code` para PIX e entrega onchain do destino configurado.
- Bridge também documenta máquina de estados de transferência com finalização explícita.
- Stripe conectou essa infraestrutura ao seu ecossistema, mas parte relevante ainda está em preview em alguns produtos.
- Circle CPN e StableFX amadureceram para uso institucional, porém com pré-requisitos de operação financeira mais exigentes.
- Crossmint e BVNK oferecem atalhos regulatórios e operacionais, com trade-offs de custo, lock-in e elegibilidade.

Decisão estratégica recomendada:

1. Evitar construir a camada regulatória inteira do zero.
2. Usar parceiro para conversão e payout regulado.
3. Manter soberania no que cria vantagem: checkout, ledger, idempotência, regras de negócio, observabilidade, prova e experiência.

---

## 2) O Que o Legado Já Provou

O monólito legado mostrou 3 linhas simultâneas:

- Linha A: `PIX confirmado -> PENDING_REVIEW -> bridge/event bus -> SETTLED/COMPLETED`.
- Linha B: checkout com pagamento cripto direto.
- Linha C: proposta explícita de `liquidação programável` (não "conversão mágica") para `BRL -> USDT`.

Conclusão prática:

- O design de domínio estava correto.
- O fechamento ponta a ponta não estava completamente unificado.
- A dívida principal não era frontend. Era orquestração de compliance + liquidação + finalidade.

---

## 3) Estado do Mercado (2025-2026) com Evidência

### 3.1 Bridge (orchestration-first)

Achados relevantes:

- Virtual accounts são descritas como endereços fiat permanentes/reutilizáveis que convertem fiat em cripto e entregam no destino configurado.
- Incluem `BRL Virtual Accounts` com `BR codes for receiving PIX payments`.
- Fluxo de criação inclui `source.currency = brl` e `destination.currency = usdc`.
- Requisito explícito: cliente precisa estar onboarded e `KYC/KYB-approved`.

Implicação:

- Hoje já existe primitive concreta para `PIX -> stablecoin` sem acoplar manualmente provedor de QR, motor de FX, watcher onchain e payout executor como blocos soltos.

### 3.2 Bridge transfer states (finalidade operacional)

Achados relevantes:

- Estados principais: `awaiting_funds -> funds_received -> payment_submitted -> payment_processed`.
- O hash em `payment_submitted` pode ser preliminar.
- O hash final deve ser o de `payment_processed`.

Implicação:

- Para "code is law", o estado final de negócio deve depender de `payment_processed`, não de hash precoce.

### 3.3 Stripe + Bridge

Achados relevantes:

- Stripe concluiu aquisição da Bridge em 2025.
- Stripe publicou expansão de produtos de stablecoin no período.
- `Stablecoin payouts for Connect` está em private preview e com escopo de elegibilidade específico.

Implicação:

- Stack Stripe/Bridge pode ser rota poderosa, mas partes do pacote têm disponibilidade e cobertura condicionadas.

### 3.4 Circle (CPN + StableFX)

Achados relevantes:

- CPN é orientado a instituições financeiras (OFI/BFI), com requisitos explícitos de liquidez USDC, custódia e KYC/AML.
- StableFX foi lançado em novembro/2025 com RFQ + settlement onchain.

Implicação:

- Excelente para operação institucional de maior escala.
- Menos indicado para v1 enxuta sem estrutura financeira robusta.

### 3.5 Crossmint (regulated abstraction)

Achados relevantes:

- Crossmint posiciona "regulated infrastructure" com API única para receber, enviar, converter e mover stablecoins.
- Declara assumir camada de compliance/licenciamento em flows regulados.
- Diferencia transferências internas de treasury versus payouts regulados para usuários/terceiros.

Implicação:

- Entra forte quando objetivo é acelerar go-live com menor carga regulatória interna.

### 3.6 BVNK (virtual accounts + payout rails)

Achados relevantes:

- Virtual accounts orientadas a instituições reguladas.
- Fluxos de payout com quote prévio, validação de endereço e modelo two-step.

Implicação:

- Alternativa viável para players com perfil regulado e operação FI/PSP.

### 3.7 QuickNode Webhooks (observabilidade onchain)

Achados relevantes:

- Serviço de entrega em tempo real de eventos onchain.
- Segurança recomendada: HMAC + allowlist.
- Suporte a retries e reorg handling.

Implicação:

- Excelente como camada de observabilidade e auditoria onchain.
- Não substitui sozinho o motor de compliance e liquidação fiat/stablecoin.

---

## 4) Trilha Regulatória Brasil (alertas objetivos)

Referências oficiais mostram:

- Marco de SPSAVs com Resoluções BCB 519/520/521 (nov/2025), explicitadas em voto oficial.
- Exigências para operações com carteira autocustodiada incluindo identificação do proprietário e processos documentados para verificar origem e destino dos ativos.

Implicação prática para FlowPay:

- Payout para wallet autocustodiada precisa política e evidência operacional, não apenas endpoint técnico.
- O risco jurídico é de processo, governança e diligência, não só de smart contract.

---

## 5) Estudo de Viabilidade (score 1-5)

Legenda:

- 5 = melhor para velocidade/controlabilidade do objetivo atual.
- 1 = pior para velocidade/controlabilidade do objetivo atual.

| Opção | Time-to-market | Complexidade técnica | Carga regulatória própria | Risco operacional | Soberania de produto | Score total |
| --- | --- | --- | --- | --- | --- | --- |
| Build próprio completo (Woovi + FX + settlement + compliance próprio) | 1 | 1 | 1 | 2 | 5 | 10 |
| Bridge-first | 5 | 4 | 4 | 4 | 3 | 20 |
| Crossmint-first | 4 | 4 | 4 | 4 | 3 | 19 |
| Circle CPN/StableFX-first | 2 | 2 | 3 | 4 | 4 | 15 |
| BVNK-first | 3 | 3 | 3 | 4 | 3 | 16 |
| Híbrido soberano (orquestrador externo + ledger/regra interna forte) | 5 | 4 | 4 | 5 | 5 | 23 |

Leitura:

- Melhor compromisso para FlowPay agora: `Híbrido soberano`.
- "Construir tudo" pode virar vitrine técnica cara e lenta, com risco regulatório evitável.

---

## 6) Arquitetura Recomendada (Híbrida Soberana)

### 6.1 Princípio

Soberania onde gera vantagem:

- checkout UX
- modelo de dados
- idempotência
- orquestração de estados de negócio
- auditoria e prova
- antifraude e monitoramento

Commoditize onde gera risco/atraso:

- FX/regulatory rails
- payouts regulados
- travel rule e screening operacional

### 6.2 Fluxo alvo

```text
1) Checkout cria PaymentIntent interno
2) Cliente paga PIX
3) Evento confirmado no provedor fiat
4) Ledger marca FUNDS_CONFIRMED
5) Orquestrador externo executa liquidação/payout conforme política
6) Receber estado final + tx hash final
7) Ledger marca SETTLED e grava prova
8) Release de produto/acesso
```

### 6.3 Invariantes "code is law"

- Nenhum `COMPLETED` sem `FUNDS_CONFIRMED`.
- Nenhum `SETTLED` sem prova de finalidade (`payment_processed` ou equivalente).
- `tx_hash` preliminar nunca libera entrega.
- Todo evento externo exige assinatura válida e idempotency key.
- Toda transição de estado deve ser monotônica e auditável.

---

## 7) Segurança (hacker não tem vez)

Camadas obrigatórias:

1. Transporte e origem
- Assinaturas HMAC obrigatórias para todos webhooks.
- Allowlist de IP quando fornecedor suportar.

2. Estado e execução
- Idempotência por `payment_intent_id` + `provider_event_id`.
- Máquina de estados imutável por regra.
- Compensações explícitas para falhas.

3. Segredos e custódia
- Proibir chave privada operacional em `.env` como estado final.
- Usar MPC/HSM/custódia regulada para produção.

4. Observabilidade e resposta
- Correlação fim a fim (`trace_id`, `intent_id`, `provider_id`, `tx_hash`).
- Alertas para divergência de estado e latência de confirmação.
- Kill-switch para congelar payout em condição de anomalia.

5. Compliance operacional
- Classificação de contrapartes (self-custody, fornecedor, cliente final).
- Regras de due diligence por tipo de payout.
- Retenção de trilhas para auditoria/regulatório.

---

## 8) Plano de Ataque (90 dias)

### Fase 1 (0-30 dias): Foundation

- Escolher parceiro primário de orquestração.
- Definir contrato de estados canônico do FlowPay.
- Implementar event envelope assinado e idempotente.
- Subir trilha de auditoria mínima.

### Fase 2 (31-60 dias): Controlled launch

- Go-live com corredor controlado (`PIX -> stablecoin` em escopo limitado).
- Payout inicial para destinos de menor risco operacional.
- Rodar reconciliação diária automática.

### Fase 3 (61-90 dias): Expansion

- Expandir corredores e limites.
- Introduzir automações avançadas de risco.
- Preparar política para autocustódia com controles reforçados.

---

## 9) Backlog para Nós NEO PROTOCOL (backend)

Workstreams propostos:

1. `state-machine-core`
- contrato de estados
- invariantes de transição
- idempotência forte

2. `provider-adapter`
- adapter primário (Bridge ou equivalente)
- adapter fallback
- normalização de estados

3. `security-gateway`
- assinatura webhook
- replay protection
- rate-limit + bot filtering

4. `ledger-proof`
- trilha de auditoria imutável
- registro de prova de finalidade
- reconciliação automatizada

5. `compliance-controls`
- classificação de payout
- checagens por tipo de contraparte
- logs para auditoria

---

## 10) Decisão Recomendada Agora

Recomendação objetiva:

1. Jogar com quem já está com a bola regulatória e operacional.
2. Manter o ataque no que diferencia FlowPay: protocolo de negócio, UX, estado e prova.
3. Operar `híbrido soberano` como padrão.

Isso reduz:

- tempo de execução
- superfície de risco jurídico
- custo de manutenção de infraestrutura sensível

E aumenta:

- velocidade de inovação
- capacidade de escalar com segurança
- vantagem competitiva real do protocolo

---

## 11) Fontes Externas Utilizadas

1. Bridge Virtual Accounts (BRL/PIX + delivery onchain):
https://apidocs.bridge.xyz/platform/orchestration/virtual_accounts/virtual-account

2. Bridge Transfer States (finalidade e hash final):
https://apidocs.bridge.xyz/platform/orchestration/transfers/transfer-states

3. Stripe Connect Stablecoin Payouts (preview e limitações):
https://docs.stripe.com/connect/stablecoin-payouts

4. Stripe acquisition of Bridge (oficial):
https://stripe.com/newsroom/news/stripe-completes-bridge-acquisition

5. Stripe Sessions 2025 (stablecoin financial accounts):
https://stripe.com/newsroom/news/sessions-2025

6. Circle CPN overview (prérequisitos OFI/BFI e flow):
https://developers.circle.com/cpn

7. Circle StableFX overview:
https://developers.circle.com/stablefx

8. Circle StableFX release notes (lançamento 2025-11-13):
https://developers.circle.com/release-notes/stablefx-2025

9. Crossmint Stablecoin Orchestration overview:
https://docs.crossmint.com/stablecoin-orchestration/overview

10. Crossmint Payouts/Regulated Transfers overview:
https://docs.crossmint.com/stablecoin-orchestration/regulated-transfers/overview

11. BVNK Virtual Accounts overview:
https://docs.bvnk.com/bvnk/use-cases/virtual-accounts/va-overview/

12. BVNK Create payouts:
https://docs.bvnk.com/bvnk/use-cases/stablecoin-payments-for-platforms/pay-out-to-your-users/

13. QuickNode Webhooks (segurança, retry e operação):
https://www.quicknode.com/docs/webhooks

14. Voto CMN/BCB com referência às Resoluções BCB 519/520/521:
https://normativos.bcb.gov.br/Votos/CMN/20267/Voto_do_CMN_7_2026.pdf

15. Voto BCB 158/2025 (autocustódia, origem/destino, disciplina cambial):
https://normativos.bcb.gov.br/Votos/BCB/2025158/Voto_do_BC_158_2025.pdf

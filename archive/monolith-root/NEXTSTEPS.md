# NEXTSTEPS.md · FLOWPAY

> ⚠️ **ARQUIVO HISTÓRICO — NÃO É SOURCE-OF-TRUTH ATIVA**
>
> Este documento fez parte do monólito original e foi arquivado em Mar/2026.
> Os próximos passos e backlog ativos agora são gerenciados no [GitHub Project Board](https://github.com/orgs/flowpay-system/projects).
> Consulte [`PROJECT_CONVENTIONS.md`](../../PROJECT_CONVENTIONS.md) para o workflow atual.

> Roadmap técnico priorizado — gerado pós-deploy `feat(auth)` + `fix(checkout)` + `fix(ui)`
> Baseado em gap analysis real da sessão de arquitetura.

---

## LEGENDA

```
🔴 CRÍTICO     · Risco em produção hoje
🟡 IMPORTANTE  · Risco em escala / experiência
🟢 EVOLUÇÃO    · Melhoria arquitetural planejada
⬜ BACKLOG     · Futuro, sem urgência
```

---

## SPRINT ATUAL · PÓS-DEPLOY IMEDIATO

### 🔴 S1 · Smoke Test de Produção
**Validar os três commits em ambiente real antes de liberar tráfego.**

```
[ ] Cadastro com @flowpay.cash → magic link chega via Resend
[ ] Log [AUTO-APPROVE] aparece no Railway Logs
[ ] Simular SETTLEMENT_FAILED → tela de erro aparece, QR permanece
[ ] Vendedor sem avatar → página /pay/[id] não quebra
```

**Critério de saída:** todos os quatro itens passando em produção.

---

### 🔴 S2 · Ativar AUTO_APPROVE em Produção
**Hoje está `false`. Só ativar após S1 confirmado.**

```
[ ] S1 concluído
[ ] Railway Variables → AUTO_APPROVE=true
[ ] Novo deploy de variável (sem código novo)
[ ] Confirmar que próximo cadastro real é aprovado automaticamente
```

**Risco se pular S1:** usuário real recebe magic link quebrado em prod.

---

## SPRINT 2 · ESTABILIZAÇÃO & UI/UX

### ✅ E1 · Mapear todos os estados de erro no frontend
**CONCLUÍDO (PR #18)**

### ✅ E2 · Rate Limiting no Cadastro
**CONCLUÍDO (PR #19)**

### ✅ E3 · TTL do Magic Link visível pro usuário
**CONCLUÍDO (PR #19)**

### ✅ E4 · Verificar Redis como fonte primária no magic-verify.js
**CONCLUÍDO (PR #19)**

### ✅ E5 · Mobile-First Bento Experience & Infrastructure Fixes
**CONCLUÍDO (PR #20)**
- Refatorada página `/para-quem` com layout **Bento Grid** responsivo e premium.
- Implementado adapter de **Light Theme** para o Navbar (Acqua Glassmorphism).
- Corrigida compatibilidade com **Web3Auth v9** (`privateKeyProvider`).
- Resolvidos warnings de ESM em arquivos de configuração (`jest`, `playwright`).
- Resolvidos warnings globais no `admin.js` (`window['viewTransaction']`).
- Otimizado script de build para evitar **OOM (Out of Memory)** na Railway.

---

## SPRINT 3 · ESCALA & MÉTRICAS

### ✅ A3 · Notificação por E-mail para o Vendedor
**CONCLUÍDO (PR #20)**
- Implementado disparo automático de e-mail para o vendedor ao concluir pagamento (`vendedor-notificacao.mjs`).
- Template dinâmico com valor, nome do produto e nome do comprador.

---

### 🔴 A2 · Dashboard de Métricas do Vendedor (Painel Premium)
**Próximo passo imediato para fechar o ciclo de autogestão.**

```
[ ] Implementar API de métricas agregadas (Total 24h/Mensal, Conversão)
[ ] Criar componente de visualização de dados (Charts simples ou Grid de Stats)
[ ] Permitir download de relatório financeiro em JSON/CSV
[ ] Integrar com o layout Bento Grid para manter consistência visual
```

---

### ✅ A1 · Migrar Polling → Server-Sent Events (SSE)
**CONCLUÍDO (PR #21)**
- Implementado endpoint `/api/charge/[id]/stream` com Redis Pub/Sub.
- Frontend agora usa `EventSource` com fallback automático para polling 3s.
- Reduzido overhead de rede e latência na confirmação de pagamento.

---

### 🟢 A4 · Auto-aprovação com KYC mínimo
**Hoje AUTO_APPROVE=true aprova qualquer email.**

```
[ ] Adicionar verificação de CPF real na API de registro
[ ] Integrar com serviço de validação de e-mail (evitar descartáveis)
```

---

## BACKLOG · SEM URGÊNCIA

### ⬜ B1 · Migração SQLite → Postgres
WAL + Railway NVMe aguenta o estágio atual.

---

### ⬜ B2 · SDK FlowPay para desenvolvedores
SDK npm que abstrai create-charge + polling.

---

### ⬜ B3 · Página de status pública
status.flowpay.cash mostrando uptime de Woovi, Redis, Railway.

---

## MAPA DE DEPENDÊNCIAS

```
S1 (smoke test) ──→ S2 (ativar auto-approve)
                      │
                      └→ A2 (Dashboard Métricas)
                           └→ A1 (SSE)
```

---

## RESUMO EXECUTIVO

```
HOJE             → ✅ Bento Grid + Infra Fixes + Seller Email
ESTA SEMANA      → 🔴 A2 (Dashboard de Métricas / Painel Vendedor)
PRÓXIMAS         → 🟡 A1 (SSE) + 🟢 A4 (KYC Básico)
TRIMESTRE        → 🟢 Expansão para Multichain (Base/Optimism)
```

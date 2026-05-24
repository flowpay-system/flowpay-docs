# Docs Audit 2026-03-03

## Escopo
Auditoria de `docs/` e subpastas para identificar conteúdo desatualizado, inseguro ou não utilizado no flow atual.

## Achados Críticos (ação imediata)

1. Credencial hardcoded exposta em guia público.
   - Arquivo: `docs/guides/ADMIN_PANEL_GUIDE.md`
   - Evidências: linhas com senha explícita (já removida).
   - Risco: incentiva uso de senha fraca/padrão e contradiz boas práticas de segurança.
   - Ação: remover todas as ocorrências e reescrever seção para uso exclusivo de segredo por ambiente.

2. Comandos inexistentes no Makefile atual.
   - Arquivos:
     - `docs/guides/ADMIN_PANEL_GUIDE.md`
     - `docs/technical/CSP_RESOLUTION_GUIDE.md`
     - `docs/visual/PWA_TEST_GUIDE.md`
     - `docs/archive/CSS_FIX.md` (arquivo de arquivo, baixo impacto)
   - Evidências: comandos antigos fora do Makefile (já corrigidos).
   - Risco: onboarding quebrado, execução inválida, perda de tempo operacional.
   - Ação: trocar por comandos válidos (`make dev`, `make build`, `make preview`, `make test*`) ou mover para arquivo histórico.

## Achados Altos (corrigir nesta semana)

3. Endpoints antigos de deploy/documentação operacional.
   - Arquivos:
     - `docs/guides/WOOVI_INTEGRATION_GUIDE.md` (usava endpoint legado)
     - `docs/IMPLEMENTATION_PLAN.md` (usava endpoint legado)
   - Risco: referência de ambiente morto/legado.
   - Ação: atualizar para domínio atual (`flowpay.cash`) e endpoints ativos.

4. Guia com data explicitamente antiga.
   - Arquivo: `docs/guides/TELEGRAM_SETUP_GUIDE.md`
   - Evidência: “Última atualização: Agosto 2024”.
   - Risco: reduz confiança no runbook.
   - Ação: revisar conteúdo e atualizar carimbo de versão.

## Achados Médios (higiene documental)

5. Documentos fora do índice principal (`docs/README.md`) e sem curadoria clara.
   - Amostra relevante:
     - `docs/ADMIN_METRICS.md`
     - `docs/CRYPTO_SERVICES.md`
     - `docs/FEATURES.md`
     - `docs/FRONTEND_INSTITUCIONAL.md`
     - `docs/SEO.md`
     - `docs/email/FLOW_EMAILS.md`
     - `docs/guides/BLOCKCHAIN_GUIDE.md`
     - `docs/guides/LIQUIDATION_GUIDE.md`
     - `docs/technical/WALKTHROUGH_POE.md`
   - Risco: conhecimento válido, mas invisível e difícil de manter.
   - Ação: ou indexar no README, ou mover para `docs/archive/` com justificativa.

6. Pasta `docs/archive/` contém histórico misturado com material ainda útil.
   - Risco: “arquivo” vira lixeira e sem política de retenção.
   - Ação: definir política simples:
     - `archive/` = somente histórico congelado.
     - ativo = precisa estar no índice principal.

## Decisão recomendada (rápida)

- Sprint curto de limpeza documental:
  1. Sanitizar segurança (`ADMIN_PANEL_GUIDE.md`) hoje.
  2. Corrigir comandos inválidos hoje.
  3. Atualizar URLs/ambientes esta semana.
  4. Classificar arquivos não indexados em `manter` ou `arquivar`.

## Inventário de inconsistências detectadas

- Links locais markdown quebrados:
  - nenhum encontrado na auditoria.

## Status pós-limpeza (2026-03-03)

- Item 1 resolvido: senha hardcoded removida do guia de admin.
- Item 2 resolvido: comandos `make` inválidos substituídos por comandos atuais.
- Item 3 resolvido: URLs legadas trocadas por domínios/hosts atuais.
- Item 5 resolvido: docs ativos reindexados no `docs/README.md`.
- Item 6 parcialmente resolvido: parte do conteúdo histórico foi movida para `docs/archive/`.

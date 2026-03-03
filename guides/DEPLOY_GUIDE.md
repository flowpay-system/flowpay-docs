# 🚀 Guia de Deploy - Railway (DEPRECATED)

> [!CAUTION]
> **ESTE GUIA ESTÁ DEPRECADO.** 
> A FlowPay migrou oficialmente para a infraestrutura **Cloudflare (Workers + D1 + Pages)**. 
> Este documento permanece apenas para registro histórico. 


Este guia cobre o processo de deploy da aplicação **FlowPay** na plataforma **Railway**.

## 📋 Visão Geral

- **Plataforma:** Railway (railway.app)
- **Framework:** Astro (SSR - Server Side Rendering)
- **Runtime:** Node.js v20+
- **Build Command:** `pnpm run build`
- **Start Command:** `pnpm run start`

## ⚙️ Configuração (railway.json)

O projeto utiliza um arquivo `railway.json` na raiz para controlar o comportamento do deploy.

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "pnpm run build"
  },
  "deploy": {
    "startCommand": "pnpm run start",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 5
  }
}
```

## 🔑 Variáveis de Ambiente (Obrigatórias)

As seguintes variáveis devem ser configuradas no painel do Railway (**Variables**):

| Variável | Descrição |
| :--- | :--- |
| `WOOVI_API_KEY` | Chave de API da Woovi (AppID) |
| `WOOVI_WEBHOOK_SECRET` | Segredo para validação de Webhooks |
| `WOOVI_API_URL` | URL da API (ex: `https://api.woovi.com`) |
| `HOST` | Deve ser `0.0.0.0` para aceitar conexões externas |
| `PORT` | O Railway define isso automaticamente (ex: `PORT`), mas o Astro deve escutar nesta porta |

> **Nota:** Consulte `docs/WOOVI_INTEGRATION_GUIDE.md` para detalhes dos segredos da Woovi.

## 🚀 Como Fazer Deploy

### Automático (Recomendado)

O Railway está conectado ao GitHub. Qualquer push na branch `main` dispara um deploy automaticamente.

1. Faça suas alterações.
2. `git push origin main`
3. Acompanhe o build no painel do Railway.

### Manual (CLI)

Se precisar testar sem commitar ou forçar um deploy:

```bash
# Login
railway login

# Deploy
railway up
```

## 🔍 Troubleshooting

**Erro 401 na API Woovi**

- Verifique se `WOOVI_API_KEY` no Railway é a string codificada (inicia com `Q2xp...`) e não o AppID legível.

**Erro de Build**

- Verifique os logs na aba "Build Logs".
- Geralmente falta de dependências ou erro de sintaxe.

**Aplicação não inicia (Crash Loop)**

- Verifique se `HOST=0.0.0.0` está definido. O Astro precisa disso para rodar em containers.

---

**Status Atual (30/01/2026):** ✅ Deploy funcional e estável.

# 🧩 Integração Woovi/Pix - Guia Técnico e Operacional

Este documento serve como a **Fonte Única da Verdade (SSOT)**
para a implementação, manutenção e segurança da integração
Pix no FlowPay.

---

## 1. Credenciais e Variáveis de Ambiente

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️  ATENÇÃO: Credenciais NUNCA devem ser commitadas
    Use variáveis de ambiente (.env ou Railway)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

| Variável | Descrição | Formato | Exemplo |
|:---------|:----------|:--------|:--------|
| `WOOVI_API_KEY` | Chave AppID | String codificada | `Q2xp...` |
| `WOOVI_WEBHOOK_SECRET` | Senha webhook | String | `f...` |
| `WOOVI_API_URL` | Endpoint base | URL HTTPS | `https://api.woovi.com` |

---

## 2. Autenticação (Cliente API)

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
▸ REGRA DE OURO - AUTENTICAÇÃO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

A API Woovi espera a chave SEM prefixo:

❌ ERRADO:  Authorization: Bearer Q2xp...
✅ CORRETO: Authorization: Q2xp...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Implementação (`src/pages/api/create-charge.js`)

```javascript
const wooviResponse = await fetch(
  `${WOOVI_API_URL}/api/v1/charge`,
  {
    method: 'POST',
    headers: {
      'Authorization': cleanApiKey, // SEM 'Bearer'
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(pixData)
  }
);
```

---

## 3. Segurança do Webhook

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛡️  BLINDAGEM TRIPLA DE SEGURANÇA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[A] Validação de IP (Whitelist)
    └─ IPs: 179.190.27.5, 179.190.27.6,
            186.224.205.214
    └─ Local: src/services/api/config.mjs

[B] Assinatura HMAC (Integridade)
    └─ Hash com WOOVI_WEBHOOK_SECRET

[C] Header Authorization
    └─ Deve bater com WOOVI_WEBHOOK_SECRET

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 4. Configuração no Painel Woovi

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚙️  SETUP DO WEBHOOK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. URL:
   https://flowpay.cash/api/webhook

2. Evento:
   Cobrança paga - OPENPIX:CHARGE_COMPLETED

3. Authentication Header:
   • Key:   Authorization
   • Value: [WOOVI_WEBHOOK_SECRET]
             (ex: valor-super-secreto-aqui)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 5. Logs e Debug

```text
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 BOAS PRÁTICAS DE LOGGING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️  JAMAIS logue apiKey ou webhookSecret completos

✅ Use secureLog para redação automática:

   secureLog('info', 'Cobrança Criada',
             { id: '123', value: 10.00 });

   // Resultado: apiKey: '[REDACTED]'

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 6. Comandos Úteis

**Testar Conexão (cURL):**

```bash
curl -I -H "Authorization: $WOOVI_API_KEY" \
  "https://api.woovi.com/api/v1/charge?limit=1"
# Esperado: HTTP 200 OK
```

**Testar Criação de Cobrança:**

```bash
curl -X POST \
  https://flowpay.cash/api/create-charge \
  -H "Content-Type: application/json" \
  -d '{
    "wallet": "0xTESTE",
    "valor": 1.00,
    "moeda": "BRL",
    "id_transacao": "test-'$(date +%s)'",
    "product_id": "test-doc"
  }'
```

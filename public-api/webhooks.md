# Webhooks and Security

Os Webhooks são a forma mais eficiente do FlowPay notificar sua aplicação sobre mudanças de estado (ex: pagamento recebido) em tempo real.

## Configuração do Endpoint

Você deve fornecer uma URL pública (Ex: `https://seuapp.com/api/flowpay-webhook`) que aceite requisições `POST` com payloads JSON.

## Verificação de Segurança

Para garantir que a notificação veio realmente do FlowPay, você deve validar a assinatura HMAC enviada nos headers.

### Header de Assinatura
`X-FlowPay-Signature: [HMAC_SHA256_HEX]`

### Como Validar (Node.js)

```javascript
const crypto = require('crypto');

function verifyWebhook(secret, payload, signature) {
    const hmac = crypto.createHmac('sha256', secret);
    const digest = hmac.update(payload).digest('hex');
    return crypto.timingSafeEqual(Buffer.from(digest), Buffer.from(signature));
}
```

> [!TIP]
> Use `crypto.timingSafeEqual` para evitar ataques de timing.

## Estrutura do Payload

```json
{
  "event": "charge.completed",
  "id_transacao": "INV-2026-001",
  "status": "COMPLETED",
  "amount": 50.00,
  "wallet": "0x123...",
  "timestamp": "2026-03-14T10:05:00Z"
}
```

## Política de Retry

Se o seu servidor retornar qualquer status diferente de `2xx`, o FlowPay tentará reenviar a notificação seguindo um esquema de backoff exponencial:
1. Imediatamente
2. Após 2 minutos
3. Após 10 minutos
4. Após 1 hora
5. Após 24 horas

Após 5 falhas, a notificação é marcada como `FAILED` e exigirá reconciliação via API de consulta.

## Idempotência no Webhook

Devido à política de retry, seu endpoint de webhook **precisa ser idempotente**. Se você receber a mesma notificação de `COMPLETED` duas vezes, sua aplicação deve processar apenas a primeira e retornar `200 OK` na segunda sem executar a lógica de negócio novamente.

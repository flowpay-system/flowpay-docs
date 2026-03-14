# Create Charge

Endpoint central para geração de cobranças PIX dinâmicas com vínculo opcional a uma carteira criptográfica para liquidação futura.

## Endpoint
`POST /api/create-charge`

## Requisição (Body JSON)

| Campo | Tipo | Obrigatório | Descrição |
|:---|:---|:---:|:---|
| `valor` | Number | Sim | Valor da cobrança em BRL (Ex: `10.50`). |
| `id_transacao` | String | Não | ID único da sua aplicação (chave de idempotência). Se não enviado, o FlowPay gerará um. |
| `wallet` | String | Não | Endereço da carteira destino (ETH/BSC/Polygon) se aplicável. |
| `moeda` | String | Não | Default: `BRL`. Moeda base da cobrança. |
| `product_id` | String | Não | Referência do seu produto ou serviço. |
| `customer_name` | String | Não | Nome do cliente final. |
| `customer_email` | String | Não | E-mail do cliente (usado para notificações e logs). |

### Exemplo de Payload

```json
{
  "valor": 50.00,
  "id_transacao": "INV-2026-001",
  "wallet": "0x1234567890abcdef1234567890abcdef12345678",
  "product_id": "premium_subscription",
  "customer_name": "João Silva",
  "customer_email": "joao@exemplo.com"
}
```

## Resposta (Success 200)

```json
{
  "success": true,
  "pix_data": {
    "qr_code": "data:image/png;base64,...",
    "br_code": "00020126580014br.gov.bcb.pix...",
    "correlation_id": "INV-2026-001",
    "value": 50,
    "expires_at": "2026-03-14T10:00:00Z",
    "status": "CREATED"
  },
  "wallet": "0x123...",
  "moeda": "BRL",
  "id_transacao": "INV-2026-001"
}
```

## Tratamento de Erros

| Código | Tipo | Causa Provável |
|:---|:---|:---|
| `400` | `VALIDATION_ERROR` | Valor inválido (menor ou igual a zero) ou e-mail mal formatado. |
| `401` | `UNAUTHORIZED` | API Key ausente ou inválida. |
| `409` | `CONFLICT` | Já existe uma transação com o `id_transacao` informado (Idempotência). |
| `502` | `EXTERNAL_API_ERROR` | Falha temporária na comunicação com o provedor (Woovi). Recomendado retry exponencial. |
| `503` | `CONFIG_ERROR` | Problema de configuração no ambiente do FlowPay. |

## Idempotência

O campo `id_transacao` atua como sua chave de idempotência. Se você enviar uma requisição com o mesmo `id_transacao` que já foi processado com sucesso, o FlowPay retornará `409 Conflict`. Isso evita cobranças duplicadas em caso de retries na sua camada de rede.

## Glossário de Campos

Para garantir a estabilidade das integrações, definimos a semântica de cada campo principal:

- **`id_transacao`**: Sua chave primária externa. Deve ser única por venda. O FlowPay usa este campo para evitar duplicidade.
- **`correlation_id`**: O identificador retornado pelo gateway (Woovi/Pix). Use para reconciliação técnica em logs.
- **`wallet`**: Endereço público da carteira onde o ativo digital será provisionado (Ex: carteira de checkout do merchant).
- **`product_id`**: String livre para você categorizar o tipo de serviço (Subscription, Fee, Product).
- **`status`**: Estado atual da cobrança. Ver [Status Lifecycle](./status-lifecycle.md).
- **`customer_email`**: Identificador canônico do cliente para suporte e histórico de pagamentos.

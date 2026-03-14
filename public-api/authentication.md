# Authentication

O FlowPay utiliza diferentes métodos de autenticação dependendo do contexto da requisição. Para integradores (server-to-server), o método principal é a **API Key**.

## Server-to-Server (API Key)

Para realizar operações programáticas, como criar cobranças a partir do seu backend, você deve enviar o header `X-API-Key`.

### Header Obrigatório
`X-API-Key: [SUA_CHAVE_DE_SERVIÇO]`

> [!IMPORTANT]
> A chave de serviço (`INTERNAL_API_KEY`) é secreta e fornece privilégios administrativos para criar ordens. Nunca a exponha no frontend (cliente).

### Exemplo de Request (cURL)

```bash
curl -X POST https://api.flowpay.cash/api/create-charge \
  -H "X-API-Key: fp_live_123456789" \
  -H "Content-Type: application/json" \
  -d '{ ... }'
```

---

## Sessão de Usuário (Dashboard)

Quando você utiliza o `app.flowpay.cash` ou o dashboard administrativo, a autenticação ocorre via Cookies seguros (`HttpOnly`, `Secure`, `SameSite=Lax`).

- **User Session**: Armazenada no cookie `flowpay_session`.
- **Admin Session**: Armazenada no cookie `flowpay_admin_session`.

Esta camada é gerenciada automaticamente pelo FlowPay e não deve ser utilizada para integrações externas.

---

## Segurança e Melhores Práticas

1. **Rotação de Chaves**: Caso sua chave seja comprometida, entre em contato com o suporte imediatamente para rotação.
2. **TLS Obrigatório**: Todas as chamadas devem ser feitas via HTTPS. Chamadas em HTTP serão rejeitadas.
3. **Whitelist de IP**: Embora as requisições sejam autenticadas por chave, recomenda-se que sua aplicação servidor mantenha um log de IPs de origem para auditoria interna.

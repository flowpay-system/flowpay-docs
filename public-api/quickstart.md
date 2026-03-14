# Integration Quickstart

Este guia apresenta receitas rápidas para os casos de uso mais comuns na integração com o FlowPay.

## 1. Venda Única (One-time Payment)
Ideal para checkout de produtos físicos ou digitais simples.

1. Chame `POST /api/create-charge` com os dados da compra.
2. Armazene o `id_transacao` no seu banco de dados vinculado ao pedido.
3. Redirecione o usuário para a página de pagamento ou exiba o `qr_code`.
4. No Webhook, quando receber `charge.completed`, marque o pedido como "Pago" e inicie o envio.

## 2. Assinatura SaaS (Subscription Setup)
Para habilitar acessos recorrentes após a primeira confirmação.

1. Crie a cobrança referente ao primeiro mês.
2. No campo `product_id`, envie o ID do plano (ex: `plan_pro_monthly`).
3. Ao receber o Webhook, registre a data de início da assinatura e libere o acesso ao Dashboard do seu SaaS.
4. Use o `customer_email` para vincular a conta do FlowPay ao usuário da sua plataforma.

## 3. Ativação de Produto Cripto (Web3)
Para dApps ou serviços que dependem de endereço on-chain.

1. Envie o endereço da carteira do usuário no campo `wallet`.
2. O FlowPay processará o PIX e, internamente, orquestrará a liquidação via Nexus.
3. Sua aplicação ouve o webhook `charge.completed` e verifica se o ativo foi enviado para a `wallet` informada usando um provider blockchain (ex: Viem, Ethers).

---

## Dicas de Ouro para Desenvolvedores

### 1. Logs de Idempotência
Sempre use o seu `id_externo` como `id_transacao`. Se o cliente clicar duas vezes no botão de "Pagar", o FlowPay garantirá que apenas uma cobrança exista.

### 2. Ambiente de Teste
Para testar sua integração sem gastar dinheiro real:
- Use valores baixos (ex: R$ 0,01) em produção, ou:
- Solicite acesso ao ambiente de **Sandbox** (se disponível para seu tier).

### 3. Timeouts de UI
O PIX geralmente é instantâneo. Recomendamos um sistema de `Polling` leve ou `WebSockets` no seu frontend para detectar a mudança de status e dar feedback visual imediato ao usuário assim que o webhook for processado pelo seu backend.

---

## Próximos Passos
- [Explorar a API de Consulta](./create-charge.md)
- [Entender a Tabela de Status](./status-lifecycle.md)
- [Garantir a Segurança do Webhook](./webhooks.md)

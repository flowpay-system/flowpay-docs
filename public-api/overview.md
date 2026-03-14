# Overview

O **FlowPay** é uma infraestrutura de pagamentos de alto desempenho que atua como ponte inteligente entre o sistema financeiro tradicional (PIX) e ativos digitais (Crypto/USDT). 

Projetado para ser uma solução "low-friction", o FlowPay permite que integradores aceitem pagamentos PIX e liquidem automaticamente em criptoativos para suas carteiras, ou vice-versa, mantendo conformidade e segurança operacional.

## O que o FlowPay resolve?

- **Conversão PIX-to-Crypto**: Receba em BRL via PIX e tenha os ativos digitais provisionados na carteira do cliente ou do merchant.
- **Abstração de Gateway**: Interface unificada para múltiplos provedores (Woovi, Nexus) sem precisar gerenciar as complexidades individuais de cada API.
- **Segurança Operacional**: Validação rigorosa de webhooks, proteção contra ataques de timing e precisão aritmética de centavos para evitar erros de arredondamento.

## Taxas e Tarifas

O FlowPay opera com um modelo de precificação transparente e competitivo:

- **Taxa Padrão**: **2,5%** por transação processada.
- **Variáveis Locais**: A taxa pode sofrer variações pontuais dependendo de mudanças regulatórias ou flutuações nas infraestruturas de liquidação no Brasil.
- **Liquidação Cripto**: Custos de rede (gas fees) podem ser aplicados dependendo da blockchain escolhida para liquidação.

## Para quem serve?

- **Plataformas SaaS**: Cobrança de assinaturas ou taxas únicas.
- **E-commerces & Infoprodutos**: Checkout rápido com baixa taxa de abandono.
- **Exchanges & Wallets**: On-ramp simplificado para usuários brasileiros.
- **Aplicações Web3**: Provisionamento de serviços on-chain após confirmação off-chain.

## Topologia do Serviço

O ecossistema FlowPay é dividido em três superfícies principais:

| Superfície | URL | Descrição |
|:---|:---|:---|
| **Marketing** | `flowpay.cash` | Landing page institucional e documentação de alto nível. |
| **Dashboard** | `app.flowpay.cash` | Interface de gerenciamento, configuração de botões e métricas. |
| **API Edge** | `api.flowpay.cash` | Camada transacional de alto desempenho rodando no Cloudflare Edge. |

## Fluxo Básico de Integração

1. **Criação**: Sua aplicação solicita uma cobrança à API do FlowPay.
2. **Pagamento**: O cliente final realiza o pagamento do PIX gerado.
3. **Confirmação**: O FlowPay recebe o webhook do provedor e valida a segurança.
4. **Liquidação**: O FlowPay emite um evento (Nexus/Webhook) para que sua infraestrutura libere o serviço ou provisione o ativo.

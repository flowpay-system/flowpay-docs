# Admin Help Center Internal

Documento interno para operação do painel administrativo do FlowPay.

## Escopo

Este material consolida o conteúdo que não deve aparecer na central pública do cliente final.

## Acesso ao painel administrativo

O administrador governa acesso, risco operacional e estado das contas no FlowPay.

Responsabilidades principais:
- validar ou negar acesso de novos usuários
- manter o status das contas coerente com a operação
- monitorar indicadores do painel administrativo

### Entrar no admin

Existem dois caminhos:
- abrir `https://app.flowpay.cash/login?area=admin`
- tentar acessar `/admin` e deixar o app redirecionar para o login admin

### Fazer login como administrador

1. Acesse a tela de admin.
2. Digite a senha de administrador.
3. Clique em `Entrar como Admin`.

Se a autenticação for aceita, a sessão admin é criada e o sistema envia você para `/admin`.

### Mensagens do sistema

Mensagens de atenção:
- `Falha de conexão. Tente novamente.`
- mensagens retornadas pela API de autenticação admin

### Validações

- o painel `/admin` exige sessão autenticada
- se a sessão não existir, o app redireciona para o login admin

## Operação do painel administrativo

### Ler as métricas do topo

No topo do painel, o admin acompanha:

| Métrica | Descrição |
| --- | --- |
| `Pendentes` | contas aguardando aprovação |
| `Aprovados` | contas ativas |
| `Pedidos` | total de solicitações recebidas |
| `Volume total` | valor agregado movimentado |

### Filtrar usuários por status

As abas disponíveis são:
- `Pendentes`
- `Aprovados`
- `Rejeitados`
- `Todos`

### Ler a tabela de usuários

A tabela mostra:
- nome
- e-mail
- tipo documental
- segmento
- data de cadastro
- status
- ações disponíveis

### Atualizar status de uma conta

Dependendo do status atual, o admin pode:

| Ação | Quando usar |
| --- | --- |
| `Aprovar` | conta pendente validada |
| `Rejeitar` | conta pendente negada |
| `Suspender` | conta aprovada com problema operacional |
| `Reativar` | conta suspensa ou rejeitada reabilitada |

### Navegar na paginação

O painel administrativo usa paginação para navegar entre lotes de usuários.

### Mensagens do sistema

Mensagens esperadas:
- `Usuário aprovado.`
- `Usuário rejeitado.`
- `Status atualizado.`

Mensagens de atenção:
- `Erro ao carregar usuários.`
- `Erro ao atualizar status.`

### Validações

- usuários pendentes podem ser aprovados ou rejeitados
- usuários aprovados podem ser suspensos
- usuários suspensos ou rejeitados podem ser reativados

### FAQ operacional

#### O que fazer se a lista não carregar?

Tente novamente e valide se a sessão admin continua ativa.

#### O admin consegue aprovar e suspender usuários pelo mesmo painel?

Sim. As ações mudam de acordo com o status atual de cada conta.

#### O que fazer se um usuário relata não conseguir acessar o app?

Primeiro valide o status da conta no painel admin. Se necessário, ajuste para `APPROVED` e oriente o usuário a solicitar novo link mágico para criar uma sessão limpa.

## Encerrar sessão

### Como encerrar

1. Clique em `Sair` no painel administrativo.
2. A sessão é encerrada e o sistema redireciona para `https://flowpay.cash`.

O logout do administrador é independente do logout do vendedor. Encerrar a sessão admin não afeta sessões de vendedores ativas.

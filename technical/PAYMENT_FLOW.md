# 💳 Arquitetura de Fluxo - FLOWPay

Este documento fornece uma visão técnica aprofundada dos motores que alimentam o FLOWPay, incluindo o ciclo de vida do pagamento, a camada de prova (PoE) e as integrações de ponte.

## 1. Ciclo de Vida do Pagamento (End-to-End)

O diagrama abaixo detalha como o FLOWPay orquestra o pagamento, as notificações externas e o desbloqueio de acesso.

```mermaid
sequenceDiagram
    autonumber
    actor User as Usuário
    participant UI as Store/Landing UI (Astro)
    participant API as FlowPay API
    participant DB as SQLite (Autonomous)
    participant Woovi as Gateway PIX
    participant PoE as PoE Service
    participant Nexus as NΞØ Nexus (Tunnel)
    participant Bot as NeoBot Core

    User->>UI: Escolhe plano & Preenche dados
    UI->>API: POST /api/create-charge<br>(ou /api/create-charge-landing)
    API->>DB: INSERT order (Status: CREATED)
    API-->>UI: PIX QR Code & Metadata
    UI->>User: Exibe QR Code

    Note over User,Woovi: Usuário realiza o pagamento
    
    Woovi->>API: Webhook (charge.paid)
    API->>DB: Check Idempotência
    API->>DB: UPDATE order (Status: PIX_PAID)
    
    par Camada de Prova & Notificação
        API->>PoE: addOrderToBatch(id)
        PoE->>DB: Link order to Batch
        API->>Nexus: notifyNexus(PAYMENT_RECEIVED)
    end

    API->>Bot: triggerUnlock(id) [JWT Auth]
    
    loop Retry Policy (3x)
        Bot->>Bot: Process Signature/Minting
        alt Success
            Bot-->>API: 200 OK + Receipt
            API->>DB: UPDATE order (Status: COMPLETED)
        else Failure
            Bot->>Bot: Exponential Backoff
        end
    end

    loop Polling Status
        UI->>API: GET /api/charge/[id]
        API->>DB: SELECT status
        API-->>UI: status: COMPLETED
    end

    UI->>User: Exibe Sucesso (Passo 4 - Green)
```

## 2. Camada de Prova (Proof-of-Execution - PoE)

O FLOWPay não apenas processa o pagamento, mas cria uma trilha de auditoria Merkle que é ancorada na Base L2.

```mermaid
graph TD
    subgraph "Batching (Local SQLite)"
        O1[Order A] --> B1[Batch #102]
        O2[Order B] --> B1
        O3[Order C] --> B1
    end

    subgraph "Proof Generation"
        B1 --> |Merkle Hash| M((Merkle Root))
        M --> |SHA256| CP[Checkpoint Hash]
    end

    subgraph "Anchoring (Base L2)"
        CP --> |WriteProof| BL2((Base Blockchain))
        BL2 --> |TX Hash| AN[Anchor Record]
    end

    AN --> |Final Update| B1
```

## 3. Arquitetura Relayer Proxy

O FLOWPay atua como um coordenador de intenções, mantendo a segurança pela segregação de chaves.

```mermaid
C4Context
    title Arquitetura Relayer Proxy FLOWPay

    Person(user, "Usuário", "Realiza o pagamento PIX")
    
    System_Boundary(c1, "FlowPay Autonomous Node") {
        System(web, "Next/Astro UI", "Checkout e Status")
        SystemDb(db, "SQLite", "Persistência local de ordens e PoE")
        System(api, "Node API", "Orquestrador de Webhooks e Pontos")
    }

    System_Ext(woovi, "Woovi API", "Gateway PIX")
    System_Ext(nexus, "NΞØ Nexus", "Monitor de Ecossistema (Tunnel)")
    
    System_Boundary(c2, "Smart Factory Core") {
        System_Ext(neobot, "NeoBot Core", "Executor de Skills e Gestor de Chaves")
        SystemDb(blockchain, "Base L2", "Soberania on-chain")
    }

    Rel(user, web, "Preenche dados", "HTTPS")
    Rel(web, api, "Cria ordem", "REST")
    Rel(api, db, "Salva estado", "SQL")
    Rel(woovi, api, "Notifica pagamento", "Webhook/HMAC")
    Rel(api, nexus, "Sincroniza evento", "HMAC/Tunnel")
    Rel(api, neobot, "Dispara Unlock", "JWT Auth")
    Rel(neobot, blockchain, "Minta Ativos / PoE Anchor", "Web3")
```

## 4. Fluxo de Autenticação Soberana (Magic Link)

O FLOWPay utiliza um sistema de login sem senha para garantir que apenas o dono do e-mail/wallet tenha acesso ao dashboard.

```mermaid
sequenceDiagram
    actor Admin as Administrador
    participant UI as Login UI
    participant API as Auth API
    participant DB as SQLite
    participant Email as SMTP / Mock Console

    Admin->>UI: Insere E-mail
    UI->>API: POST /api/auth/magic-start
    API->>API: Gera Token Seguro (SHA256)
    API->>DB: Salva Token + Expiração
    API->>Email: Envia link com Token
    Email-->>Admin: Recebe Magic Link
    Admin->>UI: Clica no Link (/auth/verify?token=...)
    UI->>API: POST /api/auth/magic-verify (Token no Body)
    API->>DB: SELECT token
    API->>API: Valida expiração & Consome token
    API-->>UI: Set Cookie (HttpOnly) & Redirect Dashboard
```

## 5. Glossário de Tecnologias

*   **PoE (Proof-of-Execution)**: Motor que agrupa ordens em lotes Merkle para provar que a execução ocorreu sem expor dados sensíveis.
*   **Nexus Bridge**: Canal de comunicação seguro que integra o FlowPay ao ecossistema NEØ para monitoramento global.
*   **Merkle Batching**: Técnica criptográfica para reduzir custos de gás e aumentar a auditabilidade das transações locais.
*   **Base L2**: Blockchain de baixa latência utilizada para ancorar as provas de execução.

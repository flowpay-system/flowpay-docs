# NEO NEXUS - TECHNICAL ARCHITECTURE
> **Version:** 1.0.0
> **Status:** LIVE & OPERATIONAL (Mar/2026)
> **Deployed:** https://nexus.neoprotocol.space
> **Repository:** NEO-PROTOCOL/neo-nexus (Railway)
> **Architect:** NEO MELLO / Antigravity AI

---

## 1. SYSTEM OVERVIEW

The **NEO Nexus** is the central orchestration engine of the NEØ Protocol. It acts as a **Autonomous Event Bus** that coordinates communication between autonomous nodes (FlowPay, Smart Factory, Fluxx DAO, WOD Pro, etc.).

**Core Principle:** Decoupled, event-driven architecture where nodes publish events and subscribe to reactions without knowing each other's implementation details.

---

## 2. ARCHITECTURE DIAGRAM

```
┌─────────────────────────────────────────────────────────────────┐
│                         NEO NEXUS SERVICE                       │
│                     (nexus.neoprotocol.space)                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────────┐      ┌──────────────────┐                 │
│  │  HTTP/REST API   │      │  WebSocket API   │                 │
│  │  (Webhooks In)   │      │  (Real-time Out) │                 │
│  └────────┬─────────┘      └────────┬─────────┘                 │
│           │                         │                           │
│           ▼                         ▼                           │
│  ┌─────────────────────────────────────────────┐                │
│  │         EVENT BUS (index.ts)                │                │
│  │  - ProtocolEvent enum                       │                │
│  │  - Nexus.dispatch()                         │                │
│  │  - Nexus.onEvent()                          │                │
│  └─────────────────────────────────────────────┘                │
│           │                         │                           │
│           ▼                         ▼                           │
│  ┌──────────────────┐      ┌──────────────────┐                 │
│  │   REACTORS       │      │   SUBSCRIBERS    │                 │
│  │  (Logic Layer)   │      │  (External Nodes)│                 │
│  └──────────────────┘      └──────────────────┘                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
         │                                  │
         ▼                                  ▼
┌─────────────────┐              ┌─────────────────┐
│   FlowPay Node  │              │ Smart Factory   │
│ (Webhook Client)│              │ (Webhook Client)│
└─────────────────┘              └─────────────────┘
```

---

## 3. COMPONENTS BREAKDOWN

### 3.1 HTTP/REST API (Ingress)
**Purpose:** Receive events from external nodes via webhooks.

**Endpoints:**
- `POST /events` - Generic event ingress (authenticated via HMAC signature)
- `POST /webhook/flowpay` - FlowPay-specific webhook
- `POST /webhook/factory` - Smart Factory callback
- `GET /health` - Health check for Railway/monitoring

**Technology:** Express.js (Node.js)

**Security:**
- HMAC signature validation (shared secret per node)
- Rate limiting (10 req/sec per IP)
- CORS restricted to known domains

---

### 3.2 Event Bus (Core Logic)
**File:** `index.ts` (already exists)

**Responsibilities:**
- Define `ProtocolEvent` enum (PAYMENT_RECEIVED, MINT_REQUESTED, etc.)
- Implement `Nexus.dispatch(event, payload)` to emit events
- Implement `Nexus.onEvent(event, handler)` to register reactors

**Current Status:** Fully Implemented & Production-Ready

**Production Features:**
- Persistent event log (SQLite)
- Retry mechanism for failed reactions (`/src/utils/retry-queue.ts`)
- Event replay via graph discovery (`/src/core/graph.ts`)
- Secret rotation with grace period (`NEXUS_SECRET_NEW`/`NEXUS_SECRET_OLD`)
- Metrics & observability (Winston, Discord alerts)

---

### 3.3 Reactors (Business Logic)
**Purpose:** Define "If This Then That" logic.

**Example Reactor:**
```typescript
Nexus.onEvent(ProtocolEvent.PAYMENT_RECEIVED, async (payload) => {
  // Call Smart Factory API to mint tokens
  await fetch('https://smart.neoprotocol.space/api/mint', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${FACTORY_API_KEY}` },
    body: JSON.stringify({
      address: payload.payerId,
      amount: payload.amount
    })
  });
});
```

**Location:** `/NEO-PROTOCOL/neo-nexus/src/reactors/`

**Live Reactors:**
- `payment-to-mint.ts`: Emits mint event on payment received
- `mint-to-notify.ts`: Sends WhatsApp via Neobot on mint confirmed

---

### 3.4 WebSocket API (Egress)
**Purpose:** Real-time event streaming to subscribers.

**Use Case:** Dashboard or monitoring tools can subscribe to events live.

**Technology:** `ws` library (Node.js WebSocket)

**Protocol:**
```json
// Client subscribes
{ "action": "subscribe", "events": ["PAYMENT_RECEIVED", "MINT_CONFIRMED"] }

// Server broadcasts
{ "event": "PAYMENT_RECEIVED", "payload": { ... }, "timestamp": 1234567890 }
```

---

### 3.5 Subscribers (External Nodes)
**Definition:** Any service that needs to react to Nexus events.

**Integration Methods:**
1. **Webhook (Push):** Nexus calls the node's endpoint when event occurs.
2. **WebSocket (Pull):** Node connects to Nexus and listens for events.
3. **SDK (Library):** Node imports `@neo-protocol/nexus-sdk` and subscribes programmatically.

---

## 4. DATA FLOW EXAMPLES

### Example 1: Payment → Mint Flow
```
1. FlowPay receives PIX payment
2. FlowPay calls: POST /webhook/flowpay
   Body: { transactionId, amount, payerId }
3. Nexus validates signature
4. Nexus dispatches: ProtocolEvent.PAYMENT_RECEIVED
5. Reactor listens and calls Smart Factory API
6. Smart Factory mints tokens on-chain
7. Smart Factory calls: POST /webhook/factory
   Body: { contractAddress, txHash }
8. Nexus dispatches: ProtocolEvent.MINT_CONFIRMED
9. Reactor sends WhatsApp notification via Neobot
```

---

## 5. DEPLOYMENT ARCHITECTURE

### Option A: Railway (Recommended for MVP)
- Single Docker container
- Auto-deploy on `git push`
- Environment variables for secrets
- Built-in health checks

### Option B: VPS (Future)
- Docker Compose with Redis
- Nginx reverse proxy
- PM2 for process management
- Dedicated IP for whitelisting

---

## 6. SECURITY CONSIDERATIONS

### 6.1 Authentication
- Each node has a unique `NEXUS_SECRET_KEY`
- Webhooks include HMAC signature in header: `X-Nexus-Signature`
- Nexus validates: `HMAC-SHA256(body, secret) === signature`

### 6.2 Authorization
- Event-level permissions (e.g., only FlowPay can dispatch PAYMENT_RECEIVED)
- Stored in config: `config/permissions.json`

### 6.3 Audit Trail
- All events logged to database with timestamp, source, and payload
- Immutable log for compliance and debugging

---

## 7. MONITORING & OBSERVABILITY

### Metrics to Track:
- Events processed per minute
- Reactor execution time
- Failed reactions (retry queue size)
- WebSocket connections count

### Tools:
- **Logs:** Winston (JSON format)
- **Metrics:** Prometheus + Grafana (future)
- **Alerts:** Discord webhook on critical failures

---

## 8. SCALABILITY ROADMAP

### Phase 1 (COMPLETE): Standalone Service
- Separate Railway deployment (live Mar/2026)
- Express HTTP + WebSocket + SQLite persistence
- Good for <10k events/day

### Phase 2 (CURRENT): High-Volume Optimization
- Event filtering & subscriptions
- Rate limiting & circuit breakers
- Good for 10k-100k events/day

### Phase 3 (ROADMAP): Distributed Nexus
- Redis Pub/Sub for event distribution
- Multiple Nexus instances (load balanced)
- Event sharding by domain
- Good for >100k events/day

---

**Document Status:** Architecture Live & Operational
**Deployment:** Railway (`nexus.neoprotocol.space`)
**For implementation details:** See `/NEO-PROTOCOL/neo-nexus`

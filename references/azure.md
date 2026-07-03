# Azure Component Catalog — Purpose & Fit

Load when the user's cloud is Azure. This is a **what-it-is-for** catalog to map C2/C3
elements to services and to reason about fit in tradeoff questions. **No pricing here
by design** — if the user asks about cost, research current prices at that moment.

## Compute — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **Container Apps (ACA)** | Serverless containers with KEDA autoscale, scale-to-zero, Dapr optional | APIs/workers as container images, spiky or modest traffic, small team, no cluster ops desired | You need K8s-level control (CRDs, operators, daemonsets) |
| **App Service** | Classic PaaS for web apps/APIs, first-class .NET, deploy slots | Plain code deploys, blue/green via slots, team fluent in the App Service model | Scale-to-zero needed; many heterogeneous services |
| **Functions** | Event-driven, short-lived compute; rich trigger/binding ecosystem (queue, blob, timer, HTTP, Service Bus) | Glue logic, scheduled jobs, queue consumers, bursty event handlers | Long-running or latency-critical user-facing paths (cold starts); complex stateful flows (unless Durable Functions fits) |
| **Durable Functions** | Code-first orchestrations/sagas on top of Functions | Multi-step workflows with state, fan-out/fan-in, human-approval waits | Simple fire-and-forget jobs (plain Functions suffice) |
| **AKS** | Full managed Kubernetes | Many services, platform team exists, need for K8s ecosystem | <10 services or <3 engineers — YAGNI probe REQUIRED |
| **VMs** | IaaS, full OS control | Lift-and-shift, exotic runtimes, licensed software | Anything a PaaS option covers |

## Data — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **Azure Database for PostgreSQL Flexible** | Managed Postgres; the sane relational default | Transactional domain, open ecosystem, team knows SQL | Team is deep SQL Server (see below) |
| **Azure SQL Database** | Managed SQL Server; serverless tier can auto-pause | .NET/EF/SQL Server-fluent teams; existing T-SQL assets | Avoiding Microsoft-specific SQL dialect matters |
| **Cosmos DB** | Multi-model, globally distributed, SLA-backed single-digit-ms | Genuine global distribution or extreme scale driver; access patterns known upfront | "It's NoSQL so it's modern" — YAGNI probe REQUIRED; RU model punishes unplanned access patterns |
| **Cache for Redis** | Managed Redis: cache, session, distributed locks, pub/sub light | Hot-path reads, session state, rate-limit counters | As a primary store |
| **Blob Storage** | Object storage; tiers (hot/cool/archive), SAS tokens, static sites | Files, images, exports, backups, data lake ground floor | Queryable structured data |
| **Table Storage** | Ultra-cheap key/attribute store | Massive simple lookup tables, logs, IoT-ish appends | Anything needing queries beyond key+partition |

## Messaging & integration — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **Storage Queues** | Dead-simple queue | One producer, one consumer, at-least-once is fine | Ordering, topics, DLQ sophistication needed |
| **Service Bus** | Enterprise broker: queues + topics/subscriptions, sessions (ordering), DLQ, transactions, dedup | Reliable async between containers; pub/sub with filtering; FIFO per entity | Trivial single-queue cases (Storage Queues) or high-throughput streaming (Event Hubs) |
| **Event Grid** | Reactive event routing (discrete events, push, filters); native events from Azure services | "When X happens anywhere, notify these consumers"; webhooks at scale | Ordered streams or heavy payloads |
| **Event Hubs** | High-throughput event streaming (Kafka-compatible endpoint) | Telemetry, clickstreams, log pipelines, replayable streams | Business messaging with per-message semantics (Service Bus) |
| **API Management** | API gateway: auth, rate limiting, versioning, developer portal | Multiple consumers/partners of your APIs; central policy point | A single internal API (built-in ingress suffices) |
| **Logic Apps** | Low-code integration workflows, huge connector library | SaaS-to-SaaS integrations, approvals, "citizen" workflows | Core domain logic (keep that in code) |

**YAGNI note:** one API + one worker often needs no broker at all — a Postgres job
table with SKIP LOCKED is a legitimate first answer. Say so when true.

## Networking, identity, edge — quick purpose map

- **VNet + Private Endpoints** — private traffic to PaaS (DB, storage); the default
  posture for data stores in anything serious.
- **Front Door** — global L7 entry: CDN + WAF + multi-region routing. Only with a
  real global/WAF driver; otherwise built-in ingress + custom domain.
- **Application Gateway** — regional L7 LB + WAF inside a VNet.
- **Key Vault** — secrets, keys, certs. Always present; never secrets in config.
- **Entra ID + Managed Identities** — service-to-service auth without credentials;
  the default. Workload identity federation for external CI/CD.
- **Azure Monitor / App Insights / Log Analytics** — one telemetry sink, distributed
  tracing, alerts. Decide retention and sampling consciously (cross-cutting concern
  in C3, not per component).

## Well-Architected Framework quick check (design-level, no pricing)

- **Reliability:** is single-region an explicit decision (usually fine early — say
  so)? Zone redundancy where offered? Backup/PITR configured? RTO/RPO written down
  in an ADR?
- **Security:** managed identities everywhere? Private endpoints on data stores?
  Key Vault wired in? LGPD: does data residency require Brazil South?
- **Cost (conceptual only):** anything always-on that could scale to zero? Tiers
  matched to actual load profile? Flag as "size later with real prices".
- **Operational excellence:** single log sink; alerts on the 3 metrics that matter;
  IaC (Bicep/Terraform) from day 1; deploy path defined.
- **Performance:** the Stage 1 priority ranking decides where NOT to optimize.

Output: top 3 risks + cheapest mitigation each, as a table or short bullets — never
five pillars of prose.

# AWS Component Catalog — Purpose & Fit

Load when the user's cloud is AWS. A **what-it-is-for** catalog for mapping C2/C3
elements to services and reasoning about fit. **No pricing here by design** — if the
user asks about cost, research current prices at that moment.

## Compute — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **ECS Fargate** | Serverless containers; the sane default for sustained APIs/workers | Container images, no cluster ops, predictable service model | Scale-to-zero matters and traffic is near-nil (App Runner/Lambda) |
| **App Runner** | Simplest managed containers: repo/image → URL | Small APIs, minimal config appetite | Fine-grained networking/scaling control needed |
| **Lambda** | Event-driven functions; deep event-source integration (SQS, S3, EventBridge, DynamoDB streams) | Glue, queue consumers, scheduled jobs, bursty handlers | Sustained high-throughput APIs; latency-critical p99 paths (cold starts) |
| **Step Functions** | Managed state machines/sagas | Multi-step workflows, retries/compensation, human waits, fan-out | Simple linear jobs (plain Lambda) |
| **EKS** | Full managed Kubernetes | Many services + platform team + K8s ecosystem need | <10 services or <3 engineers — YAGNI probe REQUIRED |
| **EC2** | IaaS | Lift-and-shift, special runtimes, licensed software | Anything a managed option covers |

## Data — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **RDS PostgreSQL** | Managed Postgres; relational default | Transactional domain, SQL fluency, open ecosystem | — |
| **Aurora (+ Serverless v2)** | AWS-built Postgres/MySQL-compatible; better failover, storage autoscale, capacity that scales in ACUs | Spiky or fast-growing load, replica-heavy reads, tighter RTO | Small steady workloads (plain RDS is simpler); note Serverless v2 has a capacity floor |
| **DynamoDB** | Serverless key-value/wide-column, single-digit-ms at any scale | Access patterns known upfront, massive scale, serverless stack | Ad-hoc queries/reporting; relational modeling reflexes — design access-patterns-first or don't use it |
| **ElastiCache Redis** | Managed Redis: cache, sessions, locks, counters | Hot-path reads, session state | Primary store |
| **S3** | Object storage; lifecycle tiers, events, static hosting | Files, exports, backups, data lake ground floor | Queryable structured data |

## Messaging & integration — what each option is for

| Service | Purpose | Right when | Wrong when |
|---|---|---|---|
| **SQS** | The default queue: at-least-once, DLQ built in; FIFO variant for ordering | Decoupling producer/consumer; Lambda event source | Fan-out (add SNS) or streaming (Kinesis) |
| **SNS** | Pub/sub fan-out to many subscribers (SQS, Lambda, HTTP, mobile) | One event, many independent consumers (classic SNS→SQS pattern) | Ordered/replayable streams |
| **EventBridge** | Event bus with content-based routing rules, schedules, SaaS partners | Domain events across services; cron; third-party SaaS events | High-throughput telemetry (Kinesis); simple 1:1 (SQS) |
| **Kinesis Data Streams** | Ordered, replayable, sharded streaming | Telemetry, clickstreams, log pipelines, stream processing | Business messaging with per-message semantics |
| **API Gateway** | Managed API front door: auth (Cognito/IAM/JWT), throttling, keys, WebSockets | Public/partner APIs, Lambda-backed APIs | A single internal service behind an ALB |
| **AppSync** | Managed GraphQL | GraphQL chosen deliberately (an ADR, not a fashion) | REST already fits |

**YAGNI note:** one API + one worker often needs no broker — a Postgres job table with
SKIP LOCKED is a legitimate first answer. Say so when true.

## Networking, identity, edge — quick purpose map

- **VPC + security groups** — lock stores to their consumers; never 0.0.0.0/0 to a DB.
- **ALB** — regional L7 load balancer; the standard front for ECS services.
- **CloudFront** — CDN/edge; only with a real global-latency or WAF-at-edge driver.
- **Secrets Manager / SSM Parameter Store** — secrets and config; never in env files
  committed anywhere. Rotation lives here.
- **IAM roles (+ IRSA on EKS)** — service-to-service auth without long-lived keys; the
  default posture.
- **CloudWatch (+ X-Ray)** — one telemetry sink, tracing, alarms; retention set
  consciously (a C3 cross-cutting decision, not per component).

## Well-Architected quick check (design-level, no pricing)

- **Reliability:** single-AZ vs multi-AZ as an explicit ADR (multi-AZ is a real
  tradeoff, not a default). Backups/PITR on. RTO/RPO written down.
- **Security:** least-privilege IAM, security groups scoped, Secrets Manager wired,
  encryption at rest on. LGPD: does residency require sa-east-1?
- **Cost (conceptual only):** anything always-on that could be event-driven? Flag
  sizing as "validate with real prices later".
- **Operational excellence:** log retention set (never "never expire"); alarms on the
  3 metrics that matter; IaC (Terraform/CDK) from day 1; deploy path defined.
- **Performance:** the Stage 1 priority ranking decides where NOT to optimize.

Output: top 3 risks + cheapest mitigation each — table or short bullets, never five
pillars of prose.

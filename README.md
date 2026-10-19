<p align="center">
  <img src="assets/ai-operations-center-icon.png" alt="AI Operations Center product icon" width="132" />
</p>

# AI Operations Center

**Datadog for AI Agents and n8n Automations** — a small-business operations layer for monitoring and managing AI systems, workflows, APIs, and automation infrastructure.

*Positioning shorthand for the product direction; not a claim of feature parity with any existing platform.*

The product is designed to provide near-real-time operational visibility into AI agents, workflow executions, API usage and health, token usage and estimated costs, error rates, uptime, rate limits, and system performance. The concept combines infrastructure observability, AI execution tracing, and workflow operations in one small-business-focused mental model.

<p>
  <img src="https://img.shields.io/badge/AI%20INFRASTRUCTURE-OBSERVABILITY-ff6b35?style=for-the-badge" alt="AI infrastructure observability" />
  <img src="https://img.shields.io/badge/FOCUS-PRODUCTION%20OPERATIONS-18b8d4?style=for-the-badge" alt="Production operations focus" />
  <img src="https://img.shields.io/badge/STATUS-PORTFOLIO%20CONCEPT-111827?style=for-the-badge" alt="Portfolio concept" />
</p>

**Product concept · Small-business AI infrastructure · Observability-first systems design**  
**Kevin Doherty** · AI Systems Engineer · Automation Architect · Full-Stack Product Builder

> **Project status:** This repository is a portfolio concept and architecture specification—not a functioning monitoring product. The architecture and proposed stack describe a direction for the system; no live integrations, production metrics, or operational performance are claimed.

## The problem

Small teams often assemble AI agents, n8n workflows, SaaS APIs, and customer-facing automations from separate tools. Each tool has its own status screen, logs, usage view, and alerting behavior. When something fails, an operator may have to trace a customer impact across several dashboards and manually reconstruct what happened.

The opportunity is a shared control plane that makes the health and activity of an AI-enabled operation understandable at a glance.

## The solution

**AI Operations Center** brings operational signals into one dashboard concept. It connects agent activity, n8n execution outcomes, API health, resource use, and user activity to an analytics and alerting layer, then presents concise, actionable views for operators.

The product is aimed at small businesses and lean teams that need practical visibility: enough context to notice incidents, diagnose common failures, understand consumption, and decide what needs attention—without turning operations into a full-time specialist discipline.

## Six product pillars

| Pillar | Operational visibility |
| --- | --- |
| **Fleet Health** | Agent status, workflow success, API request volume, availability and latency, rate limits, and service uptime. |
| **Cost Intelligence** | Token consumption, model spend, and workflow execution costs; clearly distinguish estimates from authoritative provider billing. |
| **Incident Center** | Failed workflows, API outages, agent exceptions, rate-limit events, and the context needed for triage. |
| **Governance** | Audit logs, approval queues, human-in-the-loop controls, and privacy-aware access to operational activity. |
| **Business Metrics** | Leads processed, calls booked, and optional revenue attribution—only from authorized sources and agreed definitions; no formulas or commercial rules are disclosed here. |
| **Agent Registry** | A catalog of agents, workflows, and API dependencies with ownership, status, and documentation. |

The center’s core promise is to help an operator answer: **What is failing? What is costing more than expected? What requires intervention?** The six pillars are product goals, not features already delivered by this concept repository.

## Architecture

The system is modeled as an observability pipeline: operational sources emit or expose signals; connectors collect and normalize them; analytics maps those signals to the six product pillars; and the dashboard turns them into operator decisions. For a small-business MVP, start with webhooks and lightweight connectors. Add Kafka or NATS only if throughput, replay, or multi-consumer requirements justify their added operating cost.

<p align="center">
  <img src="diagrams/ai-operations-center-architecture.png" alt="AI Operations Center architecture: AI agents, n8n workflows, APIs, and user activity send signals through webhook connectors and an optional event bus to six observability pillars, dashboards and alerts" width="100%" />
</p>

```text
AI agents ───────┐
n8n workflows ───┼──> Webhook / connector intake ──> Optional event bus ──> Metrics, logs & events ──> Six pillars ──> Dashboard
APIs/services ──┘                      (Kafka/NATS only if needed)                                        └──> Alerts
```

The architecture is conceptual. It intentionally avoids vendor-specific endpoints, event schemas, customer data, and production topology. See [`diagrams/architecture.dot`](diagrams/architecture.dot) and [`docs/architecture.md`](docs/architecture.md) for the source and design notes.

## Proposed technology stack

This is a **proposed stack for an implementation**, not a statement that the software has already been built or deployed.

| Area | Proposed technology | Intended role |
| --- | --- | --- |
| Dashboard | **TypeScript**; Next.js is a candidate | A focused product interface for responsive operational views. Grafana is an option for internal or early-stage visualization; no UI framework is selected yet. |
| Service layer | **Python** | Integration coordination, metric processing, analytics tasks, and API services. |
| Integration contracts | **REST APIs and webhooks** | Initial connectors for agents, n8n workflows, and external services. |
| Event transport | **Webhooks first; Kafka/NATS optional** | Begin with the simplest reliable transport; evaluate a bus only when scale, replay, or fan-out requirements justify it. |
| Workflow signals | **n8n** | Workflow execution and health signals exposed through appropriate supported integrations. |
| Observability model | **Metrics, events, and error summaries** | A normalized view across sources, without requiring raw prompt or customer data. |
| Storage | **Pluggable telemetry storage** | Selected later based on volume, retention, query needs, and operating cost. |
| Alerting | **Configurable rules and notification adapters** | Surface meaningful failures and threshold changes without excessive alert noise. |

<p>
  <img src="https://img.shields.io/badge/Python-Service%20Layer-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python service layer" />
  <img src="https://img.shields.io/badge/TypeScript-Operations%20UI-3178C6?style=flat-square&logo=typescript&logoColor=white" alt="TypeScript operations UI" />
  <img src="https://img.shields.io/badge/REST%20APIs-Integrations-18b8d4?style=flat-square" alt="REST API integrations" />
  <img src="https://img.shields.io/badge/n8n-Workflow%20Signals-EA4B71?style=flat-square&logo=n8n&logoColor=white" alt="n8n workflow integration" />
  <img src="https://img.shields.io/badge/OBSERVABILITY-Metrics%20%7C%20Events%20%7C%20Errors-ff6b35?style=flat-square" alt="Observability signals" />
</p>

## System design priorities

- **One operational picture:** bring agent, workflow, and API health into a coherent view while preserving the source of each signal.
- **Normalized, source-aware telemetry:** make cross-system comparison possible without erasing provider context.
- **Useful freshness:** display when each signal was last received so delayed data is not mistaken for healthy data.
- **Actionable alerts:** prioritize a small number of meaningful conditions over noisy notifications.
- **Graceful degradation:** keep the dashboard useful when one connector or provider is unavailable, and make stale or missing telemetry visible.
- **Cost transparency:** separate measured provider charges from estimates based on usage and pricing assumptions.
- **Privacy by design:** collect only the operational metadata needed; avoid retaining prompts, secrets, or unnecessary customer content.
- **Small-business operability:** favor clear defaults, simple onboarding, and understandable actions over complex enterprise configuration.

## Architecture choices to validate

- **Ingestion:** Start with supported webhooks and lightweight polling where needed. Kafka or NATS may become appropriate if the product demonstrates a need for durable replay, sustained event volume, or multiple independent consumers; they are not default infrastructure for a small-business MVP.
- **Dashboard:** A Next.js/TypeScript interface could support a cohesive customer-facing SaaS experience. Grafana may accelerate an internal or early operational dashboard. Validate the operator experience before committing to either approach.
- **Signal types:** Keep metrics, events, and error summaries distinct, preserving source and freshness. Avoid collecting raw prompts or customer content as a shortcut to debugging.
- **Business metrics:** Leads, bookings, and attribution are opt-in product views dependent on authorized data sources and definitions. No revenue-calculation logic or actual business results are included in this repository.

## Challenges this project addresses

### Fragmented system visibility

A business may depend on multiple agents, automation workflows, and APIs. The proposed operations center groups health and execution signals by source and system so operators can trace issues across a workflow without pretending that all providers expose identical telemetry.

### Noisy and inconsistent signals

Providers report activity and failures in different formats and at different times. A metrics-collection layer needs to normalize comparable signals, preserve provenance, and indicate whether the data is delayed or incomplete.

### Understanding usage and cost

Token consumption and cost can be useful operating indicators, but pricing and billing data are not uniform. The product should label estimated costs clearly, disclose assumptions, and distinguish estimates from confirmed charges.

### Alert fatigue

Monitoring every event is not the same as helping an operator. Alerting should be configurable, contextual, and focused on conditions that merit action, with sensible controls against repeated notifications.

### Secure, privacy-conscious operations

Observability must not become a back door for sensitive prompts, customer data, or credentials. The design favors least-privilege integrations, data minimization, redaction, explicit retention choices, and access-aware reporting.

## Why this is a stronger AI portfolio project

Many AI demos emphasize prompts and generated answers. This concept emphasizes the **operational system around AI**: runtime health, workflow reliability, integration boundaries, usage and cost visibility, failure diagnosis, and operator experience.

It complements work in agents, MCP integrations, and n8n automation by demonstrating an end-to-end systems perspective: building AI-enabled functionality is only part of the job; teams also need ways to observe it, understand its behavior, and operate it responsibly.

## What this demonstrates

- **Python and TypeScript:** a proposed split between service-side operations and an interactive web dashboard.
- **API integration:** a connector-oriented approach across heterogeneous AI, workflow, and service providers.
- **Dashboard product thinking:** organizing technical signals around the decisions small-business operators need to make.
- **System design:** a collection, storage, analytics, alerting, and presentation pipeline with explicit boundaries.
- **Observability:** metrics, events, errors, freshness, uptime, and usage as first-class operational concerns.
- **AI infrastructure:** a focus on the lifecycle and reliability of systems that use AI—not just model output.
- **SaaS thinking:** a product direction that values onboarding, tenant-aware design, operational simplicity, privacy, and sustainable usage visibility.

## Roadmap

### Phase 1 — Fleet Health and Incident Center

- Start with a small set of agent, n8n, and API signal sources.
- Deliver a read-only view of status, execution outcomes, API health, error summaries, and signal freshness.
- Use webhooks and lightweight connectors first; keep the event bus optional.

### Phase 2 — Cost Intelligence

- Add token and model-usage visibility where providers expose reliable usage data.
- Show cost estimates with clear pricing assumptions and distinguish them from confirmed provider charges.
- Surface API usage and rate-limit trends alongside workflow execution costs.

### Phase 3 — Governance and Agent Registry

- Add an inventory of agents, workflows, and dependencies with ownership and documentation.
- Introduce privacy-aware audit history, role-based access, and human approval views before considering control actions.

### Phase 4 — Business Metrics

- Explore leads processed, calls booked, and revenue attribution only when a customer authorizes the source and agrees on metric definitions.
- Keep business metrics optional and clearly separate from technical health signals.

### Phase 5 — SaaS readiness

- Validate tenant isolation, retention and deletion controls, secure onboarding, and support workflows.
- Evaluate system cost, integration reliability, and operator value before expanding the connector catalog.
## Repository structure

```text
ai-operations-center/
├── README.md
├── assets/
│   └── ai-operations-center-icon.png
├── diagrams/
│   ├── ai-operations-center-architecture.png
│   └── architecture.mmd
├── docs/
│   ├── architecture.md
│   ├── product-brief.md
│   └── security-and-privacy.md
└── screenshots/
    └── README.md
```

## Status and contribution boundary

This is a **portfolio product concept** documenting an intended product direction and high-level design. There is no runnable dashboard, production integration, credential setup, or deployed service in this repository. Future implementation work should begin with a clearly scoped MVP and synthetic test data—not with access to real customer systems.

---

**Kevin Doherty** · AI Systems Engineer · Automation Architect · Full-Stack Product Builder  
**EDGE | AGENCY**

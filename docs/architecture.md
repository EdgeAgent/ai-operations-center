# Architecture overview

AI Operations Center is a portfolio concept for a cross-system observability pipeline. The architecture is designed around clear stages rather than provider-specific implementation details.

## Logical stages

1. **Signal sources:** AI agents, n8n workflows, connected APIs and services, and relevant user-activity events.
2. **Metrics collector and connectors:** collect supported telemetry, normalize comparable signals, and preserve source attribution and freshness.
3. **Telemetry storage:** retain operational signals according to future volume, access, cost, privacy, and retention requirements. The storage technology is intentionally undecided.
4. **Analytics and alert evaluation:** aggregate activity, highlight errors and usage trends, and evaluate operator-configured alert conditions.
5. **Operations dashboard and notifications:** present a prioritized operational view and deliver actionable alerts.

## Design concerns

- A missing or stale signal must not be mistaken for a healthy service.
- Cost views should clearly distinguish provider-reported charges from estimated costs.
- Connector failures should be visible without making unrelated parts of the dashboard unusable.
- User activity reporting should be privacy-conscious and role-aware.
- Raw prompts, secrets, and unnecessary customer content should not be collected as routine telemetry.

The diagram in `../diagrams/architecture.dot` is a conceptual flow. It does not define endpoints, event schemas, deployment topology, vendor-specific APIs, or a production storage model.

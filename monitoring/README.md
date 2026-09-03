# Monitoring Reference

The reference observability stack is deliberately provider-neutral:

- Prometheus for metrics.
- node_exporter for Linux host metrics.
- cAdvisor for container metrics where required.
- Grafana Alloy for telemetry collection/forwarding.
- Loki for logs.
- Grafana for dashboards.
- Alertmanager for routing actionable alerts.

Production deployment manifests are environment-specific and are not fabricated in this proof-of-concept. The architecture and required telemetry flows are documented in `docs/observability.md`.

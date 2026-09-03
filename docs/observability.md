# Distributed Observability and Compliance

## Objective

Provide a central operational and compliance view without making the solution dependent on one cloud provider's proprietary monitoring stack.

## Proposed Stack

- **Prometheus** — metrics collection and querying.
- **node_exporter** — Linux host metrics.
- **cAdvisor** — container resource metrics where appropriate.
- **Grafana Alloy** — vendor-neutral telemetry collection/forwarding.
- **Loki** — centralized log aggregation.
- **Grafana** — dashboards and single operational view.
- **Alertmanager** — alert routing for actionable conditions.

## Data Flow

```text
50 Linux/Docker tenant VMs
       |              |
 host/container     application/
 metrics            security logs
       |              |
       v              v
 Prometheus        Alloy -> Loki
       \              /
        \            /
            Grafana
               |
          Alertmanager
```

## What Is Observed

### Host
CPU, memory, filesystem capacity, load, network state, uptime and service health.

### Docker / Containers
Container state, restarts, resource pressure and expected workload availability.

### Logs and Security Events
System logs, Docker/container logs, application logs and selected audit/security events are centrally searchable.

### Compliance
AWX remains the authoritative execution history for automation. Selected compliance outcomes can be exported or represented in Grafana so operators can see fleet compliance status alongside operational telemetry.

## Alerting Examples

- Host unreachable.
- Filesystem capacity threshold exceeded.
- Docker daemon unavailable.
- Expected container stopped/restarting repeatedly.
- Compliance check failed.
- Drift detected on a security-sensitive control.
- Patching health gate failed.

## Vendor Neutrality

The monitoring architecture uses open protocols and open-source components and can operate across heterogeneous providers. Provider-native telemetry can still be integrated where useful, but it is not the only management view.

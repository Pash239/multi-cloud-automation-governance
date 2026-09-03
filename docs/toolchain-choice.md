# Automation Toolchain Choice

## Selection Criteria

The toolchain is selected against five requirements:

1. provider neutrality across heterogeneous cloud footprints;
2. strong Linux configuration and operational automation;
3. auditable centralized orchestration;
4. safe rolling change control;
5. open, portable observability.

## Git

**Role:** source of truth and change control.

Git stores approved Ansible content, Compose definitions and architecture documentation. Pull requests provide reviewable change history before automation is synchronized into AWX.

## AWX

**Role:** centralized automation control plane.

AWX provides inventory, credentials, RBAC, scheduling, workflow orchestration, approval gates, execution nodes and job history. This makes Ansible operationally manageable across the 50-host estate without depending on one cloud provider.

## Ansible

**Role:** in-guest configuration, compliance, hardening and operational change.

Ansible is a strong fit because the estate consists of existing Linux hosts across multiple providers. It is agentless, SSH-based and idempotent, and the same roles/playbooks can operate across provider boundaries.

Ansible is not described as a permanently running drift agent. Recurring AWX jobs and host/security telemetry provide the detection loop.

## Terraform

**Role:** supporting infrastructure-as-code.

Terraform is useful for management-plane infrastructure and new provider resources. It is intentionally not the primary tool for in-guest configuration drift on the 50 existing Linux appliances.

## Prometheus

**Role:** metrics and alert-rule evaluation.

Prometheus provides an open, provider-neutral metrics model with broad Linux and container exporter support.

## Loki + Grafana Alloy

**Role:** centralized logs and telemetry forwarding.

This combination keeps log collection portable and integrates cleanly with Grafana without forcing dependence on a proprietary provider logging service.

## Grafana + Alertmanager

**Role:** operational visualization and actionable notification routing.

Grafana provides the main fleet view across metrics, logs and selected compliance outcomes. AWX remains authoritative for detailed automation execution records.

## Why This Combination

The stack is intentionally conservative rather than a collection of overlapping tools. Each component has a clear responsibility:

```text
Git          -> define and review
AWX          -> govern and orchestrate
Ansible      -> enforce and validate
Terraform    -> provision supporting infrastructure
Prometheus   -> measure
Loki/Alloy   -> collect logs
Grafana      -> visualize
Alertmanager -> route alerts
```

This keeps the solution explainable, supportable and independent of the underlying cloud providers.

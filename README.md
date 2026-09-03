# Multi-Cloud Automation Governance

A vendor-neutral reference implementation for governing, hardening, patching and observing a distributed fleet of Linux virtual machines running Docker-based tenant workloads.

## Scenario

The target environment is a fleet of 50 isolated, single-tenant Linux VMs distributed across multiple hosting providers. Each VM runs a Docker-based application stack consisting of a Vue.js frontend, PHP middleware and backend microservices.

The design preserves tenant isolation while introducing one operational control plane above the cloud providers.

## Technical Strategy

- **Git** stores the approved desired state and change history.
- **AWX** provides central inventory, credentials, RBAC, scheduling, workflows and execution evidence.
- **Ansible** enforces Linux and Docker configuration, detects/remediates drift and orchestrates rolling maintenance.
- **Terraform** is reserved for supporting infrastructure/bootstrap where appropriate.
- **Prometheus, Loki and Grafana** provide provider-neutral observability.

## Architecture

```text
                         Git
                          |
                          v
                    AWX Control Plane
                          |
                       Ansible
                          |
              Secure Management Paths
                  +-------+-------+
                  |       |       |
                  v       v       v
              Provider A  B      Other
               Linux + Docker tenant VMs
                  |       |       |
                  +-------+-------+
                          |
                  Metrics / Logs / Events
                          |
              Prometheus + Loki + Grafana
```

## Repository Structure

```text
.
├── ansible/
│   ├── inventories/
│   ├── playbooks/
│   └── roles/
├── docker/
├── docs/
├── monitoring/
├── terraform/
└── README.md
```

## Core Operational Flows

### Drift

`Detect -> Classify -> Remediate -> Verify -> Record`

### Patching

`Pre-check -> Canary -> Update -> Health Gate -> Rolling Batch -> Validate`

A failed health gate stops wider rollout.

### Hardening

Controls are applied at four layers:

1. Linux host
2. Docker daemon/socket
3. Containers
4. Inter-container networks

## Design Guardrails

- No secrets in Git.
- No public SSH exposure required by the design.
- No Docker socket mounts into normal application containers.
- Low-risk drift may be auto-remediated; high-risk changes require approval.
- Canary-first maintenance limits blast radius.
- The design prevents concurrent fleet-wide downtime; it does not claim zero interruption for a standalone tenant VM that requires a reboot.

## Documentation

- [Architecture](docs/architecture.md)
- [Drift Management](docs/drift-management.md)
- [Patching Lifecycle](docs/patching-lifecycle.md)
- [Security Hardening](docs/security-hardening.md)
- [Observability](docs/observability.md)

## Purpose

This repository is a technical proof-of-concept and architecture reference showing how a Systems Automation Engineer could implement consistent multi-cloud governance for a Linux and Docker fleet without depending on one cloud provider's management stack.

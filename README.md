# Multi-Cloud Automation Governance

Portfolio project demonstrating multi-cloud Linux automation, governance, security, patching, drift management and observability using Ansible, AWX, Terraform, Docker, Prometheus, Loki and Grafana.

A vendor-neutral reference implementation for governing, hardening, patching and observing a distributed fleet of Linux virtual machines running Docker-based tenant workloads.

## Scenario

The target environment is a fleet of 50 isolated, single-tenant Linux VMs distributed across multiple hosting providers. Each VM runs a Docker-based application stack consisting of a Vue.js frontend, PHP middleware and backend microservices.

The design preserves tenant isolation while introducing one operational control plane above the cloud providers.

## Technical Strategy

- **Git** stores the approved desired state and change history.
- **AWX** provides central inventory, credentials, RBAC, scheduling, workflows, approval gates and execution evidence.
- **Ansible** enforces Linux and Docker configuration, detects/remediates drift and orchestrates rolling maintenance.
- **Terraform** is reserved for supporting infrastructure/bootstrap where appropriate.
- **Prometheus, Loki and Grafana** provide provider-neutral observability.

## Skills Demonstrated

- Linux systems automation and hardening
- Ansible roles and operational playbooks
- AWX workflow orchestration and governance
- Terraform infrastructure/bootstrap patterns
- Docker security and lifecycle management
- Multi-cloud operational design
- Patch orchestration and drift remediation
- Monitoring, logging and observability
- Git-based change control and CI validation
- Production-oriented rollback and blast-radius thinking

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

The detailed architecture document contains GitHub-rendered Mermaid blueprints for the governance and change flows.

## Repository Structure

```text
.
├── ansible/
│   ├── inventories/production/hosts.yml
│   ├── playbooks/
│   │   ├── compliance-check.yml
│   │   ├── remediate-drift.yml
│   │   ├── patch-linux.yml
│   │   ├── update-containers.yml
│   │   └── validate-health.yml
│   ├── roles/
│   │   ├── linux_baseline/
│   │   └── docker_hardening/
│   ├── ansible.cfg
│   └── requirements.yml
├── awx/
│   └── workflow-design.md
├── docker/
│   └── compose-baseline.yml
├── docs/
│   ├── architecture.md
│   ├── drift-management.md
│   ├── patching-lifecycle.md
│   ├── security-hardening.md
│   ├── observability.md
│   ├── toolchain-choice.md
│   ├── assumptions-and-boundaries.md
│   └── architecture-walkthrough.md
├── monitoring/
├── terraform/
├── .github/workflows/ansible-syntax.yml
├── .gitignore
└── README.md
```

## Core Operational Flows

### Drift

`Detect -> Classify -> Remediate -> Verify -> Record`

AWX schedules compliance checks. Deterministic, approved low-risk drift can be remediated through the `linux_baseline` and `docker_hardening` roles. Potentially disruptive or suspicious drift is held for controlled remediation rather than blindly self-healed.

### Patching

`Pre-check -> Canary -> Update -> Health Gate -> Rolling Batch -> Validate`

A failed health gate stops wider rollout. AWX workflow gates provide orchestration around the Ansible batch controls.

### Hardening

Controls are applied at four layers:

1. Linux host
2. Docker daemon/socket
3. Containers
4. Inter-container networks

## Proof-of-Concept Automation

The repository includes executable reference automation The repository includes executable reference automation rather than pretending to represent a real production estate. Example IP addresses, registry names, image versions and health endpoints are placeholders and must be replaced with environment-specific values before execution.

The Ansible content demonstrates:

- scheduled compliance validation;
- approved drift remediation;
- Linux baseline enforcement;
- Docker daemon hardening;
- canary/rolling Linux patching;
- controlled container image reconciliation;
- post-change host/container/application health validation.

A GitHub Actions workflow syntax-checks the Ansible playbooks on relevant branch/PR changes.

## Design Guardrails

- No secrets in Git.
- No public SSH exposure required by the design.
- No Docker socket mounts into normal application containers.
- Low-risk drift may be auto-remediated; high-risk changes require approval.
- Canary-first maintenance limits blast radius.
- A failed health gate stops wider rollout.
- The design prevents concurrent fleet-wide downtime; it does not claim zero interruption for a standalone tenant VM that requires a reboot.
- Environment-specific production values are not fabricated in this proof-of-concept.

## Documentation

- [Architecture](docs/architecture.md)
- [Drift Management](docs/drift-management.md)
- [Patching Lifecycle](docs/patching-lifecycle.md)
- [Security Hardening](docs/security-hardening.md)
- [Observability](docs/observability.md)
- [Automation Toolchain Choice](docs/toolchain-choice.md)
- [Assumptions and Boundaries](docs/assumptions-and-boundaries.md)
- [AWX Workflow Design](awx/workflow-design.md)
- [Architecture Walkthrough](docs/architecture-walkthrough.md)

## Purpose

This repository is a technical proof-of-concept and architecture reference demonstrating a practical approach to consistent multi-cloud governance for Linux and Docker workloads without depending on a single cloud provider’s management stack.

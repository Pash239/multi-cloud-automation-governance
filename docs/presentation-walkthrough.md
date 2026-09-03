# 15-Minute Technical Walkthrough

This document provides a concise order for presenting the repository as a technical solution.

## 1. Problem and Strategy — ~1 minute

The estate contains 50 isolated Linux/Docker tenant appliances distributed across multiple hosting providers. The strategy is to place a provider-neutral governance plane above those environments rather than manage each VM manually or depend on one cloud vendor's management service.

**Key message:** Git defines the approved state, AWX governs execution, and Ansible enforces it.

## 2. Architecture Blueprint — ~2 minutes

Open `docs/architecture.md`.

Explain:

- Git as the reviewed source of truth;
- AWX inventory, credentials, RBAC, schedules and workflows;
- Ansible execution across secure management paths;
- execution nodes/bastions/private routing where provider topology requires them;
- Prometheus/Loki/Grafana as the common observability plane.

## 3. Drift Management — ~2.5 minutes

Open `docs/drift-management.md` and `ansible/playbooks/compliance-check.yml`.

Explain the loop:

`Detect -> Classify -> Remediate -> Verify -> Record`

Then show `remediate-drift.yml` and the `linux_baseline` / `docker_hardening` roles as proof that approved controls are represented as code.

Emphasize that high-risk drift is not blindly self-healed.

## 4. Patching Lifecycle — ~3 minutes

Open `docs/patching-lifecycle.md` and `awx/workflow-design.md`.

Explain:

- pre-checks;
- 1–2 canary hosts;
- health gates;
- Ansible `serial` rolling batches;
- stop-on-failure behavior;
- rollback/recovery distinction between containers and host OS.

Clarify that a standalone tenant VM may experience a controlled reboot interruption; the solution prevents concurrent fleet-wide downtime rather than claiming impossible per-tenant zero downtime without redundancy.

## 5. Zero-Trust Hardening — ~3 minutes

Open `docs/security-hardening.md`, the hardening roles and `docker/compose-baseline.yml`.

Cover four layers:

1. Linux host baseline;
2. Docker daemon/socket;
3. container privilege/sandboxing;
4. inter-container networks.

Point out non-root containers, dropped capabilities, `no-new-privileges`, read-only filesystems where appropriate, no normal application access to the Docker socket, and explicit frontend/backend network boundaries.

## 6. Observability and Compliance — ~2 minutes

Open `docs/observability.md`.

Explain Prometheus metrics, Loki/Alloy logs, Grafana dashboards, Alertmanager notifications and AWX job history as automation evidence.

## 7. Toolchain and Close — ~1.5 minutes

Open `docs/toolchain-choice.md`.

Close with the clear responsibility model:

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

**Outcome:** 50 isolated tenant appliances remain isolated, but operationally become one consistent, auditable, safely automated fleet.

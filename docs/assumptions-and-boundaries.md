# Assumptions and Boundaries

This proof-of-concept is based on the assessment scenario rather than access to the client's real production estate.

## Assumptions

- The 50 tenant appliances are reachable through approved management paths from AWX/Ansible execution environments.
- SSH key-based automation is permitted through a dedicated least-privilege account.
- Each VM runs a supported Linux distribution and a standard Docker runtime.
- The application exposes or can expose a local health check suitable for automated validation.
- A controlled maintenance process exists for changes requiring restart/reboot.
- The organization can provide a registry/release process for approved immutable container images.

## Explicit Boundaries

- The external database is outside the solution scope.
- Real provider account details, network CIDRs, VM addresses and credentials were not provided and are therefore not fabricated.
- The repository does not claim to be a deployable copy of Acme's production environment.
- Terraform modules are not invented for unknown provider resources; Terraform's intended responsibility is documented instead.
- The observability directory describes the portable stack without inventing environment-specific production manifests.
- A standalone single-VM tenant can experience a controlled service interruption during host reboot or Docker daemon restart. Fleet-wide concurrent downtime is prevented through canaries and rolling batches.

## Productionization Work Required

Before real deployment, the following must be agreed with the client:

1. provider/network connectivity and AWX execution-node placement;
2. exact Linux distributions and supported package repositories;
3. approved SSH/sudo/security baseline;
4. application service names and health endpoints;
5. container registry and image-signing/scanning process;
6. maintenance windows, patch rings and rollback/recovery policy;
7. monitoring retention, alert routing and log-volume requirements;
8. secrets-management integration;
9. compliance benchmark/profile and exception-management process.

Keeping these unknowns explicit makes the solution defensible: the architecture is concrete where the scenario provides enough information and parameterized where production facts are unavailable.

# Cross-Cloud Drift Management

## Desired State

Approved Linux, Docker and application-topology configuration is version-controlled in Git and implemented through Ansible roles and templates.

## Detection Loop

AWX schedules Ansible compliance/check jobs across the inventory. Results are compared with the approved state and retained as execution evidence.

```text
Git baseline
    |
AWX scheduled check
    |
Ansible validation
    |
Drift detected?
  /       \
No        Yes
|          |
Record   Classify risk
           |
      +----+----+
      |         |
   Low risk   High risk
      |         |
Auto-remediate Approval/window
      \         /
        Verify
          |
       Record
```

## Drift Examples

- Required package removed or unauthorized package introduced.
- SSH configuration changed outside the approved baseline.
- Firewall rule or sysctl setting changed.
- Docker daemon configuration altered.
- Required service stopped or unexpectedly enabled.
- Docker Compose definition or approved image reference changed.
- Container security options removed.

## Remediation Policy

Automation is not allowed to blindly repair every difference. Low-risk, deterministic controls can be automatically restored. High-risk changes that could interrupt connectivity or application service are flagged for controlled remediation or approval.

## Offline Hosts

An unreachable host is recorded as failed/unreachable; it is not treated as successfully compliant. The workflow continues according to the defined failure threshold, raises an operational signal and re-evaluates the host when connectivity is restored.

## Evidence

AWX job history records the host, playbook, result and execution time. Compliance status can additionally be exported to the monitoring/reporting layer for fleet-level visibility.

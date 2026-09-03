# AWX Workflow Design

AWX is the operational control plane around the Ansible content in this repository. It supplies inventory, credentials, RBAC, schedules, workflow gates and execution history.

## Project

Create an AWX Project that synchronizes this Git repository and tracks the approved production branch/ref.

## Inventories

Recommended groups:

- `canary` — 1–2 representative tenant appliances.
- `provider_a`, `provider_b`, etc. — connectivity/provider grouping.
- `production` — complete managed production fleet.
- optional OS-family and maintenance-window groups.

Real addresses and tenant identifiers are environment-specific and must not be committed as secrets.

## Credentials

Use AWX machine credentials or an integrated secret store. Credentials are scoped to the smallest practical execution zone/environment. No private keys or passwords are stored in Git.

## Job Templates

1. **Compliance Check** — `ansible/playbooks/compliance-check.yml`
2. **Approved Drift Remediation** — `ansible/playbooks/remediate-drift.yml`
3. **Linux Patching** — `ansible/playbooks/patch-linux.yml`
4. **Container Update** — `ansible/playbooks/update-containers.yml`
5. **Health Validation** — `ansible/playbooks/validate-health.yml`

## Compliance Workflow

```text
Scheduled Compliance Check
          |
       Success? -------------------- No ---> Alert / Investigate
          |
         Yes
          |
     Findings present?
       /        \
     No          Yes
     |            |
 Record       Risk classification
                  |
            Approved low risk?
              /        \
            Yes         No
             |           |
        Remediation    Approval / maintenance workflow
             |
        Health validation
             |
        Record evidence
```

## Patching Workflow

```text
Pre-check / Canary Linux Patching
             |
       Health Validation
          /       \
       FAIL       PASS
        |           |
   Stop + alert   Approval/gate
                    |
             Production rolling patch
                    |
               Health validation
                    |
             Evidence / completion
```

AWX workflow approval nodes can be used before the wider production stage. The Ansible playbook additionally constrains concurrency with `serial` and stops on failure.

## Scheduling

- Compliance: recurring schedule according to the client's governance policy.
- Patching: approved maintenance windows.
- Container deployment: change-controlled release workflow.

The exact frequency is intentionally not hard-coded because it depends on client SLA, maintenance policy and risk classification.

## Execution Nodes

Where the central AWX execution environment cannot securely reach a provider network, execution nodes can be assigned to appropriate instance groups closer to those managed hosts. This avoids making public SSH exposure the management model.

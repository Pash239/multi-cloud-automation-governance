# Host, Docker and Container Hardening

The security baseline follows defence in depth and uses CIS Linux/Docker guidance as a reference baseline, adapted to application requirements.

## Layer 1: Linux Host

- Minimal required packages and services.
- SSH key authentication for automation; restrict direct root/password login where operationally appropriate.
- Host firewall with explicit required ingress/egress.
- Hardened sysctl/kernel parameters.
- SELinux or AppArmor enforcement where supported and tested.
- auditd/security event collection.
- Time synchronization.
- Least-privilege administration and controlled sudo.
- Routine vulnerability and patch compliance.

## Layer 2: Docker Daemon and Socket

- Do not mount `/var/run/docker.sock` into normal application containers.
- Restrict Unix socket ownership and permissions.
- Do not expose an unauthenticated Docker remote API.
- If remote API access is genuinely required, protect it with authenticated TLS/mTLS and network controls.
- Consider rootless Docker or user namespace remapping where compatible with the workload.
- Maintain an approved `daemon.json` baseline through Ansible.

## Layer 3: Container Runtime

- Run application processes as non-root users where possible.
- Drop all Linux capabilities by default and add back only explicitly required capabilities.
- Enable `no-new-privileges`.
- Use read-only root filesystems where application design permits.
- Apply seccomp plus SELinux/AppArmor controls.
- Define CPU and memory limits.
- Use immutable, versioned and approved images.
- Keep secrets out of images and Compose files.

Example Compose policy:

```yaml
services:
  api:
    image: registry.example/api@sha256:APPROVED_DIGEST
    user: "10001:10001"
    read_only: true
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    networks:
      - backend
```

## Layer 4: Inter-Container Networking

Use separate Docker networks for frontend, middleware and backend trust zones. Publish only ports that must be reachable externally. Backend services should not be directly exposed to the public network. Communication paths are explicitly defined according to application need rather than allowing every container to communicate with every other container.

## Automation

Ansible roles enforce these controls and scheduled compliance runs detect unauthorized changes. Potentially disruptive controls are tested and rolled out through canaries before fleet-wide enforcement.

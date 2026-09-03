# Terraform Scope

Terraform is intentionally not the primary drift-management tool for the existing in-guest Linux/Docker configuration.

In this design Terraform is used where infrastructure-as-code is appropriate, for example:

- provisioning the AWX management platform and supporting network/security resources;
- creating provider-side bootstrap resources;
- standardizing new infrastructure introduced by the governance platform.

The existing heterogeneous tenant appliances are governed in-guest by Ansible. Provider-specific Terraform modules can be added later without changing the vendor-neutral operational control plane.

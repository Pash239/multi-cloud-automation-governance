# Production Inventory Notes

`hosts.yml` is an **illustrative inventory model** for this technical proof-of-concept. The IP addresses and tenant names are placeholders only.

Before use in a real environment:

- Replace example addresses with the client's approved management endpoints.
- Move dynamic/environment-specific values into AWX inventory, CMDB integration, inventory plugins, or protected variables as appropriate.
- Do not commit private keys, passwords, tokens, vault passwords, database credentials, or other secrets.
- Group hosts by provider/connectivity zone, OS family, tenant, maintenance window and patch ring where those attributes materially affect execution.
- Keep the `canary` group small and representative of the wider fleet.

AWX credentials should supply the machine credential and privilege-escalation secret at runtime.

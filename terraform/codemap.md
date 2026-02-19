# my_homelab/terraform/

## Responsibility
Infrastructure-as-Code (IaC) for provisioning the base virtual private server (VPS) on Hetzner Cloud. This directory defines the core compute and security assets (SSH keys) required for the homelab deployment.

## Design
- **Provider**: `hcloud` (Hetzner Cloud) version `~> 1.45`.
- **Resource Types**:
  - `hcloud_ssh_key`: Manages SSH public keys for server access.
  - `hcloud_server`: Provisions the virtual machine instance (defaulting to Debian 12).
- **Security Context**:
  - Uses SSH keys for authentication instead of passwords.
  - Public network enabled for both IPv4 and IPv6.
  - Sensitive token management via `hcloud_token` variable.

## Flow
1. **Input**: `variables.tf` defines the required `hcloud_token` (sensitive) and optional `location`/`server_type`.
2. **Provider Initialization**: `providers.tf` configures the Hetzner Cloud provider using the token.
3. **Resource Provisioning**: `main.tf` creates the SSH key from a local file and attaches it to a new server instance.
4. **Output**: `outputs.tf` exposes the server's public IPv4 address and status.

## Integration
- **Upstream**: Depends on local SSH public key file (`~/.ssh/id_ed25519.pub`).
- **Downstream**: The resulting `server_ip` is used for subsequent configuration (e.g., Ansible, manual setup).
- **External**: Interfaces with Hetzner Cloud API.

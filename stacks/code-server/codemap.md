# Code-Server Stack

## Responsibility
VS Code in the browser for remote development.

## Design
- **code-server**: LinuxServer.io implementation of VS Code.

## Security Analysis
- **Volumes**:
  - `/opt/infrastructure/stacks`: Direct access to the host's stack configurations.
  - `./config`: Application configuration and settings.
- **Environment**:
  - `CODE_PASSWORD`: Access password for the web UI.
  - `PUID/PGID=0`: Running as root (High privilege).
- **Security Features**:
  - `authentik-auth@docker` middleware: Protected by Authentik SSO.
- **Ports**:
  - Internal port 8443 exposed via Traefik.

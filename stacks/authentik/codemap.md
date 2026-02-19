# Authentik Stack

## Responsibility
Identity provider and authentication service (GoAuthentik) for the homelab.

## Design
- **postgresql**: Database for authentik.
- **redis**: Cache and task queue.
- **server**: Core authentik server.
- **worker**: Background task processor.

## Security Analysis
- **Volumes**:
  - `./database`: Sensitive database files.
  - `./certs`: TLS certificates.
  - `/var/run/docker.sock`: Mounted in worker (High privilege).
- **Environment**:
  - `POSTGRES_PASSWORD`: Sensitive DB password.
  - `AUTHENTIK_SECRET_KEY`: Critical secret key.
  - `AUTHENTIK_INSECURE`: Set to "true" (Likely behind proxy).
- **Networks**:
  - `proxy-network`: External network for Traefik integration.
- **Integration**:
  - Traefik labels define OIDC/ForwardAuth endpoints used by other services.

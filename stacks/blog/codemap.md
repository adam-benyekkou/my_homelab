# Blog Stack

## Responsibility
WordPress blog hosting with MariaDB backend.

## Design
- **wordpress**: CMS application.
- **wordpress_db**: MariaDB database.

## Security Analysis
- **Volumes**:
  - `wordpress_data`: WordPress application files and uploads.
  - `wordpress_db_data`: Database files.
- **Environment**:
  - `WORDPRESS_DB_PASSWORD`: Sensitive database password.
  - `MYSQL_ROOT_PASSWORD`: Sensitive root password.
- **Networks**:
  - `wordpress_internal`: Isolated network for app-db communication.
  - `proxy-network`: External network for Traefik access.
- **Security Features**:
  - `wp-secure-headers` middleware for HTTPS protocol forwarding.
  - Redirects from WWW to non-WWW for consistency.

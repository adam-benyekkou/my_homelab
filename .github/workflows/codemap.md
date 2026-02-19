# .github/workflows/ Codemap

## Responsibility
Manages the CI/CD pipelines for the `my-homelab` repository, including linting, security scanning, and quality checks for Infrastructure as Code (IaC) and automation scripts.

## Current CI/CD Pipelines

### 1. IaC Linting (`lint-iac.yml`)
- **Responsibility**: Ensures Ansible and Terraform code follow best practices and formatting standards.
- **Tools**: `ansible-lint`, `terraform fmt`.
- **Triggers**: Push and Pull Requests to `main`/`master`.

### 2. Script Linting (`lint-scripts.yml`)
- **Responsibility**: Validates shell scripts for common errors and stylistic issues.
- **Tools**: `shellcheck`.
- **Triggers**: Changes to `scripts/**` via Push or Pull Requests.

### 3. Security Scan (`security.yml`)
- **Responsibility**: Basic security hygiene.
- **Tools**: `gitleaks` (Secret detection), `dependency-review-action` (PR-only dependency check).
- **Triggers**: Push and Pull Requests.

### 4. Extended Security Scan (`security-extended.yml`)
- **Responsibility**: In-depth security analysis for IaC and configurations.
- **Tools**:
    - **Trivy**: Scans IaC configurations (Terraform, Docker) for vulnerabilities.
    - **Checkov**: Policy-based security scanning for multi-framework (Terraform, Ansible, Docker).
- **Triggers**: Push/PR to `main`/`master` and a weekly schedule.

## Security Integration Status

- **Trivy**: Successfully integrated in `security-extended.yml`. It is configured to fail the build on `CRITICAL,HIGH` vulnerabilities in IaC configurations.
- **Checkov**: Successfully integrated in `security-extended.yml`. It covers Terraform, Ansible, Dockerfile, and Docker Compose. Currently set to `soft_fail: true` (reporting only).

## Recommendations
- **Checkov Hardening**: Consider switching `soft_fail: false` for `Checkov` once existing violations are addressed to prevent new security regressions.
- **Docker Image Scanning**: If this repository builds Docker images, integrate Trivy `image` scanning in a build/publish workflow.
- **Unified Security Job**: If performance allows, consider merging `security.yml` and `security-extended.yml` into a single, comprehensive security pipeline for consistency.

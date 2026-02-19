# My Security Implementation Walkthrough

Security is the core of this homelab. I've implemented a "defense in depth" strategy that covers everything from the code I write to the infrastructure I deploy. Here is a walkthrough of the security layers I've built.

## 1. Identity & Access (The Gatekeeper)

I don't expose services directly to the internet without protection.
*   **Authentik SSO**: I use Authentik as my central identity provider. Every service in my lab is protected behind this SSO layer.
*   **Multi-Factor Authentication**: I've integrated **Authy** for 2FA. Even if my credentials are leaked, nobody can access my dashboard or services without my physical device.
*   **HashiCorp Vault**: All my sensitive secrets are stored in Vault, meaning I don't have to hardcode passwords in my configuration files.

## 2. My CI/CD Security Pipeline

I built a tiered security pipeline in GitHub Actions that acts as a "Security Gate" for every change I make.

### Tier 1: The Immediate Gate (On Push)
Every time I push code, I run **Gitleaks**. It scans my entire Git history to make sure I haven't accidentally committed a secret or a private key. It’s my first line of defense against human error.

### Tier 2: The Deep Audit (On Pull Request)
Before I merge any change, I run a deep analysis:
*   **Checkov**: This tool audits my Terraform and Ansible files against hundreds of security policies (like checking for open ports or unencrypted disks).
*   **Trivy**: I use Trivy to scan my Infrastructure as Code (IaC) and my Docker Compose files for misconfigurations.
*   **Dependency Review**: I scan for vulnerabilities in the libraries and Docker images I use. If a package has a known CVE, the pipeline blocks the merge.

### Tier 3: The Continuous Audit (Weekly)
Security isn't "set and forget." Every Sunday, I trigger a full repo audit and generate a **Software Bill of Materials (SBOM)** using Syft. This gives me a full inventory of everything running in my lab and catches new vulnerabilities discovered in old code.

## 3. Infrastructure Hardening

On the VPS itself, I've implemented several hardening measures via Ansible:
*   **SSH Lockdown**: I've disabled password authentication and restricted SSH access.
*   **Firewalling**: I use UFW and Fail2ban to automatically block malicious actors.
*   **Unattended Upgrades**: The system is configured to automatically install security patches every day, so I'm never running outdated, vulnerable software.

This walkthrough represents my commitment to building a professional-grade, secure DevOps environment.

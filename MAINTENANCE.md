# Homelab Maintenance & Log Management Strategy

This document outlines the strategy for managing disk space and logs in the CavyDev Homelab.

## 1. Log Management Policy

### Docker Logs (Prevention)
All Docker containers are governed by a global logging policy defined in `/etc/docker/daemon.json`. 
- **Driver**: `json-file` (compatible with Promtail/Loki)
- **Max Size**: 10MB per file
- **Max Files**: 3
- **Total Cap**: ~30MB per container.

### System Logs (Journald)
The system journal (`journalctl`) is restricted to a rolling window to prevent `/var/log` saturation.
- **Retention**: 7 days of historical logs.
- **Cleanup**: Automated via weekly cron job.

## 2. Automated Maintenance Jobs

The following tasks are automated via the `maintenance` Ansible role:

| Task | Frequency | Action |
| :--- | :--- | :--- |
| **Docker System Prune** | Weekly | Removes stopped containers, unused networks, and dangling images/volumes. |
| **APT Cleanup** | Weekly | Runs `autoremove` and `autoclean` to free up package cache. |
| **Log Truncation** | Weekly | Forcefully truncates Docker `.log` files to 0 bytes. |
| **Journal Cleanup** | Weekly | Vacuums `journald` logs older than 7 days. |
| **Restic Pruning** | Weekly | Prunes old backups according to retention policy (7D/4W/6M). |
| **Restic Verification**| Monthly | Performs an integrity check on the remote backup repository. |

## 3. Best Practices for New Services

When adding new stacks to the homelab:

1. **Avoid Volume Bloat**: Ensure persistent data is stored in dedicated volumes, but logs are kept in the container standard output (stdout/stderr) so the global Docker rotation can handle them.
2. **Loki for Persistence**: If you need to keep logs for longer than 7 days, rely on the **Observability Stack (Loki)**. It is designed for efficient log storage and retrieval, unlike the local filesystem.
3. **Monitor Disk Usage**: Always check the Grafana dashboard for "Node Exporter" metrics. Disk usage over 80% should be investigated immediately.
4. **Quiet Noise**: For high-frequency services (like cAdvisor or heavy web scrapers), adjust the application logging level to `WARN` or `ERROR` in the environment variables.

---
*Maintained by the DevOps / SRE transition team.*

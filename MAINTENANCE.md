# My Homelab Maintenance Walkthrough

Running a homelab on a VPS means I have to be careful about resources—especially disk space. I've built a multi-layered maintenance system to keep the system lean and healthy without me having to touch it every day.

## How I Manage Logs

Logs used to be my biggest headache, taking up gigabytes of space. Here is how I solved it:

### 1. Stopping the Bloat at the Source (Docker)
I configured a global logging policy in `/etc/docker/daemon.json`. I set it to use the `json-file` driver but limited every single container to a maximum of 3 files of 10MB each. This means no matter how chatty a service like cAdvisor gets, it can never use more than 30MB of disk space for its logs.

### 2. Taming the System Journal
I noticed the system journal was also growing quite large. I set up a rolling window so I only keep the last 7 days of historical logs. If I haven't looked at a system error within a week, it’s probably not relevant anymore, and Loki likely has it anyway.

## My Automated Maintenance Pipeline

I wrote an Ansible role specifically for maintenance that handles the "dirty work" every week. Here is what my pipeline does:

*   **System Cleanup**: Every Sunday at 03:00, I run a `docker system prune`. This wipes out stopped containers, unused networks, and dangling images that I don't need anymore.
*   **Package Management**: Shortly after, I trigger an APT cleanup (`autoremove` and `autoclean`) to keep the OS package cache from bloating.
*   **The "Clean Slate" Truncation**: Even with Docker rotation, I implemented a weekly task that forcefully truncates all `.log` files to 0 bytes. It’s a bit aggressive, but it ensures I start every Monday with a fresh disk.
*   **Backup Hygiene**: My Restic backups are pruned weekly. I kept it simple: I keep 7 daily, 4 weekly, and 6 monthly snapshots. Every month, I also run a full integrity check to make sure my data is actually recoverable.

## My Rules for New Services

When I add a new stack, I follow these rules to make sure I don't break my maintenance flow:
1.  **Stdout Only**: I always log to stdout/stderr. If I need persistence, I ship it to Loki instead of writing to a file inside the container.
2.  **Monitor First**: I always check my Grafana dashboard after adding a service to see the impact on disk and CPU.
3.  **Log Levels**: If a service is too noisy, I immediately drop the log level to `WARN` in the environment variables.

This setup allows me to focus on building new things instead of cleaning up disk space.

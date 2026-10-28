# Monitoring

## Overview

Monitoring is used to track the health, performance, and availability of the homelab.

The current environment primarily relies on manual monitoring through Proxmox, Linux commands, and service logs.

Automated monitoring and alerts are planned but have not yet been implemented.

---

## Current Monitoring

### Proxmox

The Proxmox web interface is currently used to check:

- CPU usage
- Memory usage
- Storage usage
- Container status
- Virtual machine status
- Network activity
- Host availability

### ZFS

ZFS pool and dataset health can be checked through the Proxmox host.

View pool health:

`zpool status`

View pool capacity:

`zpool list`

View datasets:

`zfs list`

### Linux Containers

Container status is checked using:

`pct list`

Individual container status:

`pct status <CTID>`

### Virtual Machines

VM status is checked using:

`qm list`

Individual VM configuration:

`qm config <VMID>`

---

## Service Monitoring

### File Server

Current monitoring includes:

- CT100 status
- Samba service status
- Storage mount availability
- Network connectivity

Check Samba:

`systemctl status smbd`

### Immich

Current monitoring includes:

- CT101 status
- Docker status
- Immich container health
- Database availability
- Storage usage

Check Docker containers:

`docker ps`

Check Compose services:

`docker compose ps`

### GameServer1

Current monitoring includes:

- VM status
- Valheim startup logs
- SteamCMD server files
- UDP port availability
- External connection testing
- Playit.gg tunnel status

The Valheim server is not yet configured as an automatically managed systemd service.

### Joplin Server

Current monitoring includes:

- VM status
- Docker status
- Joplin Server logs
- PostgreSQL status
- Synchronization testing

Check Compose services:

`docker compose ps`

View recent logs:

`docker compose logs --tail=100`

### Pi-hole

Current monitoring includes:

- CT104 status
- Pi-hole FTL service status
- DNS resolution
- DNS blocking
- Local DNS records
- Upstream DNS availability

Check container status from Proxmox:

`pct status 104`

Check Pi-hole status inside the container:

`pihole status`

Check FTL service:

`systemctl status pihole-FTL`

Review service logs:

`journalctl -u pihole-FTL`

Pi-hole can also be monitored through its web administration dashboard.

Automated DNS availability alerts have not yet been configured.

---

### Media Server VM

The media-server VM adds several Docker Compose services: Jellyfin, qBittorrent, Radarr, Sonarr, Prowlarr, and Seerr. Current checks are manual (`qm status`, `docker compose ps`, service logs, media-mount availability, and a sample library/playback check).

No dedicated alerting or verified continuous checks for failed downloads, stalled import, full storage, or unavailable media playback were established.

### Off-Site Gateway

The independently deployed Debian gateway was tested for Tailscale/SSH reachability, reboot recovery, Ethernet reconnection, firewall/SSH hardening, and AC-loss recovery. A successful one-time test does not equal automated monitoring.

The gateway still needs reliable reachability alerts, update/drive-health reporting, and recovery/runbook checks; its off-site location and management identifiers are intentionally omitted from public docs.

---

## Storage Monitoring

Important conditions to check include:

- ZFS pool health
- Drive errors
- Available capacity
- Scrub status
- SMART information
- Dataset growth

Automated alerts have not yet been configured.

---

## Network Monitoring

Current network monitoring is primarily manual.

Checks include:

- Device connectivity
- IP address assignment
- Network interface status
- Service accessibility
- Internet connectivity

Network segmentation and dedicated monitoring tools have not yet been implemented.

### DNS Monitoring

Pi-hole now provides DNS filtering and local DNS records.

Manual checks include:

- Confirm Pi-hole is running
- Check DNS resolution from network clients
- Review blocked requests
- Confirm local hostnames resolve correctly
- Check upstream DNS availability

DNS configuration on Linux clients can be reviewed using:

`resolvectl status`

Pi-hole availability is important because network clients may depend on it for domain resolution.

---

## Logs

Logs are used to identify service failures and troubleshoot issues.

Common tools include:

`journalctl`

`systemctl status`

`docker compose logs`

`zpool status`

Important troubleshooting events are documented separately in:

`10 - Troubleshooting Logs.md`

---

## Current Limitations

- No sustained monitoring or alerts for the media VM, its Docker services, or import workflow.
- No configured off-site gateway health/SSH/Tailscale alerting.
- The ZFS pool was reported `DEGRADED` and needs proactive status/SMART alerting.
- No centralized monitoring dashboard
- No automated host alerts
- No automated disk failure notifications
- No automated application health alerts
- No dedicated uptime monitoring
- No established monitoring schedule

---

## Planned Improvements

- Alert on `DEGRADED`/`FAULTED` ZFS states and failed scrubs, with follow-up verification.
- Monitor media VM storage mounts, Docker containers, and end-user playback availability.
- Monitor off-site gateway reachability, Tailscale status, security updates, and storage health.
- Implement centralized monitoring
- Configure CPU and memory alerts
- Add ZFS health notifications
- Monitor SMART disk health
- Monitor container and VM availability
- Add application health checks
- Configure uptime monitoring
- Establish a regular maintenance schedule
- Monitor Pi-hole FTL availability
- Configure DNS service alerts
- Monitor DNS resolution failures
- Test DNS recovery during outages

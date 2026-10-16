# Immich

## Overview

Immich is used as the homelab's self-hosted photo and video management platform.

The service runs inside a Debian LXC container hosted by Proxmox. Immich and its supporting services are deployed using Docker Compose.

Persistent photo and video data is stored on the ZFS-backed `TCloud/immich` dataset.

Immich is currently used to back up and organize photos and videos for household users.

---

## Container Information

| Setting | Value |
| --- | --- |
| Container ID | CT101 |
| Hostname | `immich` |
| Type | LXC |
| Operating System | Debian |
| CPU | 4 cores |
| Memory | 8 GB |
| Networking | `vmbr0` |
| Start at Boot | Yes |
| Address | `x.x.x.x` - Sanitized |

The container is configured with:

`onboot: 1`

This allows the Immich container to start automatically when Proxmox boots.

---

## Architecture

Physical HDDs
|
|
ZFS RAIDZ2
|
|
TCloud/immich
|
|
Proxmox Host
|
|
LXC Mount Point
|
|
CT101 - Immich
|
|
Docker Compose
|
+--- Immich Server
|
+--- Immich Machine Learning
|
+--- PostgreSQL
|
+--- Redis
|
|
Web / Mobile Clients

---

## Application Stack

Immich is deployed using Docker Compose.

| Service | Purpose |
| --- | --- |
| Immich Server | Main application and API |
| Immich Machine Learning | Image recognition and search |
| PostgreSQL | Application database |
| Redis | Caching and supporting services |

Docker Compose manages the containers as a single application stack.

---

## Docker

Docker runs inside CT101.

### View Running Containers

`docker ps`

### View All Containers

`docker ps -a`

### Docker Compose Status

`docker compose ps`

### Start Immich

`docker compose up -d`

### Stop Immich

`docker compose down`

### Restart Immich

`docker compose restart`

Compose commands should be run from the directory containing the Immich Compose configuration.

---

## Docker Compose Configuration

### Compose Directory

TBD

### Primary Configuration

`docker-compose.yml`

Exact filename and location need to be verified.

The Compose configuration defines:

- Immich Server
- Machine Learning
- PostgreSQL
- Redis
- Networking
- Storage mounts
- Environment configuration
- Restart behavior

---

## Environment Configuration

Immich uses environment variables for application configuration.

Common settings are stored in an environment file such as:

`.env`

Exact location: TBD

Sensitive values are intentionally excluded from public documentation.

---

## Storage

### ZFS Dataset

Immich uses:

`TCloud/immich`

Host mount point:

`/TCloud/immich`

### Container Mount Point

TBD

The exact mount configuration can be verified from Proxmox using:

`pct config 101`

---

## Storage Flow

ZFS Pool
`TCloud`
|
|
`TCloud/immich`
|
|
Proxmox Host
|
|
LXC Mount Point
|
|
CT101
|
|
Docker Volume / Bind Mount
|
|
Immich

The ZFS-backed storage keeps photo and video files separate from the container's root filesystem.

---

## Database

Immich uses PostgreSQL for application data and metadata.

This includes:

- User information
- Photo and video metadata
- Albums
- Application information
- Search data

The database is important for restoring the full Immich installation.

Backing up only the original photos and videos would not provide a complete application recovery.

---

## Redis

Redis is used as a supporting service within the Immich Docker Compose stack.

It provides temporary application data and caching.

---

## Machine Learning

Immich includes machine-learning features for image recognition and search.

These features run through the dedicated machine-learning container.

---

## Client Access

### Web Interface

`http://x.x.x.x:<port>`

Exact configured port: TBD

### Mobile Application

The Immich mobile application connects to the self-hosted server for photo and video uploads.

The service has been used for household photo synchronization.

General remote access is not currently documented as fully implemented.

---

## Service Management

### Check LXC Status

`pct status 101`

### Enter Container

`pct enter 101`

### Check Docker

`systemctl status docker`

### Check Immich Containers

`docker compose ps`

### Restart Immich Stack

`docker compose restart`

### Start Immich Stack

`docker compose up -d`

### Stop Immich Stack

`docker compose down`

---

## Logs

### View Stack Logs

`docker compose logs`

### Follow Logs

`docker compose logs -f`

### View Recent Logs

`docker compose logs --tail=100`

### View Individual Service Logs

`docker compose logs <service-name>`

---

## Updating Immich

A general update process is:

1. Review Immich release notes.
2. Confirm important data is backed up.
3. Confirm the database is protected.
4. Confirm sufficient storage space is available.
5. Pull updated container images.
6. Restart the stack using Docker Compose.
7. Verify that all containers start successfully.
8. Verify the web interface.
9. Verify mobile access.
10. Confirm existing photos and albums are accessible.

The exact update procedure should be verified before performing major upgrades.

---

## Startup and Recovery

CT101 is configured to start automatically with Proxmox.

Expected startup sequence:

1. Proxmox boots.
2. ZFS pool becomes available.
3. CT101 starts automatically.
4. Docker starts inside CT101.
5. Immich Docker containers start.
6. PostgreSQL and Redis become available.
7. Immich Server becomes available.
8. Clients can reconnect.

---

## Troubleshooting

### Confirm CT101 Is Running

`pct status 101`

### Enter CT101

`pct enter 101`

### Check Network Address

`ip addr`

### Verify Storage

`df -h`

### Check Docker

`systemctl status docker`

### Check Containers

`docker ps`

### Check Compose Stack

`docker compose ps`

### Review Logs

`docker compose logs --tail=100`

### Verify ZFS Storage From Proxmox

`zfs list TCloud/immich`

### Verify Pool Health

`zpool status TCloud`

---

## Troubleshooting Flow

If Immich becomes unavailable:

1. Confirm the Proxmox host is operational.
2. Confirm CT101 is running.
3. Confirm CT101 has network connectivity.
4. Confirm ZFS storage is available.
5. Confirm Docker is running.
6. Check `docker compose ps`.
7. Identify stopped or unhealthy containers.
8. Review Docker Compose logs.
9. Check PostgreSQL availability.
10. Check Redis availability.
11. Check filesystem permissions.
12. Verify Immich from another client.

---

## Data Protection

Important persistent data includes:

- Original photos
- Original videos
- PostgreSQL database
- User-uploaded content
- Application configuration
- Docker Compose configuration
- Environment configuration

Docker containers can be recreated, but important persistent data and configuration need to be backed up.

---

## Security

Current security considerations include:

- Immich is hosted internally.
- Administrative access requires authentication.
- Exact internal IP addresses are excluded from public documentation.
- Passwords and application secrets are excluded.
- Docker environment secrets are not published.
- Proxmox management is not intentionally exposed to the Internet.

---

## Backup Considerations

A complete backup strategy should include:

- `TCloud/immich` persistent media
- PostgreSQL database
- Docker Compose configuration
- Environment configuration
- Container configuration
- Recovery documentation

ZFS RAIDZ2 protects against certain disk failures but does not replace a separate backup.

Backup procedures will be documented in:

`09 - Backup & Recovery.md`

---

## Current Limitations

- Off-system backups are not yet fully implemented.
- Automated ZFS snapshots are not yet implemented.
- Automated service monitoring is not yet implemented.
- Recovery procedures have not yet been fully tested.
- Database backup procedures need to be established.
- Exact Docker Compose paths need to be documented.
- Exact container storage mappings need to be verified.

---

## Planned Improvements

- Implement automated ZFS snapshots
- Establish PostgreSQL database backups
- Establish off-system media backups
- Implement service monitoring
- Configure health alerts
- Implement secure remote access
- Test complete Immich restoration
- Document the verified update procedure
- Monitor storage growth
- Monitor CPU and memory usage

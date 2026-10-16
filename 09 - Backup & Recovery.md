# Backup & Recovery

## Overview

Backup and recovery procedures are being developed to protect important homelab data and services.

The homelab currently uses ZFS RAIDZ2 to provide redundancy against certain disk failures.

RAIDZ2 is not considered a replacement for backups.

A separate backup system is needed to protect against accidental deletion, corruption, hardware failure, and other data loss.

---

## Current Configuration

### ZFS Redundancy

**Pool:** `TCloud`

**RAID Level:** RAIDZ2

The storage pool uses dual-parity redundancy.

This allows the pool to tolerate up to two drive failures within its RAIDZ2 configuration.

### Current Backup Status

- ZFS RAIDZ2 redundancy implemented
- Automated ZFS snapshots not yet implemented
- Off-system backups not yet fully implemented
- Proxmox guest backup procedures still being developed
- Full recovery testing not yet completed

---

## Data Requiring Backups

### File Server

Important data:

- `TCloud/data`
- Samba configuration
- User and permission configuration
- CT100 configuration

### Immich

Important data:

- `TCloud/immich`
- PostgreSQL database
- Docker Compose configuration
- Environment configuration
- CT101 configuration

Both original media and database information are needed for a complete restoration.

### GameServer1

Important data:

- Valheim world saves
- Dedicated server configuration
- Startup script
- VM configuration

Persistent Valheim data is stored under:

`/home/valheim/data`

The server installation is stored separately under:

`/home/valheim/server`

### Joplin Server

Important data:

- PostgreSQL database
- Docker Compose configuration
- Environment configuration
- User data and attachments
- VM configuration

Joplin application configuration is stored under:

`/opt/joplin`

Separate PostgreSQL backups still need to be established.

### Proxmox

Important configuration includes:

- Host network configuration
- Storage configuration
- Container configuration
- Virtual machine configuration
- Backup scheduling
- Guest startup configuration

### Pi-hole

Important data:

- Pi-hole settings
- Local DNS records
- Blocklists
- Allowlist and denylist settings
- Upstream DNS configuration
- CT104 configuration

Pi-hole configuration backups have not yet been automated.

Its configuration should be preserved so the home's DNS service can be restored following a container or host failure.

---

## ZFS Snapshots

ZFS supports point-in-time snapshots.

Snapshots can help recover data following accidental modification or deletion.

### Current Status

Automated snapshots have not yet been implemented.

### Planned Configuration

- Scheduled snapshots
- Snapshot retention
- Automatic cleanup
- Recovery testing

Snapshots stored on the same pool do not provide protection against total pool loss.

---

## Proxmox Backups

Proxmox supports backups of containers and virtual machines.

### Current Status

A verified automated backup schedule has not yet been established.

Planned backups include:

- CT100 - File Server
- CT101 - Immich
- GameServer1 VM
- Joplin Server VM
- CT104 - Pi-hole

Persistent bind-mounted datasets and application databases require separate consideration because a guest backup may not include every external storage location.

---

## Off-System Backups

### Current Status

Not yet fully implemented.

Future backups should be stored separately from the primary ZFS pool.

Potential options include:

- External storage
- Another physical system
- Off-site storage

The final backup destination has not yet been selected.

---

## Database Backups

### Immich

Immich uses PostgreSQL.

The database needs to be backed up alongside persistent photo and video storage.

### Joplin

Joplin Server also uses PostgreSQL.

A separate database backup process still needs to be configured.

Database consistency and recovery testing are important because copying database files while they are running may not create a usable backup.

---

## Recovery Procedures

### File Server

Recovery would require:

1. Restore file data.
2. Restore or recreate CT100.
3. Restore Samba configuration.
4. Restore users and permissions.
5. Verify SMB access.

### Immich

Recovery would require:

1. Restore persistent media.
2. Restore PostgreSQL.
3. Restore application configuration.
4. Recreate the Docker stack.
5. Verify user accounts, albums, and media.

### GameServer1

Recovery would require:

1. Restore or recreate the Debian VM.
2. Install SteamCMD and Valheim Dedicated Server.
3. Restore world data.
4. Restore the startup configuration.
5. Test local and external connections.

### Joplin

Recovery would require:

1. Restore or recreate the Debian VM.
2. Install Docker and Docker Compose.
3. Restore PostgreSQL.
4. Restore Joplin configuration.
5. Verify synchronization from clients.

These procedures are planned and have not yet been fully tested.

### Pi-hole

Recovery would require:

1. Restore or recreate CT104.
2. Install Pi-hole if needed.
3. Restore Pi-hole configuration.
4. Restore local DNS records.
5. Verify upstream DNS.
6. Verify Eero Custom DNS settings.
7. Test DNS resolution from network clients.

These procedures have not yet been fully tested.

---

## Current Limitations

- No verified off-system backup process
- No automated ZFS snapshot schedule
- No confirmed automatic Proxmox backup schedule
- No tested complete Immich recovery
- No tested Valheim recovery
- No tested Joplin database recovery
- No complete disaster recovery test

---

## Planned Improvements

- Implement automated ZFS snapshots
- Establish Proxmox guest backups
- Configure PostgreSQL backups
- Back up Valheim world data
- Establish separate backup storage
- Document retention policies
- Test file restoration
- Test application restoration
- Test full VM and container recovery
- Establish a regular backup verification schedule
- Back up Pi-hole configuration
- Test restoring local DNS records
- Test DNS recovery following a Proxmox failure

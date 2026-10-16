# ZFS Storage

## Overview

ZFS is used as the primary storage system for persistent homelab data.

The storage pool is hosted directly by the Proxmox server and is separate from the SSD used to boot the Proxmox operating system.

ZFS provides:

- Storage pooling
- RAID-based redundancy
- Data integrity checking
- Dataset-based organization
- Snapshot capabilities
- Storage for virtualized services

---

## Storage Hardware

The Dell PowerEdge R510 currently contains:

| Device Type | Quantity | Purpose |
| --- | --- | --- |
| 1 TB 7200 RPM HDD | 11 | ZFS Storage |
| 50 GB SSD | 1 | Proxmox boot device |

The HDDs are connected through an LSI 2008 HBA storage controller.

The boot SSD is kept separate from the main data pool.

---

## ZFS Pool

**Pool Name:** `TCloud`

**RAID Level:** RAIDZ2

**Drive Type:** 1 TB 7200 RPM HDDs

### RAIDZ2

RAIDZ2 provides dual-parity protection.

This allows the storage pool to tolerate the failure of up to two drives without immediately losing the pool.

Redundancy is intended to improve availability and fault tolerance but is not treated as a replacement for backups.

---

## Pool Layout

Current logical structure:

TCloud
|
+--- data
|
+--- immich
|
+--- subvol-100-disk-0
|
+--- subvol-101-disk-0
|
+--- VM Disks
|
+--- Additional datasets as services are added

### Current Datasets

| Dataset | Purpose |
| --- | --- |
| `TCloud/data` | General network file storage |
| `TCloud/immich` | Persistent Immich photo/video data |
| `TCloud/subvol-100-disk-0` | CT100 root filesystem |
| `TCloud/subvol-101-disk-0` | CT101 root filesystem |

Additional datasets and virtual disks may be created as new services are deployed.

GameServer1 currently uses a 60 GB virtual disk on `TCloud`.

The exact Joplin VM disk allocation and storage mapping still need verification.

---

## Dataset Design

Separate datasets are used for major services rather than storing all application data in a single location.

This provides several advantages:

- Easier organization
- Independent dataset settings
- Easier snapshot management
- Easier backup planning
- Clear separation between services
- Easier identification of storage usage

For example:

`TCloud/data`

is used by the file server, while:

`TCloud/immich`

is used for Immich application data.

---

## Proxmox Integration

The ZFS pool is managed by the Proxmox host.

Persistent datasets can be mounted into Linux containers that require access to the stored data.

Conceptually:

Physical HDDs
|
|
ZFS RAIDZ2
|
|
TCloud
|
+--- TCloud/data
|       |
|       +--- File Server Container
|
+--- TCloud/immich
|       |
|       +--- Immich Container
|
+--- Proxmox Guest Storage
        |
        +--- Container Root Filesystems
        |
        +--- GameServer1 Virtual Disk
        |
        +--- Additional VM Storage

This keeps persistent application data on the ZFS storage pool rather than only inside the container's root filesystem.

---

## File Server Storage

The File Server container uses storage backed by:

`TCloud/data`

The dataset is mounted into the container and then made available to network clients through SMB.

Conceptually:

ZFS Dataset
`TCloud/data`
|
|
Proxmox Host
|
|
LXC Mount Point
|
|
File Server Container
|
|
SMB Share
|
|
Network Clients

Detailed file server configuration is documented in:

`05 - File Server.md`

---

## Immich Storage

Immich persistent storage is backed by:

`TCloud/immich`

This dataset is used to separate photo and video storage from other network file storage.

Detailed Immich configuration is documented in:

`06 - Immich.md`

---

## Virtual Machine Storage

### GameServer1

GameServer1 uses a 60 GB virtual disk stored on `TCloud`.

Valheim server files and persistent world data are stored inside the Debian VM.

Important paths:

`/home/valheim/server`

`/home/valheim/data`

The world data should be backed up separately from the Steam-managed server installation.

### Joplin Server

Joplin Server runs inside a dedicated Debian VM.

Docker Compose files are stored under:

`/opt/joplin`

PostgreSQL stores the application database.

The exact Proxmox storage allocation and Docker database volume location still need to be verified.

---

## Storage Health

ZFS pool health should be checked regularly.

Should plan an established maintenance schedule for regular checkups.

### View Pool Status

`zpool status`

This displays:

- Overall pool health
- Disk status
- Read/write/checksum errors
- RAIDZ state
- Scrub progress
- Failed or degraded devices

### View Pools

`zpool list`

This provides a summary of:

- Pool size
- Allocated capacity
- Free capacity
- Fragmentation
- Health status

### View Datasets

`zfs list`

This displays datasets, used capacity, available capacity, and mount points.

---

## ZFS Scrubs

ZFS scrubs verify stored data and detect integrity problems.

### Start a Scrub

`zpool scrub TCloud`

### Check Scrub Status

`zpool status TCloud`

Scrubs should eventually be scheduled and monitored as part of routine storage maintenance.

### Current Scrub Schedule

TBD

---

## Dataset Properties

Dataset configuration can be reviewed using:

`zfs get all TCloud/data`

or:

`zfs get all TCloud/immich`

Important properties include:

- Compression
- Mount point
- Quotas
- Reservations
- Record size
- Atime
- Snapshot policies

### Current Dataset Properties

The last documented ZFS dataset configuration is:

| Dataset | Compression | Mount Point | Record Size | Atime | Quota | Reservation |
| --- | --- | --- | --- | --- | --- | --- |
| `TCloud/data` | `lz4` | `/TCloud/data` | `128K` | `on` | `none` | `none` |
| `TCloud/immich` | `lz4` | `/TCloud/immich` | `128K` | `on` | `none` | `none` |

Both datasets inherit `lz4` compression from the parent `TCloud` dataset.

Neither dataset currently has a documented quota or reservation configured.

The datasets use the default ZFS record size of `128K`, and access time updates (`atime`) are enabled.

---

## Snapshots

ZFS supports point-in-time snapshots of datasets.

Example structure:

`TCloud/data@snapshot-name`

Snapshots may be used to help recover accidentally modified or deleted data.

### Current Snapshot Configuration

Not yet implemented.

Future plans include defining:

- Snapshot frequency
- Snapshot retention
- Automated snapshot creation
- Snapshot cleanup
- Recovery procedures

Snapshots are not considered a replacement for separate backups.

---

## Capacity

### Raw Capacity

Approximately 11 TB of raw HDD capacity is documented before accounting for:

- RAIDZ2 parity
- ZFS metadata
- Filesystem overhead
- Reserved free space

### Current Usable Capacity

The last documented storage capacity was verified using `zpool list` and `zfs list`.

These values are from September 2026 and should be refreshed after the addition of new virtual machines.

#### Pool Capacity

| Pool | Raw Size | Allocated | Free | Capacity Used | Health |
| --- | --- | --- | --- | --- | --- |
| `TCloud` | 10.9 TB | 342 GB | 10.6 TB | 3% | `ONLINE` |

#### ZFS Dataset Capacity

| Dataset | Used | Available | Referenced |
| --- | --- | --- | --- |
| `TCloud` | 260 GB | 7.93 TB | 256 KB |
| `TCloud/data` | 194 GB | 7.93 TB | 194 GB |
| `TCloud/immich` | 59.2 GB | 7.93 TB | 59.2 GB |
| `TCloud/subvol-100-disk-0` | 692 MB | 99.3 GB | 692 MB |
| `TCloud/subvol-101-disk-0` | 6.51 GB | 43.5 GB | 6.51 GB |

The pool previously reported approximately **7.93 TB of usable ZFS capacity available** after RAIDZ2 redundancy and ZFS overhead.

The main service datasets share the available capacity of the parent pool unless individual quotas or reservations are configured.

---

## Failure Handling

RAIDZ2 allows the pool to continue operating following certain disk failures, but degraded pool conditions should be addressed as soon as possible.

A typical recovery process would include:

1. Identify the failed disk using `zpool status`.
2. Confirm the physical drive associated with the failed device.
3. Replace the failed drive.
4. Add or replace the disk in the ZFS pool as required.
5. Allow ZFS to resilver the replacement disk.
6. Monitor the resilver operation.
7. Verify that the pool returns to an `ONLINE` state.

A complete tested disk replacement procedure will be documented after the process has been performed in the environment.

---

## Monitoring

Important storage conditions to monitor include:

- Pool health
- Drive failures
- Checksum errors
- Available capacity
- Scrub results
- Drive SMART health
- Excessive storage usage

Automated monitoring and alerting have not yet been fully implemented.

Detailed monitoring documentation will be maintained in:

`08 - Monitoring.md`

---

## Backup Considerations

ZFS redundancy protects against certain disk failures but does not protect against every form of data loss.

Potential risks include:

- Accidental deletion
- Application corruption
- Multiple simultaneous disk failures
- Hardware failure
- Theft
- Fire
- Filesystem administration mistakes

Important data therefore requires backups stored separately from the primary ZFS pool.

Backup procedures will be documented in:

`09 - Backup & Recovery.md`

---

## Current Limitations

- Automated ZFS snapshots are not yet fully documented or implemented.
- Automated pool health alerting is not yet implemented.
- Scrub scheduling needs to be verified and documented.
- Off-system backups are still being developed.
- Disk replacement and recovery procedures have not yet been tested and documented.
- Current pool and dataset usage needs to be updated following the new VM deployments.

---

## Planned Improvements

- Configure scheduled ZFS scrubs
- Implement automated snapshots
- Establish snapshot retention policies
- Monitor pool health automatically
- Monitor individual disk SMART data
- Configure storage alerts
- Establish off-system backups
- Document disk replacement procedures
- Perform and document recovery testing
- Monitor capacity growth

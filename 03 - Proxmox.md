# Proxmox

## Overview

Proxmox VE is the primary virtualization platform used by the homelab.

It is installed directly on the Dell PowerEdge R510 and is responsible for:

- Hosting Linux containers (LXC)
- Hosting virtual machines
- Providing virtual networking
- Connecting guest systems to ZFS-backed storage
- Managing compute and memory resources
- Providing centralized administration through the Proxmox web interface

---

## Host Information

| Setting | Value |
| --- | --- |
| Platform | Proxmox VE |
| Physical Host | Dell PowerEdge R510 |
| Hostname | `pve` |
| Management Address | `x.x.x.x` - Sanitized |
| Address Assignment | Static |
| CPU | 2x Intel Xeon X5670 |
| CPU Resources | 12 cores / 24 threads |
| Memory | 64 GB DDR3 |
| Boot Device | 50 GB SSD |
| Storage Controller | LSI 2008 HBA |

### Proxmox Version

**Proxmox VE:** 9.1.7

**pve-manager:** 9.1.7

**Running Kernel:** 6.17.13-2-pve

These are the last documented version details.

---

## Management

### Web Interface

The Proxmox web interface is the primary method used to manage the server.

Public documentation address:

`https://x.x.x.x:8006`

The actual internal management address is intentionally excluded from public documentation.

The web interface is used for:

- Starting and stopping guests
- Creating VMs and containers
- Monitoring resource usage
- Managing storage
- Viewing logs
- Accessing guest consoles
- Configuring networking
- Managing backups

### Command Line

The Proxmox host is also administered through the Linux command line.

Administrative tasks can be performed through:

- Proxmox web shell
- Local console
- SSH

The command line is commonly used for troubleshooting, checking system status, managing containers, reviewing logs, and making configuration changes.

---

## Host Networking

The Proxmox host connects to the local network through a Linux bridge.

### Network Bridge

`vmbr0` provides network connectivity between the physical network interface and virtualized guest systems.

Conceptually:

Physical NIC
|
|
vmbr0
|
+--- Proxmox Host
|
+--- LXC Containers
|
+--- Virtual Machines

The Proxmox management interface currently uses a static address.

Exact internal addressing is documented privately and sanitized in public documentation.

Detailed network architecture is documented in:

`02 - Network Architecture.md`

---

## Physical Network Interfaces

The server contains multiple physical network interfaces.

### Current Configuration

| Interface | Current Role |
| --- | --- |
| Primary NIC | Connected to `vmbr0` |
| Secondary NIC | Currently unused |

Additional interfaces may be used in the future for network segmentation, management traffic, storage traffic, or redundancy.

---

## Virtualization

Proxmox supports both Linux containers and full virtual machines.

### Linux Containers - LXC

LXC containers are currently used for services that do not require an entire virtualized operating system.

Advantages for the current environment include:

- Lower resource usage
- Fast startup
- Easy resource allocation
- Isolation between services
- Centralized management through Proxmox

### Virtual Machines

Virtual machines are used for workloads requiring:

- A complete virtualized operating system
- Greater isolation
- Different operating systems
- Testing environments
- Workloads not appropriate for LXC containers

GameServer1, Joplin Server, and the media server have been deployed as dedicated Debian virtual machines.

---

## Current Guests

### CT100 - File Server

**Type:** LXC Container

**Purpose:** SMB network file storage

**Operating System:** Debian

**Resources:**

| Resource | Allocation |
| --- | --- |
| CPU | 2 cores |
| Memory | 2 GB |
| Networking | `vmbr0` |
| Storage | ZFS-backed dataset mounted into container |

Detailed configuration:

`05 - File Server.md`

---

### CT101 - Immich

**Type:** LXC Container

**Purpose:** Self-hosted photo and video management

**Operating System:** Debian

**Resources:**

| Resource | Allocation |
| --- | --- |
| CPU | 4 cores |
| Memory | 8 GB |
| Networking | `vmbr0` |
| Storage | ZFS-backed Immich dataset |

Immich application services are hosted using Docker inside the container.

Detailed configuration:

`06 - Immich.md`

---

### GameServer1 - Valheim

**Type:** Virtual Machine

**Purpose:** Dedicated multiplayer Valheim server

**Operating System:** Debian 13

**Resources:**

| Resource | Allocation |
| --- | --- |
| CPU | 4 vCPU |
| Memory | 8 GB |
| Storage | 60 GB |
| Networking | `vmbr0` |
| IP Address | `x.x.x.x` |
| Address Assignment | DHCP Reservation |
| QEMU Guest Agent | Enabled |

GameServer1 uses SteamCMD to install and manage the Valheim Dedicated Server.

The server is currently started manually and still requires additional work for automated startup.

Detailed configuration:

`12 - GameServer1.md`

---

### Joplin Server

**Type:** Virtual Machine

**Purpose:** Self-hosted note synchronization

**Operating System:** Debian

**Resources:**

| Resource | Allocation |
| --- | --- |
| VM ID | 103 |
| CPU | 2 cores |
| Memory | 2 GB |
| Storage | 32 GB |
| Networking | `vmbr0` |
| IP Address | `x.x.x.x` |
| Start at Boot | Yes |

Joplin Server and PostgreSQL are hosted using Docker Compose inside the VM.

The application is installed under:

`/opt/joplin`

Laptop and desktop synchronization has been configured.

Detailed configuration:

`07 - Joplin.md`

---

### CT104 - Pi-hole

**Type:** LXC Container

**Purpose:** Network-wide DNS filtering and local DNS records

**Operating System:** Debian 13

**Resources:**

| Resource | Allocation |
| --- | --- |
| CPU | 1 core |
| Memory | 1024 MB |
| Swap | 512 MB |
| Storage | 8 GB |
| Storage Pool | TCloud |
| Networking | `vmbr0` |
| Address Assignment | DHCP |
| Unprivileged | Yes |
| Start at Boot | Yes |

Pi-hole is installed directly inside the Debian container.

The Eero Pro 7 uses Pi-hole through its Custom DNS configuration.

Detailed configuration:

`13 - Pi-Hole.md`

---

### Media Server VM — Media Services

**Type:** Virtual Machine  
**Purpose:** Household media playback and download/organization stack  
**Operating System:** Debian 13  
**Networking:** `vmbr0` with an address reserved on the local network

The media VM runs Docker Compose with Jellyfin, qBittorrent, Radarr, Sonarr, Prowlarr, and Seerr. A ZFS-backed media dataset is presented from the Proxmox host into the VM through **VirtioFS** and mounted persistently at `/mnt/media`. Application containers use a consistent `/data` view of that storage.

Storage access, a test download, media import, and Jellyfin playback were verified. The exact current CPU/memory allocation has not been independently re-audited for this publication; do not infer it from another VM.

Full configuration and outstanding VPN/request-workflow validation: `14 - MediaServer1.md`.

---

## Independent Off-Site Gateway (Not a Proxmox Guest)

The off-site Debian/Tailscale gateway is a **separate physical computer**, not a Proxmox guest. It provides a verified private SSH management path but does not yet provide the planned WireGuard public game path. See `15 - Off-Site Gateway.md`.

---

## Resource Allocation

Guest resources are allocated based on workload requirements rather than assigning the full physical resources of the server to individual services.

This allows multiple workloads to share the same physical server while maintaining separation between services.

Current server resources available:

- 12 physical CPU cores
- 24 CPU threads
- 64 GB RAM

Resource allocation may be adjusted as additional services are added or usage patterns change.

---

## Storage Integration

Proxmox uses both local boot storage and ZFS-backed storage.

### Boot Storage

The Proxmox operating system is installed on a dedicated SSD.

### Data Storage

Service data is stored separately on the ZFS storage pool.

This separation prevents application data from being dependent solely on the Proxmox boot drive.

ZFS datasets can be mounted into containers when services require access to persistent storage.

GameServer1 also uses a virtual disk stored on the `TCloud` pool.

Detailed storage documentation:

`04 - ZFS Storage.md`

---

## Container Administration

Common Proxmox container management tasks can be performed from the host.

### View Containers

`pct list`

### Start Container

`pct start <CTID>`

### Stop Container

`pct stop <CTID>`

### Reboot Container

`pct reboot <CTID>`

### Enter Container Shell

`pct enter <CTID>`

These commands are useful when the Proxmox web interface is unavailable or when troubleshooting directly from the host.

---

## Virtual Machine Administration

Virtual machines can be managed through the Proxmox web interface or command line.

### View Virtual Machines

`qm list`

### Start VM

`qm start <VMID>`

### Stop VM

`qm stop <VMID>`

### Reboot VM

`qm reboot <VMID>`

### View VM Configuration

`qm config <VMID>`

VM-specific documentation is maintained separately for GameServer1 and Joplin.

---

## Service Startup

Important services are configured to start automatically with the Proxmox host.

| Guest | Service | Start at Boot | Startup Order |
| --- | --- | --- | --- |
| CT100 | File Server | Yes | Default |
| CT101 | Immich | Yes | Default |
| CT104 | Pi-hole | Yes | Default |
| GameServer1 | Valheim | Not verified | TBD |
| VM103 | Joplin Server | Yes | TBD |

CT100, CT101, and CT104 have Start at Boot enabled.

VM103 also has Start at Boot enabled.

Joplin's Docker Compose services use `restart: unless-stopped`.

This allows the main services to start following a Proxmox reboot.

GameServer1 still needs a systemd service for reliable automatic Valheim startup.

Recovery following a complete Proxmox reboot has not yet been fully tested.

Pi-hole startup should be prioritized during recovery because other devices now depend on it for DNS resolution.

---

## Updates and Maintenance

Proxmox updates are installed periodically to maintain:

- Security
- Stability
- Hardware compatibility
- Proxmox package updates

Before major configuration changes or upgrades:

1. Review current system state.
2. Confirm important data is backed up.
3. Review available disk space.
4. Review release notes when performing major version upgrades.
5. Verify guest services after the maintenance is complete.

---

## Security

Current management practices include:

- Proxmox management interface remains internal to the home network
- Management interfaces are not intentionally exposed directly to the Internet
- Administrative access requires authentication
- Sensitive credentials are not included in public documentation
- Remote administration will use a secure VPN-based solution if implemented

---

## Backup and Recovery

Proxmox guest backup procedures are still being developed.

Planned documentation includes:

- Container backups
- Virtual machine backups
- Proxmox configuration backup
- ZFS data protection
- Off-system backup storage
- Recovery testing
- Valheim world backups
- Joplin PostgreSQL backups

Detailed procedures will be maintained in:

`09 - Backup & Recovery.md`

---

## Planned Improvements

- Implement reliable automated backups
- Configure UPS integration
- Improve monitoring and alerting
- Implement secure remote administration
- Evaluate network segmentation
- Document guest startup order
- Establish regular maintenance procedures
- Test service recovery following host failure
- Configure automatic startup for GameServer1
- Test Joplin automatic recovery following a Proxmox reboot
- Test Pi-hole automatic recovery following a Proxmox reboot
- Review guest startup order for DNS-dependent services

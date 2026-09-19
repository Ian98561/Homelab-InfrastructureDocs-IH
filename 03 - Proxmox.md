# Proxmox

## Overview

Proxmox VE is the primary virtualization platform used by the homelab.

It is installed directly on the Dell PowerEdge R510 and is responsible for:

- Hosting Linux containers (LXC)
- Hosting virtual machines
- Providing virtual networking
- Connectin guest systems to ZFS-backed storage
- Managing compute and memory resources
- Providing centralized administration through the Proxmox web interface

---

## Host Information

| Setting | Value|
| --- | --- |
| Platform | Proxmox VE |
| Physical Host | Dell PowerEdge R510 |
| Hostname | `pve' |
| Management Address | `192.168.x.x` - Sanitzed |
| Address Assignment | Static |
| CPU | 2x Intel Xeon X5670 |
| CPU Resources | 12 cores / 24 threads |
| Memory | 64 GB DDR3 |
| Boot Device | 50 GB SSD |
| Storage Controller | LSI 2008 HSA |

### Proxmox Version

**Proxmox VE:** 9.1.7
**pve-manager:** 9.1.7
**Running Kernel:** 6.17.13-2-pve

---

## Management

### Web Interface

The Proxmox web interface is the primary method used to manage the server.

Public documentation address:

`https://192.168.x.x:8006`

The actual internal management address is intentionally excluded from public documenation.

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
- Local consoles
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
+---  Virtual Machines

The Proxmox management interface currntely uses a static address.

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

Additional interfaces may be used in the future for network segmentation, management traffic, storage traffic, or redundance.

---

## Virtualization

Proxmox supports both Linux containers and full virtual machines

### Linux Containers - LXC

LXC containers are currently used for services that do not require an entire virtualized operating system.

Advantages for the current environment include:

- Lower resource usage
- Fast startup
- Easy resource allocation
- Isolation between services
- Centralized management through Proxmox

### Virtual Machines

Virtual machines are available for workloads requiring:

- A complete virtualized operating system'
- Greater isolation
- Different operating systems
- Testing environments
- Workloads not appropriate for LXC containerss

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

The Proxmox operation system is installed on a dedicated SSD.

### Data Storage

Service data is stored separately on the ZFS storage pool.

This serparation prevents application data from being dependent solely on the Proxmox boot drive.

ZFS datasets can be mounted into containers when services require access to persistent storage.

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

`pct enter <PCID>`

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

VM-specific documentation will be added as virtual machines are deployed.

---

## Service Startup

Critical services are configured to start automatically with the Proxmox host.

| Guest | Service | Start at Boot | Startup Order |
| --- | --- | --- | --- |
| CT100 | File Server | Yes | Default |
| CT101 | Immich | Yes | Default |

Both containers currently- have `onboot: 1` configured.

This allows the file server and Immich services to recover automatically after a Proxmox host reboot or power interruption without requiring each container to be started manually.

---

## Updates and Maintenance

Proxmox updates are installed periodically to maintain:

- Security
- Stability
- Hardware compatibillity
- Proxmox package updates

Before major configuration changes or uprgades:

1. Review current system state.
2. Confirm importtant data is backed up.
3. Review available disk space.
4. Review release notes when performing major version upgrades.
5. Verify guest services after the maintenance is complete.

---

## Security

Current management practices include:

- Proxmox management interface remains internal to the home network
- Management interfaces are not inentionally exposed directly to the Internet
- Adminisitrative access requries authentication
- Sensitive credentials are not included in public documentation
- Remote administration will use a secure VPN-based solution if implemented

---

## Backup and Recovery

Proxmox guest backup procedures are still being developed.

Planned documentation includes:

- Container backups
- Virtuial machines backups
- Proxmox configuration backup
- ZFS data protection
- Off-system backup storage
- Recovery testing

Detailed procedures will be maintained in:

`09 - Backup & Recovery.md`

---

## Planned Improvements

- Implement reliable automated backups
- Configure UPS integration
- Improve monitoring and alerting
- Implement secure remote administration
- Evaulate network segmentation
- Document guest startup order
- Establish regular maintenance procedures
- Test service recovery following host failure































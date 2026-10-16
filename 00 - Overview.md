# HomeLab Overview

## Purpose

This homelab is a personal environment used to develop hands-on experience with Linux administration, virtualization, networking, storage, self-hosted services, troubleshooting, and infrastructure management.

The lab is built on a dedicated Dell PowerEdge R510 Server from a past FB marketplace purchase and is running Proxmox VE. Virtual machines and Linux containers are used to separate services while ZFS provides centralized storage for applications and network file storage.

---

## Current Environment

### Virtualization
- Proxmox VE
- Linux Containers (LXC)
- Virtual Machines

### Storage
- ZFS storage pool
- RAIDZ2 disk configuration
- Separate datasets for different services

### Current Services
- SMB File Server
- Immich photo and video management
- Valheim dedicated Game Server (GameServer1)
- Joplin Server for self-hosted note synchronization
- Pi-hole for network-wide DNS filtering and local DNS

### In Progress
- Valheim external connection troubleshooting and automatic startup
- Joplin synchronization verification and secure remote access
- Joplin and PostgreSQL backups
- Homelab documentation updates

### Planned
- Network monitoring
- Improved backup and recovery procedures
- Additional self-hosted applications
- Network configuration
- Additional Linux testing environments

---

## High-Level Architecture

Internet / Router
        |
        |
Network PoE Switch (TP-Link)
        |
        |
        Proxmox Server
        |
        + - - - - ZFS Storage
        |
        + - - - - File Server Container (CT100)
        |
        + - - - - Immich Container (CT101)
        |
        + - - - - GameServer1 VM (Valheim)
        |
        + - - - - Joplin Server VM
        |
        + - - - - Pi-hole Container (CT104)
        |
        + - - - - Additional VMs / Containers

---

## Goals

The main goals of this project are to:

- Gain practical experience managing Linux servers.
- Learn virtualization and container administration.
- Develop networking and troubleshooting skills.
- Learn how to deploy and maintain self-hosted applications.
- Gain experience with ZFS storage and data management.
- Build reliable backup and recovery procedures.
- Document infrastructure changes and troubleshooting processes.
- Create a portfolio demonstrating practical IT and infrastructure experience.

---

## Documentation

Detailed documentation for the environment is separated into the following:

- Hardware
- Network Architecture
- Proxmox
- ZFS Storage
- File Server
- Immich
- Joplin
- Monitoring
- Backup & Recovery
- Troubleshooting Logs
- Future Improvements & Goals
- GameServer1
- Daily Logs

---

## Current Status

The homelab is actively being developed and expanded.

Core virtualization, ZFS storage, network file storage, and Immich services are currently operational.

GameServer1 has been deployed and the Valheim server has successfully started. External connections are still being troubleshooted due to ISP CGNAT, PlayFab connection issues, and latency differences between relay services.

Joplin Server has also been deployed in a dedicated Debian VM using Docker Compose and PostgreSQL. Laptop and desktop synchronization has been configured with end-to-end encryption. Some synchronization and note rendering problems still need further verification.

Pi-hole has been deployed in CT104 and is configured through the Eero Pro 7 for DNS filtering. Local DNS records have also been added to access Homelab services using hostnames. Ad blocking was successfully tested on the Linux desktop, and Joplin was updated to work with its local hostname. Additional device testing, backups, and DNS recovery testing are still needed.

Additional services, monitoring, backup procedures, and network improvements are being implemented as the environment grows.

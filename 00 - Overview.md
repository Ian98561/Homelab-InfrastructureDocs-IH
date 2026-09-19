# HomeLab Overiew

##Purpose

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

### In Progress
- Joplin self-hosted synchronization

### Planned
- Network monitoring
- Improved backup and recovery procedures
- Additional self-hosted applications
- Network conifguration

---

## High-Level Architecture

Internet / Router
        |
        |
Network PoE Switch (tp-link)
        |
        |
        Proxmox Server
        |
        + - - - - ZFS Storage
        |
        + - - - - File Server Container
        |
        + - - - - Immich Container
        |
        + - - - - Additional VMs / Containers
        
---

## Goals

The main goals of this project are to:

- Gain practical experience managaing Linux servers.
- Learn virtualization and container administration.
- Develop networking and troubleshooting skills.
- Learn how to deploy and maintain self-hosted applications.
- Gain experience with ZFS storage and data management.
- Build reliable backup and recovery procedures.
- Document infrastructure changes and troubleshooting processes.
- Create a portfolio demonstrating practical IT and infrastructure experience.

---

## Documentation

Detailed documentation for th environment is separated into the following:

- Hardware
- Network Architecture
- Proxmox
- ZFS Storage
- File Server
- Immich
~ Joplin
- Monitoring
- Backup & Recovery
- Troubleshooting Logs
- Future Improvements & Goals
+ Daily Logs

---

## Current Status

The homelab is actively being developed and expanded.

Core virtualization, ZFS storage, network file storage, and Immich services are currently operational. Additional services, monitoring, backup procedures, and network improvements are being        implemented as the environment grows.

        
        
        
        
        
        
        
        
        
        
        
        
        
        

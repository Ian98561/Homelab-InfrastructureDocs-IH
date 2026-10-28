# Hardware

## Overview

The homelab is centered around a Dell PowerEdge R510 rack server used as the primary virtualization and storage host.

Additional networking equipment connects the server and other devices on the local network.

---

## Primary Server

### Dell PowerEdge R510

**Role:** Primary Proxmox virtualization and storage server

| Component | Specification |
| --- | --- |
| Server | Dell PowerEdge R510 |
| CPU | 2x Intel Xeon CPU X5670 @ 2.93GHz |
| CPU Cores | 12 physical cores / 24 threads total |
| Memory | 64GB DDR3 RAM |
| Drive Bays | 12 |
| Installed Drives | 11x 1 TB HDD / 1x 50GB SSD Boot |
| Drive Speed | 7200 rpm |
| OS / Hypervisor | Proxmox VE |
| PCIe | LSI 2008 HBA Storage Controller |

### Purpose

The PowerEdge R510 acts as the central server for the homelab.

It currently provides:

- Virtual machine hosting
- Linux container hosting
- Centralized ZFS storage
- Network file storage
- Self-hosted applications
- Dedicated game server hosting
- Self-hosted note synchronization

The server was selected because its multiple drive bays, dual-CPU configuration, and large memory capacity provide enough resources to experiment with virtualization, storage, and multiple services on a single physical system.

---

## Storage Hardware

The server currently contains:

- 11x 1 TB 7200 RPM hard drives
- 1x 50GB SSD boot drive
- 11 TB raw storage capacity before redundancy

The drives are configured as part of a ZFS RAIDZ2 storage pool.

One previous pool member was intentionally removed during a hardware repurposing task. The pool was subsequently reported **DEGRADED**, without reported data errors at that time. The drive count alone does not establish restored redundancy; see `04 - ZFS Storage.md`.

The logical storage configuration, datasets, redundancy, and storage decisions are documented separately in:

`04 - ZFS Storage.md`

---

## Network Hardware

### TP-Link Switch

**Role:** Primary wired network switch for home network

**Ports:** 24 gigabit RJ45, 4 gigabit SFP slots

**PoE Support:** Yes, all RJ45 ports support PoE

**Exact Model:** TP-Link JetStream T1600G-28PS Smart PoE+ Switch

This switch provides wired connectivity between the homelab server and the rest of the network. PoE+ capability also provides room for future network-powered devices such as cameras or other infrastructure.

---

## Router / Gateway

**Device:** Eero Pro 7

**Role:**
- Internet gateway
- DHCP
- Local network routing

Detailed network configuration is documented in:

`02 - Network Architecture.md`

---

## Off-Site Gateway Hardware

**Device:** Lenovo ThinkCentre M70q Gen 3 mini PC  
**Role:** Independently deployed Linux gateway for private remote administration

| Component | Last Verified State |
| --- | --- |
| Processor | Intel Core i7-12700T |
| Memory | Approximately 24 GiB |
| OS Disk | 512 GB NVMe |
| Secondary Disk | 1 TB HDD, left unmounted for write use |
| Operating System | Debian 13 (minimal, CLI-only) |
| Management | SSH over a private Tailscale network |

The NVMe reported zero media errors and approximately 2% endurance consumed at the time of review. The secondary HDD showed extensive age/use indicators and **was not approved for critical backup storage**. Actual AC-power-loss reboot and remote reconnection testing later passed.

The gateway is located on a separate trusted network; its exact physical location, address, and identifying details are omitted from this public document.

Full configuration: `15 - Off-Site Gateway.md`.

---

## Physical Infrastructure

### Server Location

The primary server and networking equipment share a dedicated indoor location. Exact physical placement is omitted from public documentation.

### Power Protection

UPS: Not yet implemented / Needs to be fixed

- Surge Protection

### Cooling

Dedicated cooling beyond existing air circulation has not been implemented. Cooling, airflow, and temperature monitoring remain future improvements; exact room construction and layout are omitted publicly.

---

## Planned Hardware Improvements

Potential future hardware additions or improvements include:

- UPS / battery backup
- Network Configuration Establishment
- PoE Security Cameras (Reolink)
- Improved server monitoring
- Replacement or expansion storage as needed
- Cat6 drops instead of custom patch cables to each end device/room

Long-term plans are documented in:

`11 - Future Improvements & Goals.md`

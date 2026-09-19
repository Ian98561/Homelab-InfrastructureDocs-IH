# Network Architecture

## Overview

The homelab is connectewd to the primary home network and uses a centralized wired network design.

The Eero Pro 7 functions as the primary router,  DHCP server, and Internet gateway. Wired devices connect through a TP-Link JetStream managed PoE+ switch.

The Proxmox server connects directly to the dwitch and provides network connectivity to its virtual machines and Linux containers through a Proxmox network bridge.

---

## Physical Network Topology

Internet
|
|
Eero Pro 7
Router / Gateway / DHCP
|
|
TP-Link JetStream T1600G-28PS
Managed PoE+ Switch
|
+---------- Proxmox Server
|
+---------- Desktop / Workstations
|
+---------- Other Wired Devices
|
+---------- Future PoE Devices
            |
            +--- Security Cameras
            +--- Other Network Infrastructure

---

## Logical Network Topology

Internet
|
|
Eero Pro 7
|
|
TP-Link Network Switch
|
|
Proxmox Physical NIC
|
|
vmbr0
|
+---------- Proxmox Host
|
+---------- CT / VM File Server
|
+---------- CT / VM Immich -- Additional Services


---

## Network Addresing

> IP addresses shown in public documentation are sanitized.

### Local Network

| Device / Service | Address | Assignment |
| --- | --- | --- |
| Router / Gateway | `192.168.x.x` | Gateway |
| Proxmox Host | `192.168.x.x` | Static |
| File Server | `192.168.x.x` | DHCP Reservation |
| Immich | `192.168.x.x` | DHCP Reservation |
| Additional Services | `192.168.x.x` | As required |

### Addressing Method

The Eero Pro 7 currently provides DHCP services for the local network.

Important infrastructure devices use DHCP reservations or otherwise consistent addresses so that services can be reliably accessed without their addresses changing unexpectedly.

Exact internal addresses are excluded from the public version of this documentation.

---

## Proxmox Networking

### Network Bridge

**Bridge:** `vmbr0`

The Proxmox host uses a Linux network bridge named `vmbr0`

The bridge allows virtual machines and container hosted by Proxmox to communicate with the physical local network through the server's network inferface.

Conceptually:

Physical Network Interface
|
|
vmbr0
|
|
+--- Proxmox Host
|
+--- LXC Container
|
+---  Virtual Machine
|
+--- Additional Guest Systems

This allows hosted systems to operate as individual devices on the local network.

---

## Current Network Segmentation

Current Status:

- Primary home LAN
- Homelab currently operates on the same primary network
- No dedicated homelab VLAN documented at this time
- Additional segmentation is being considered for future implementation

Potential future segmentation may separate:

- Server infrastructure
- Client devices
- IoT devices
- Security cameras
- Management interfaces

---

## Internal Services

### File Server

The file server provides SMB network storage to authorized devices on the local network.

Traffic Flow:

Client Device
|
|
Network Switch
|
|
Proxmox Host
|
|
File Server Container
|
|
ZFS-backed Storage

---

### Immich

Immich provides self-hosted photo and video management.

Traffic Flow:

Client Device
|
|
Local Network
|
|
Proxmox Host
|
|
Immich Container
|
|
ZFS Storage

---

## Remote accesse

### Current Status

Remote access is not currently implemented.

Homelab services are currently accessed only from the local network.

Future remote access will most likely be provided through a VPN-based solution such as Tailscale rather than exposing management interfaces directly to the public Internet.

Potential solutions being evaluated include:

- Tailscale
- VPN-based remote access

---

## DNS

### Current Configuration

TBD

Future improvements may include:

- Local DNS records
- Hostname-based service access
- Pi-hole or another internal DNS service

This would reduce reliance on manually entering IP addresses when accessing internal services.

---

## Network Management

### Router

**Device:** Eero Pro 7

Responsibilities:

- Internet gateway
- DHCP
- Local network routing

### Switch

**Device:** TP-Link JetStream T1600G-28PS

Responsibilities:

- Wired devices connectivity
- PoE+ power delivery
- Future managed-network functionality

---

## Security Considerations

The network is being developed with the following principles:

- Avoid unnceessary public exposure of internal services
- Use consistent addressing for infrastructure
- Keep management interfaces internal whenever possible
- Separate services where practical
- Use authenticated access for network services
- Implement network segmentation as the environment grows

Sensitive information such as passwords, authentication tokens, public IP addresses, MAC addresses, and private keys is not stored in public documentation.

---

## Planned Network Improvements

- Establish a more structured network configuration
- Investigate VLAN-based segmentation
- Separate IoT and security camera devices
- Implement local DNS
- Improve remote access
- Add PoE security cameras
- Replace long/custom patch cable runs with permanent Cat6 drops
- Improve network monitoring
- Create a detailed network diagram 

























































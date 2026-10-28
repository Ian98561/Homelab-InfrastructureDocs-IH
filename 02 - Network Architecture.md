# Network Architecture

## Overview

The homelab is connected to the primary home network and uses a centralized wired network design.

The Eero Pro 7 functions as the primary router, DHCP server, and Internet gateway. Wired devices connect through a TP-Link JetStream managed PoE+ switch.

The Proxmox server connects directly to the switch and provides network connectivity to its virtual machines and Linux containers through a Proxmox network bridge.

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
+---------- CT100 - File Server
|
+---------- CT101 - Immich
|
+---------- VM - GameServer1
|
+---------- VM - Joplin Server
|
+---------- CT104 - Pi-hole
|
+---------- Media Server VM (Docker stack)
|
+---------- Additional Services

Separate off-site network:

Gateway host -- Tailscale (private SSH administration, verified)
             -- WireGuard (game-traffic tunnel, planned only)

---

## Network Addressing

> IP addresses shown in public documentation are sanitized.

### Local Network

| Device / Service | Address | Assignment |
| --- | --- | --- |
| Router / Gateway | `x.x.x.x` | Gateway |
| Proxmox Host | `x.x.x.x` | Static |
| File Server | `x.x.x.x` | DHCP Reservation |
| Immich | `x.x.x.x` | DHCP Reservation |
| GameServer1 | `x.x.x.x` | DHCP Reservation |
| Joplin Server | `x.x.x.x` | LAN Address |
| Pi-hole | `x.x.x.x` | DHCP Reservation|
| Media Server VM | `x.x.x.x` | Reserved LAN Address |
| Additional Services | `x.x.x.x` | As required |

### Addressing Method

The Eero Pro 7 currently provides DHCP services for the local network.

Important infrastructure devices use DHCP reservations or otherwise consistent addresses so that services can be reliably accessed without their addresses changing unexpectedly.

Exact internal addresses are excluded from the public version of this documentation.

---

## Proxmox Networking

### Network Bridge

**Bridge:** `vmbr0`

The Proxmox host uses a Linux network bridge named `vmbr0`.

The bridge allows virtual machines and containers hosted by Proxmox to communicate with the physical local network through the server's network interface.

Conceptually:

Physical Network Interface
|
|
vmbr0
|
+--- Proxmox Host
|
+--- LXC Container
|
+--- Virtual Machine
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

### GameServer1

GameServer1 hosts a dedicated Valheim server inside a Debian virtual machine.

Traffic Flow:

Game Client
|
|
LAN / External Network
|
|
Eero Router / Playit.gg or PlayFab
|
|
Proxmox Host
|
|
GameServer1 VM
|
|
Valheim Dedicated Server

Valheim currently uses UDP port 2456 as its base port.

UDP ports 2456-2458 were used when configuring and testing connectivity.

External access has been tested through Playit.gg and Valheim's PlayFab crossplay service.

---

### Joplin Server

Joplin provides self-hosted synchronization for notes across devices.

Traffic Flow:

Laptop / Desktop
|
|
Local Network
|
|
Proxmox Host
|
|
Joplin Server VM
|
|
Docker Compose
|
+--- Joplin Server
|
+--- PostgreSQL Database

Joplin is currently accessed using a local hostname:

`http://joplin.example.internal:22300`

The hostname uses Pi-hole's local DNS records. The address shown is sanitized for public documentation.

PostgreSQL is restricted to the internal Docker Compose network.

---

### Pi-hole

Pi-hole provides DNS filtering and local hostname resolution for the home network.

Traffic Flow:

Client Device
|
|
Local Network
|
|
CT104 - Pi-hole
|
+---------- Local DNS Records
|
+---------- DNS Filtering
|
+---------- Cloudflare Upstream DNS

The Eero Pro 7 remains responsible for DHCP and routing.

Its Custom DNS configuration points to Pi-hole.

Pi-hole handles DNS requests received from configured network clients.

Detailed configuration:

`13 - Pi-Hole.md`
---

## Remote Access

### Current Status

Remote access has been tested for GameServer1 through Playit.gg and Valheim's crossplay networking.

**Private gateway administration is now implemented:** Tailscale is enrolled on an independent off-site Debian gateway and a Linux laptop. SSH was verified over a separate network, after reboot, and following Ethernet reconnection. The gateway remained reachable after relocation to its intended off-site network.

This does **not** mean that all Proxmox services are currently exposed through Tailscale, nor that any public WireGuard game relay is configured. The latter remains a design goal, separate from private management access.

See `15 - Off-Site Gateway.md` for the administrative path and security boundaries.

### ISP / CGNAT

The current ISP is Hotwire Communications.

Testing identified that the ISP connection is behind Carrier-Grade NAT (CGNAT).

This means the router does not have a directly reachable public IPv4 address.

Normal port forwarding was tested for GameServer1 but did not provide reliable external connectivity.

Additional findings:

- Outbound IPv4 Internet connectivity is available, but the home router does not have a directly reachable public IPv4 address.
- No usable public IPv6 connection was detected.
- Standard inbound port forwarding cannot bypass ISP CGNAT.
- Playit.gg was configured as a workaround.
- PlayFab crossplay was also tested with Valheim.
- A paid static public IPv4 option was considered but not implemented.

### Playit.gg

Playit.gg was installed on GameServer1 to allow external players to connect without requiring normal port forwarding.

Testing confirmed that the tunnel could carry traffic to the game server.

However, different relay locations affected connection latency.

The free global relay had higher latency than regional routing during testing.

External player connectivity and long-term reliability still need additional testing.

### Planned Public Gateway Path

A separate off-site system may eventually accept public game connections and forward them over a dedicated WireGuard tunnel initiated outbound from the CGNAT home network. The off-site ISP's public IPv4/router capabilities, forwarding rules, tunnel configuration, game routing, and fail-closed behavior have **not** yet been established.

Tailscale for private SSH administration is a distinct, completed component; it is not proof that the WireGuard/public endpoint is operational.

---

## DNS

### Current Configuration

Pi-hole has been implemented as the primary DNS filtering service for the home network.

It runs inside Proxmox container CT104.

The Eero Pro 7 is configured to use Pi-hole through its Custom DNS settings.

Current configuration:

| Setting | Value |
| --- | --- |
| DNS Server | Pi-hole |
| Container | CT104 |
| DNS Address | `x.x.x.x` |
| DNS Port | 53 |
| Upstream DNS | Cloudflare |
| DHCP Server | Eero Pro 7 |
| Local Domain | `example.internal` - Sanitized |

### Local DNS Records

Pi-hole is also used to create local DNS records for Homelab applications.

Current records:

| Service | Hostname | Address |
| --- | --- | --- |
| Proxmox | `proxmox.example.internal` | `x.x.x.x` |
| File Server | `fileserver.example.internal` | `x.x.x.x` |
| Immich | `immich.example.internal` | `x.x.x.x` |
| Joplin | `joplin.example.internal` | `x.x.x.x` |
| Pi-hole | `pihole.example.internal` | `x.x.x.x` |

All addresses and hostnames are sanitized for public documentation.

These records allow local clients to use hostnames instead of manually entering IP addresses.

### DNS Testing

Pi-hole was tested on the Linux desktop.

After updating the Eero Custom DNS settings, the desktop initially continued using its previous DNS configuration.

The Ethernet connection was disconnected and reconnected.

After reconnecting, DNS-based ad blocking worked successfully.

Joplin was also tested using its new local hostname.

### Current Limitations

- Not every device has been tested.
- Pi-hole currently provides a single DNS service.
- DNS recovery following a Proxmox restart has not been tested.
- No secondary local DNS server has been configured.
- Pi-hole's persistent address assignment still needs verification.

If Pi-hole becomes unavailable, devices depending on it may experience DNS resolution problems.

A secondary DNS or recovery solution is being considered.


---

## Network Management

### Router

**Device:** Eero Pro 7

Responsibilities:

- Internet gateway
- DHCP
- Local network routing
- Custom DNS configuration pointing to Pi-hole

### Switch

**Device:** TP-Link JetStream T1600G-28PS

Responsibilities:

- Wired device connectivity
- PoE+ power delivery
- Future managed-network functionality

---

## Security Considerations

The network is being developed with the following principles:

- Avoid unnecessary public exposure of internal services
- Use consistent addressing for infrastructure
- Keep management interfaces internal whenever possible
- Separate services where practical
- Use authenticated access for network services
- Implement network segmentation as the environment grows

Sensitive information such as passwords, authentication tokens, public IP addresses, MAC addresses, and private keys is not stored in public documentation.

Joplin's current HTTP connection should remain limited to trusted local access until HTTPS or secure VPN-based access is configured.

---

## Media Server Networking

A Debian media VM on the Proxmox bridge runs Jellyfin and related download/indexer applications. Its media data is provided through VirtioFS from the ZFS host rather than through a guest-mounted SMB share. App-to-app connectivity and playback were tested; request approval and torrent VPN isolation still require validation. The media VM is intended for trusted-network use, not arbitrary public exposure.

See `14 - MediaServer1.md`.

---

## Planned Network Improvements

- Establish a more structured network configuration
- Investigate VLAN-based segmentation
- Separate IoT and security camera devices
- Improve local DNS reliability
- Investigate secondary DNS redundancy
- Confirm persistent Pi-hole addressing
- Test DNS recovery following a Proxmox reboot
- Improve remote access beyond the verified private gateway SSH path
- Implement and validate a separate WireGuard game-traffic tunnel
- Verify the off-site public IPv4/router capability before forwarding games
- Add PoE security cameras
- Replace long/custom patch cable runs with permanent Cat6 drops
- Improve network monitoring
- Create a detailed network diagram
- Continue testing Valheim external connectivity and latency
- Configure secure remote access for Joplin

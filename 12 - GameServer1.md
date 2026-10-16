# GameServer1

## Overview

GameServer1 is a dedicated virtual machine used to host a multiplayer Valheim server.

The VM runs Debian 13 on Proxmox and uses SteamCMD to install and maintain the official Valheim Dedicated Server.

The goal is to provide a persistent multiplayer world that friends can access while keeping the game server separate from the other homelab services.

* * *

## Virtual Machine Information

| Setting | Value |
| --- | --- |
| VM Name | GameServer1 |
| Type | Virtual Machine |
| Operating System | Debian 13 |
| CPU | 4 vCPU |
| Memory | 8 GB |
| Storage | 60 GB |
| Storage Pool | TCloud |
| Networking | vmbr0 |
| IP Address | x.x.x.x |
| Address Assignment | DHCP Reservation |
| QEMU Guest Agent | Enabled |

GameServer1 is configured for remote administration using SSH.

Debian runs without a desktop environment to reduce unnecessary resource usage.

* * *

## Architecture

Internet / LAN  
|  
|  
Eero Pro 7  
|  
|  
TP-Link Network Switch  
|  
|  
Proxmox Host  
|  
|  
GameServer1 VM  
|  
|  
Debian 13  
|  
|  
SteamCMD  
|  
|  
Valheim Dedicated Server  
|  
+--- Server Installation  
|  
+--- World Data  
|  
+--- Startup Script

External players have also been tested through Playit.gg and PlayFab crossplay.

* * *

## Installation

### Operating System

Debian 13 was installed as a minimal server installation.

SSH was configured for remote administration.

The QEMU Guest Agent was enabled for improved VM integration with Proxmox.

### SteamCMD

SteamCMD is used to download and update the Valheim Dedicated Server.

Required 32-bit dependencies were installed.

A dedicated Linux user was created for running the Valheim server.

### Dedicated Server

Steam App ID:

`896660`

The server was installed using SteamCMD.

* * *

## Directory Structure

### Server Installation

`/home/valheim/server`

This contains the Valheim Dedicated Server installation.

### World Data

`/home/valheim/data`

This directory is used for persistent Valheim world data.

### Startup Script

`/home/valheim/start_valheim.sh`

The startup script is used to launch the dedicated server with the selected configuration.

Keeping world data separate from the server installation makes updates and future backups easier to manage.

* * *

## Server Configuration

### Server Name

`XXXXX XXXX`

### World Name

XXXXXXXXXX

### Network Port

`2456`

### Additional Ports

`2456-2458 UDP`

These ports were used during network and forwarding tests.

### Crossplay

Valheim crossplay was configured and tested.

PlayFab successfully registered the server and generated join codes.

However, client connection attempts sometimes froze or failed.

Steam-only networking was also tested.

### Password

The server uses password-based access.

The password is intentionally excluded from public documentation.

* * *

## Server Management

### Start Server

The current server is started manually using:

`/home/valheim/start_valheim.sh`

### Automatic Startup

Not yet fully implemented.

The server still needs to be configured as a systemd service.

This would allow:

- Automatic startup
- Automatic restart after failures
- Better service management
- Easier log access
- Recovery after VM restarts

* * *

## Networking

### Local Network

GameServer1 uses a reserved LAN address.

Public documentation address:

`x.x.x.x`

Local clients can use the LAN connection for lower latency.

### External Access

External connectivity has been tested using:

- PlayFab crossplay
- Playit.gg
- Router port forwarding

Normal port forwarding did not work reliably because the ISP uses CGNAT.

* * *

## ISP / CGNAT

The current ISP is Hotwire Communications.

Testing confirmed that the router is behind Carrier-Grade NAT.

The router's WAN address is not directly reachable from the public Internet.

Normal port forwarding cannot bypass CGNAT.

Additional testing confirmed:

- IPv4 connectivity is available.
- No usable public IPv6 was detected.
- External port forwarding did not reach the server directly.
- Alternative networking methods are required for outside clients.

A paid public/static IPv4 service was considered but not implemented.

The goal is to avoid unnecessary monthly costs while maintaining low latency.

* * *

## Playit.gg

Playit.gg was installed and configured on GameServer1.

A Valheim tunnel was created to provide external connectivity.

The Playit agent was tested as a running systemd service.

Testing confirmed that traffic passed through the tunnel to the Valheim server.

### Benefits

- Works around CGNAT
- Does not require normal router port forwarding
- External clients do not need to install additional software
- Can provide access through a public tunnel address

### Problems

Relay location affected latency.

The free global routing option produced noticeably higher latency during testing.

Regional routing showed lower latency.

Reliable multiplayer connections still need further testing.

The actual tunnel address is excluded from public documentation.

* * *

## PlayFab Crossplay

Valheim's crossplay mode uses PlayFab networking.

During testing:

- The server registered with PlayFab.
- Join codes were generated.
- Connection attempts sometimes froze.
- Direct IP connections were also tested.
- IPv6-related errors appeared in logs.

Crossplay is not yet considered fully reliable.

* * *

## Troubleshooting

### External Connections Failing

Problem:

Players could not connect from outside the local network.

Cause:

ISP CGNAT prevented normal inbound IPv4 connections.

Workaround:

Configured Playit.gg and tested PlayFab crossplay.

### IPv6 Errors

Problem:

Valheim attempted to retrieve or use IPv6 networking information.

Investigation:

Tested public IPv4 and IPv6 connectivity.

Findings:

No usable public IPv6 connectivity was detected.

Disabling IPv6 did not resolve the problem and was not kept as a permanent change.

### PlayFab Connection Failure

Problem:

Clients froze or failed while joining through the generated join code.

Testing included:

- Reviewing server logs
- Checking UDP traffic
- Trying Steam-only networking
- Testing Playit.gg

Current Status:

Additional verification required.

### Server Startup Failure

Problem:

The server initially stopped during startup due to configuration and password-related issues.

Solution:

Corrected the configuration and confirmed successful startup.

A later restart also showed a WorldGenerator-related error.

Further startup reliability testing is still needed.

* * *

## Troubleshooting Flow

When GameServer1 becomes unavailable:

1.  Confirm Proxmox is operational.
2.  Confirm the GameServer1 VM is running.
3.  Confirm Debian has network connectivity.
4.  Confirm the Valheim process is running.
5.  Review the Valheim startup logs.
6.  Check UDP port availability.
7.  Test LAN connectivity.
8.  Check Playit.gg status.
9.  Test external connectivity.
10. Check world data and permissions if startup fails.

This separates problems into:

- Proxmox
- Virtual machine
- Operating system
- Valheim application
- World data
- Local networking
- External networking

* * *

## Backup Considerations

Important data includes:

- World saves
- Server configuration
- Startup script
- VM configuration

Persistent data location:

`/home/valheim/data`

The world data should be backed up separately from the Steam-managed installation.

Automated world backups have not yet been implemented or verified.

* * *

## Security

Current considerations include:

- Dedicated Linux user for Valheim
- SSH used for administration
- Server password required
- Public IP addresses excluded from documentation
- Game server credentials excluded
- External access limited to intended services
- Proxmox management not intentionally exposed to the Internet

* * *

## Current Limitations

- External connectivity still requires testing.
- PlayFab connection reliability is unresolved.
- Playit.gg latency varies by relay location.
- Valheim is still started manually.
- Automatic restart is not configured.
- World backups are not automated.
- Server shutdown when empty is not implemented.
- Discord integration is not implemented.

* * *

## Planned Improvements

- Improve external player connectivity
- Reduce connection latency
- Configure a systemd service
- Configure automatic server startup
- Configure automatic restart on failure
- Implement automated world backups
- Test restoring saved worlds
- Investigate Discord bot integration
- Investigate starting the VM on demand
- Investigate stopping the server when empty
- Test recovery following Proxmox reboot
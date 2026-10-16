# Troubleshooting Logs

## Overview

This section documents major technical problems encountered while building and managing the homelab.

Daily Logs are used to record normal work and progress.

Troubleshooting Logs focus on specific problems, their causes, attempted solutions, and what was learned.

---

## GameServer1 - CGNAT Connectivity

### Problem

External players were unable to connect to the Valheim dedicated server using normal port forwarding.

### Symptoms

- LAN connections worked during testing.
- External clients could not reach the server directly.
- Router port forwarding did not resolve the issue.
- Valheim was listening on the expected UDP ports.

### Investigation

Checked:

- Server network configuration
- Router port forwarding
- UDP traffic
- WAN addressing
- Public IPv4 connectivity
- ISP network configuration

### Root Cause

Hotwire Communications uses Carrier-Grade NAT for the current connection.

The router does not have a directly reachable public IPv4 address.

Normal port forwarding cannot bypass the ISP's CGNAT configuration.

### Solution / Workaround

- Configured Playit.gg
- Tested Valheim PlayFab crossplay
- Used LAN connections for local testing
- Investigated ISP public IPv4 options

### Current Status

Playit.gg successfully forwarded traffic during testing.

External connection reliability and latency still need improvement.

---

## GameServer1 - PlayFab Connection Issues

### Problem

Players experienced problems joining through Valheim's crossplay system.

### Symptoms

- PlayFab registered the server.
- Join codes were generated.
- Client connections sometimes froze or failed.
- Server startup logs included IPv6-related errors.

### Investigation

- Tested crossplay
- Tested Steam-only connections
- Reviewed server logs
- Checked IPv4 and IPv6 connectivity
- Tested alternative external networking

### Findings

The ISP connection does not currently provide usable public IPv6 connectivity.

Disabling IPv6 did not resolve the problem.

Steam-only and Playit.gg connectivity were also tested.

### Current Status

Additional external connection testing is needed.

---

## GameServer1 - Startup Problems

### Problem

The Valheim server initially had trouble completing startup.

### Symptoms

- Server stopped during startup.
- Configuration and password-related errors appeared.
- A later restart showed a WorldGenerator-related error.

### Solution

Corrected the initial server configuration and confirmed successful server startup.

### Current Status

The server has successfully started, but reliable automatic startup and restart behavior still need to be implemented and tested.

---

## Joplin - Sudo Permissions

### Problem

The newly installed Debian VM did not include `sudo`, and the normal user did not have sudo permissions.

### Solution

- Logged in as root
- Installed `sudo`
- Added the normal user to the sudo group
- Reconnected through SSH

### Result

Administrative commands became available through the normal user account.

---

## Joplin - Account Confirmation

### Problem

Joplin account confirmation emails were not arriving.

### Root Cause

SMTP had not been configured and the email service was disabled.

### Solution

Used Joplin's local administration workflow to handle account confirmation.

### Result

Account setup was able to continue without configuring an external email service.

---

## Joplin - Database and Keychain Warnings

### Problem

Initial Joplin startup logs showed PostgreSQL migration-table errors and keychain warnings.

### Investigation

- Reviewed Docker Compose logs
- Confirmed PostgreSQL initialized
- Confirmed Joplin began starting background services

### Current Status

Joplin has been deployed and synchronization configured.

The warnings still need verification before the deployment is considered fully tested.

---

## Joplin - Markdown and Image Rendering

### Problem

Some notes did not display correctly after synchronization.

### Symptoms

- Raw Markdown appeared instead of rendered content.
- HTML appeared directly in some notes.
- Embedded Base64 image text was visible.

### Investigation

Reviewed synchronization behavior and considered differences between editor and viewer modes.

### Current Status

Not confirmed fixed.

Further testing is required before changing the original note contents.

---

## Pi-hole - DNS Not Updating on Desktop

### Problem

The Linux desktop was not immediately using Pi-hole after changing the Eero Custom DNS configuration.

### Symptoms

- Pi-hole was installed and running.
- Eero Custom DNS was configured.
- The desktop still showed its previous DNS servers.
- DNS-based ad blocking was not initially working.

### Investigation

Reviewed DNS configuration using:

`resolvectl status`

### Solution

Disconnected and reconnected the desktop's Ethernet connection.

### Verification

After reconnecting, Pi-hole ad blocking worked successfully.

---

## Joplin - Invalid Origin After Local DNS Change

### Problem

Joplin returned an Invalid origin error when accessed using its new local hostname.

### Symptoms

The web interface rejected the new hostname.

Sanitized error:

`Invalid origin: http://joplin.example.internal:22300`

### Investigation

Checked the Joplin Docker Compose application URL configuration.

### Root Cause

Joplin's configured APP_BASE_URL did not match the hostname being used.

### Solution

Updated the Compose configuration located at:

`/opt/joplin/compose.yaml`

Sanitized setting:

`APP_BASE_URL=http://joplin.example.internal:22300`

Applied the updated Docker Compose configuration.

### Verification

Joplin worked using the new local hostname.

---

---

## Future Troubleshooting Entries

New incidents should include:

### Problem

What stopped working?

### Symptoms

What was observed?

### Investigation

What was checked or tested?

### Root Cause

What caused the problem?

### Solution

What fixed it?

### Verification

How was the fix confirmed?

### Notes

What was learned or still needs improvement?

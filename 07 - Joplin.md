# Joplin

## Overview

Joplin Server is used for self-hosted note synchronization between personal devices.

The service runs inside a dedicated Debian virtual machine hosted by Proxmox.

Joplin Server and PostgreSQL are deployed using Docker Compose.

The main purpose is to keep personal notes and Homelab documentation synchronized between my laptop and desktop without relying on a third-party synchronization service.

* * *

## Virtual Machine Information

| Setting | Value |
| --- | --- |
| VM Name | Joplin Server |
| VM ID | 103 |
| Type | Virtual Machine |
| Operating System | Debian |
| CPU | 2 Cores |
| Memory | 2GB |
| Storage | 32GB |
| Networking | `vmbr0` |
| Address | `x.x.x.x` - Sanitized |
| Start at Boot | Yes |

The VM was created specifically for hosting Joplin and its PostgreSQL database.

SSH access has been configured for remote administration.

* * *

## Architecture

Proxmox Host  
|  
|  
Joplin Server VM  
|  
|  
Debian  
|  
|  
Docker Compose  
|  
+--- Joplin Server  
|  
+--- PostgreSQL  
|  
|  
Local Network  
|  
+--- Desktop  
|  
+--- Laptop

Joplin Server manages synchronization while PostgreSQL stores the server's application database.

* * *

## Application Stack

| Service | Purpose |
| --- | --- |
| Joplin Server | Note synchronization |
| PostgreSQL | Application database |
| Docker Engine | Container runtime |
| Docker Compose | Application deployment and management |

Both application containers have been deployed and started.

* * *

## Installation

### Operating System

Debian was installed as a dedicated Proxmox virtual machine.

SSH access was configured.

The Debian installation initially did not include `sudo`.

This was fixed by installing `sudo` as root and adding the normal administrative user to the sudo group.

### Docker

Docker Engine and Docker Compose were installed.

The application files are stored under:

`/opt/joplin`

### Docker Compose

Joplin Server and PostgreSQL are managed together using Docker Compose.

The current Compose configuration uses:

`restart: unless-stopped`

This allows the containers to restart automatically when Docker starts.

The VM also has Start at Boot enabled in Proxmox.

Automatic recovery following a full Proxmox reboot still needs testing.

* * *

## Database

Joplin Server uses PostgreSQL.

The database runs as a separate Docker container.

PostgreSQL is only accessible through the internal Docker Compose network.

It is not directly exposed to other devices on the local network.

This reduces unnecessary exposure of the database service.

* * *

## Networking

### Network Bridge

`vmbr0`

### Local Address

`x.x.x.x`

### Joplin Server Port

`22300`

### Application URL

Joplin now uses a local DNS hostname provided by Pi-hole.

Current application URL (sanitized):

`http://joplin.example.internal:22300`

The Docker Compose configuration was updated to use the same hostname.

Application setting (sanitized):

`APP_BASE_URL=http://joplin.example.internal:22300`

The configuration is located under:

`/opt/joplin/compose.yaml`

Joplin currently uses local HTTP access.

HTTPS has not yet been configured.

The service should not be forwarded directly to the public Internet in its current configuration.

* * *

## User Management

Joplin Server has separate administrator and personal user accounts.

### Administrator Account

Used for:

- Server administration
- User management
- Application settings
- Account troubleshooting

### Personal Account

Used for:

- Normal note synchronization
- Personal notebooks
- Homelab documentation
- Daily notes

Administrator credentials are not included in public documentation.

* * *

## Synchronization

Joplin has been configured to synchronize notes between the laptop and desktop.

### Current Configuration

- Self-hosted Joplin Server
- Laptop connected
- Desktop connected
- End-to-end encryption enabled
- Homelab documentation imported

Synchronization has been configured, but note rendering and some synchronization behavior still need verification.

* * *

## End-to-End Encryption

Joplin end-to-end encryption (E2EE) has been enabled.

This protects synchronized note content when the clients are correctly configured for encryption.

All devices must use the correct encryption configuration and keys.

Important encryption credentials and recovery information are stored privately.

Exported Markdown files are not protected by Joplin's encryption.

* * *

## Homelab Documentation

Joplin is being used as the working location for Homelab documentation.

The GitHub repository is used for publishing sanitized Markdown documentation.

Current workflow:

Joplin  
|  
|  
Homelab Notes  
|  
|  
Markdown Export  
|  
|  
Review / Sanitization  
|  
|  
Local Git Repository  
|  
|  
GitHub

This separates private working notes from publicly shared documentation.

Only notes intended for the public repository should be exported.

* * *

## Service Management

### Check Docker

`systemctl status docker`

### View Running Containers

`docker ps`

### View Compose Services

`docker compose ps`

### Start Joplin

`docker compose up -d`

### Stop Joplin

`docker compose down`

### Restart Joplin

`docker compose restart`

### View Logs

`docker compose logs --tail=100`

These Compose commands should be run from:

`/opt/joplin`

* * *

## Startup and Recovery

The Joplin containers are configured with:

`restart: unless-stopped`

Expected startup sequence:

1.  Proxmox boots.
2.  The Joplin virtual machine starts.
3.  Debian starts.
4.  Docker starts.
5.  PostgreSQL starts.
6.  Joplin Server starts.
7.  Clients can reconnect and synchronize.

VM103 has Start at Boot enabled in Proxmox.

Docker Compose uses `restart: unless-stopped` for Joplin and PostgreSQL.

Automatic recovery following a complete Proxmox host restart still needs verification.

* * *

## Troubleshooting

### Initial Debian Permissions

Problem:

`sudo` was not installed by default.

Solution:

- Logged in as root
- Installed `sudo`
- Added the normal user to the sudo group
- Reconnected to SSH

### Account Confirmation

Problem:

Account confirmation emails did not arrive.

Cause:

SMTP was not configured and the email service was disabled.

Solution:

Used Joplin's local administration workflow to handle account confirmation.

### PostgreSQL Startup

Initial logs included PostgreSQL migration-table errors.

Joplin continued starting services and processing background tasks.

The application and synchronization should be checked before treating database startup as fully verified.

### Joplin Keychain Warnings

Keychain warnings appeared in the initial logs.

The cause and impact still need additional verification.

### Markdown / Image Rendering

Some synchronized notes displayed raw Markdown, HTML, and Base64 image data rather than rendering correctly.

This was noticed after synchronizing notes to the desktop.

Current Status:

Not confirmed fixed.

Further testing is needed before changing the original note contents.

### Local DNS / Invalid Origin

Problem:

Joplin returned an Invalid origin error after accessing the application through its new local DNS hostname.

Error (sanitized):

`Invalid origin: http://joplin.example.internal:22300`

Cause:

The application URL configured in Joplin did not match the hostname being used to access the server.

Solution:

Updated the Docker Compose configuration at:

`/opt/joplin/compose.yaml`

Changed the application URL to match the new hostname.

Sanitized setting:

`APP_BASE_URL=http://joplin.example.internal:22300`

The file required sudo permissions to edit.

Applied the updated configuration through Docker Compose.

Result:

Joplin worked using the new local hostname.

* * *

## Troubleshooting Flow

If Joplin becomes unavailable:

1.  Confirm the Proxmox host is running.
2.  Confirm the Joplin VM is running.
3.  Check the VM network connection.
4.  Confirm Docker is running.
5.  Check `docker compose ps`.
6.  Review Joplin logs.
7.  Review PostgreSQL logs.
8.  Verify access to port 22300.
9.  Check account authentication.
10. Test synchronization from a client.

* * *

## Security

Current security considerations include:

- Joplin is hosted on the local network.
- PostgreSQL is not exposed directly to the LAN.
- Separate administrator and personal accounts are used.
- End-to-end encryption is enabled for note synchronization.
- Passwords and encryption keys are excluded from public documentation.
- HTTPS remote access has not yet been configured.
- Exported Markdown files must be sanitized before GitHub publication.

* * *

## Backup Considerations

Important data includes:

- PostgreSQL database
- Docker Compose configuration
- Environment configuration
- Joplin application settings
- Proxmox VM configuration
- Notes and associated attachments

Joplin encryption is not a replacement for backups.

Separate PostgreSQL backups and recovery procedures still need to be implemented.

* * *

## Current Limitations

- HTTPS remote access is not configured.
- Automatic recovery after a complete Proxmox reboot has not yet been tested.
- PostgreSQL backups are not yet fully implemented.
- Complete restoration has not been tested.
- Some note and embedded image rendering issues remain unresolved.
- Initial database and keychain log warnings need verification.
- Automated monitoring is not implemented.

* * *

## Planned Improvements

- Verify synchronization on laptop and desktop
- Fix Markdown and image rendering problems
- Configure secure HTTPS remote access
- Evaluate Tailscale Serve
- Implement automated PostgreSQL backups
- Store backups separately from the Joplin VM
- Test VM103 and Docker Compose recovery following a Proxmox reboot
- Test backup restoration
- Establish monitoring and alerts
- Continue improving the Joplin-to-GitHub documentation workflow

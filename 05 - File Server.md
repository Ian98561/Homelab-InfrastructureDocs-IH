# File Server

## Overview

The homelab file server provides centralized network storage to authorized devices on the local network.

The service runs inside a Debian LXC container hosted by Proxmox and uses ZFS-backed storage from the `TCloud/data` dataset.

SMB is used to make the storage accessible to Windows, Linux, and other compatible devices on the local network.

---

## Container Information

| Setting | Value |
| --- | --- |
| Container ID| CT100 |
| Hostname | `fileserver` |
| Type | LXC |
| Operating System | Debian |
| CPU | 2 cores|
| Memory | 2 GB |
| Networking | `vmbr0` |
| Start at boot | Yes |
| Address | `192.168.x.x` - Sanitized |

The container is configured with:

`onboot: 1`

This allows the file server to automatically start when the Proxmox host boots.

---

## Architecture

The file server separates the network service from the underlying storage.

Conceptually:

Physically HDDs
|
|
ZFS RAIDZ2
|
|
TCloud/data
|
|
Proxmox Host
|
|
LXC Mount Point
|
|
CT100 - File Server
|
|
SMB
|
|
Network Clients

This allows the SMB service to run inside an isolated container while the actual persisten data remains stored on the ZFS pool.

---

## Storage

### Host Dataset

The file server uses:

`TCloud/data`

Host mount point:

`/TCloud/data`

### Container Mount Point

The dataset is mounted inside CT100 at:

`/data`

This provides the container access to the persistent ZFS-backed storage without placing the main file data inside the container's root filesystem.

`pct config 100`

---

## SMB

SMB is used to provide file sharing to devices on the local network.

### Samba Configuration

The primary Samba configuration file is:

`/etc/samba/smb.conf`

This file defines:

- SMB shares
- Share paths
- Authentication requirements
- Read/write permissions
- Allowed users
- Additional Samba settings

---

## SMB User

A dedicated Samba account is used for authenticated file access.

Current user:

`lan`

Passwords and authentication credentials are intentionally excluded from this documentation.

Credentials should never be stored directly in public documentation.

---

## Client Access

Clients connect to the file server using SMB.

### Windows

A share can be accessed using:

`\\<fileserver-address>\<share-name>`

or:

`\\fileserver\<share-name>`

### Linux 

Graphical file managers can access SMB using:

`smb://<fileserver-address>/<share-name>`

SMB shares can also be mounted through the Linux command line or configured to mount automatically at startup.

---

## Service Management

### Check Samba Status

`systemctl start smbd`

### Stop Samba

`systemctl stop smbd`

### Restart Samba

`systemctl restart smbd`

### Enable Samba at Boot

`systemctl enable smbd`

---

## Configuration Validation

After modifying Samba configuration, the configuration can be checked before restarting the service.

`testparm`

This verifies the Samba configuration and helps identify syntax or configuration errors.

---

## User Management

### View Samba Users

`pdbedit -L`

### Add a Samba User

`sudo smbpasswd -a <username>`

### Change Samba Passwords

`sudo smbpasswd <username>`

### Remove a Samba User

`sudo smbpasswd -x <username>`

Samba authentication should user dedicated accounts and passwords rather than storing credentials directly in configuration documentation.

---

## Permsissions

File access depends on both:

1. Linux filesystem permissions
2. Samba share permissions

Both layers must allow the intended user access.

When troubleshooting permission problems, the following should be checked:

- Dataset ownership
- Directory ownership
- Liunux user and group permissions
- Samba user configuration
- Read/write settings

---

## Troubleshooting

### Confirm Container Is Running

From the Proxmox host:

`pct status 100`

### Enter Container

`pct enter 100`

### Check Network Address

Inside the container:

`ip addr`

### Check Samba

`systemctl status smbd`

### Validate Configuration

`testparm`

### Verify Storage Mount

`df -h`

or:

`mount`

The `/data` mount should be available inside the container.

### Verify Directory

`ls -lah /data`

### Verify Network Connectivity

From another device:

`ping <fileserver-address>`

### Test SMB Availability 

From Linux:

`smbclient -L //<fileserver-adress> -U <usernameL>`

---

## Troubleshooting Flow

When the file server cannot be accessed:

1. Confirm CT100 is running.
2. Confirm the container has network connectivity.
3. Confirm the `/data` storage mount exists.
4. Confirm `smbd` is running.
5. Validate `/etc/samba/smb.conf` with `testparm`
6. Confirm the SMB user exists.
7. Check filesystem ownership and permissions.
8. Test SMB access from another client.
9. Review Samba logs if the issue continues.

This separates the problem into:

- Container
- Network
- Storage
- Samba 
- Authentication
- Permissions

rather than changing multiple settings at once.

---

## Logs

Useful Samba logs can typically be found under:

`/var/log/samba/`

System service logs can also be viewed using:

`journalctl -u smbd`

Recent service logs:

`journalctl -u smbd --since today`

---

## Startup and Recovery

CT100 is configured to start automatically when the Proxmox host starts.

After a host reboot, the basic recovery sequence is:

1. Proxmox boots.
2. ZFS pool becomes available
3. CT100 starts automatically
4. The ZFS dataset is mounted into the container.
5. Samba starts inside the container. 
6. Network clinents regain access to the SMB share.

After maintenance or a power interruption, file-server availability should be verified before assuming the service has fully recovered.

---

## Security

Current security considerations include:

- SMB reqquires authenticated access.
- The file server is intended for internal network use.
- The SMB service is not intentionally exposed directly to the Internet.
- Passwords are excluded from documentation.
- Public documentation uses sanitized IP addresses.
- Remote access should eventually use a VPN-based solution rather than direct SMB exposure.

---

## Backup Considerations

The SMB service itself is separate from the persistent data stored in:

`TCloud/data`

Important backup considerations include:

- File data
- Samba configuration
- User and permission configuration
- Container configuration
- Recovery procedures

ZFS redundancy provides protection against certain disk failures but does not replace backups.

Backup procedures are documented separately in:

`09 - Backup & Recovery.md`

---

## Current Limitations

- Off-system backups are not yet fully implemented.
- Automated snapshots are not yet implemented.
- Local DNS / hostname resolution is still being developed.
- Remote access is not currently implemented.
- Recovery procedures need to be tested and documented.
- Monitoring and automated alerts are not yet implemented.

---

## Planned Improvements

- Implement automated ZFS snapshots
- Establish an off-system backup
- Improve hostname-based access
- Implement monitoring and alerts
- Test restoration of file-server data
- Document permission and user-management procedures
- Implement secure remote access
- Test automatic recovery following a Proxmox reboot










































# File Server

## Overview

The homelab file server provides centralized network storage to authorized devices on the local network.

The service runs inside a Debian LXC container hosted by Proxmox and uses ZFS-backed storage from the `TCloud/data` dataset.

SMB is used to make the storage accessible to Windows, Linux, and other compatible devices on the local network.

---

## Container Information

| Setting | Value |
| --- | --- |
| Container ID | CT100 |
| Hostname | `fileserver` |
| Type | LXC |
| Operating System | Debian |
| CPU | 2 cores |
| Memory | 2 GB |
| Networking | `vmbr0` |
| Start at Boot | Yes |
| Address | `x.x.x.x` - Sanitized |

The container is configured with:

`onboot: 1`

This allows the file server to automatically start when the Proxmox host boots.

---

## Architecture

The file server separates the network service from the underlying storage.

Conceptually:

Physical HDDs
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

This allows the SMB service to run inside an isolated container while the actual persistent data remains stored on the ZFS pool.

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

`<smb-user>` (sanitized placeholder)

Passwords and authentication credentials are intentionally excluded from this documentation.

Credentials should never be stored directly in public documentation.

---

## Client Access

Clients connect to the file server using SMB.

### Windows

A share can be accessed using:

`\\x.x.x.x\<share-name>`

or:

`\\fileserver\<share-name>`

### Linux

Graphical file managers can access SMB using:

`smb://x.x.x.x/<share-name>`

### Persistent Linux Workstation Mount

In October 2026, an authenticated CIFS/SMB share was mounted on a Linux workstation with a systemd automount configured through `/etc/fstab` (`x-systemd.automount`). The real server address, share name, credentials-file path, and client mount path are omitted from public documentation.

The automount was verified as accessible after setup. A user could browse the share but lacked permission to delete some files in a folder, so the issue was narrowed to server-side Samba/ZFS ownership or ACL settings; a verified permanent fix was not recorded.

This is a client-side SMB mount. Separately, the media VM receives the NAS media dataset by **VirtioFS** from Proxmox, not by mounting the same SMB share inside that VM.

SMB shares can also be mounted through the Linux command line or configured to mount automatically at startup.

---

## Service Management

### Check Samba Status

`systemctl status smbd`

### Start Samba

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

### Change Samba Password

`sudo smbpasswd <username>`

### Remove a Samba User

`sudo smbpasswd -x <username>`

Samba authentication should use dedicated accounts and passwords rather than storing credentials directly in public documentation.

---

## Permissions

File access depends on both:

1. Linux filesystem permissions
2. Samba share permissions

Both layers must allow the intended user access.

When troubleshooting permission problems, the following should be checked:

- Dataset ownership
- Directory ownership
- Linux user and group permissions
- Samba user configuration
- Samba share configuration
- Read/write settings

---

## Troubleshooting

### October 2026 LXC Networking Recovery

The file-server LXC temporarily lost its IPv4 address/default route during a startup/network issue. The container configuration had IPv6 DHCP enabled even though the network did not provide usable DHCPv6 service. Changing the LXC setting from `ip6=dhcp` to `ip6=manual` cleared the observed networking-service timeout and restored IPv4 access.

The immediate recovery was tested, but long-term DHCP renewals and repeated reboot behavior still need validation. A change to IPv6 startup configuration is **not** a proven fix for every future SMB outage.

### SMB Delete-Permission Investigation

Workstation access alone did not guarantee delete permissions. When reading works but deleting fails, review both Samba share rules **and** the underlying ZFS dataset ACL/ownership for the authenticated identity. Do not broadly relax permissions without confirming the intended access model. The final resolution of this specific issue was not recorded.

---

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

`ping x.x.x.x`

### Test SMB Availability

From Linux:

`smbclient -L //x.x.x.x -U <username>`

---

## Troubleshooting Flow

When the file server cannot be accessed:

1. Confirm CT100 is running.
2. Confirm the container has network connectivity.
3. Confirm the `/data` storage mount exists.
4. Confirm `smbd` is running.
5. Validate `/etc/samba/smb.conf` with `testparm`.
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

Rather than changing multiple settings at once.

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
2. ZFS pool becomes available.
3. CT100 starts automatically.
4. The ZFS dataset is mounted into the container.
5. Samba starts inside the container.
6. Network clients regain access to the SMB share.

After maintenance or a power interruption, file-server availability should be verified before assuming the service has fully recovered.

---

## Security

Current security considerations include:

- SMB requires authenticated access.
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
- Local DNS records are now configured through Pi-hole, but SMB access using the new hostname still needs verification.
- Direct public SMB exposure remains intentionally unimplemented. A separate private Tailscale gateway has been tested, but remote SMB access through it has not been verified.
- Some delete operations on the mounted share remained blocked by Samba/ZFS permissions during testing.
- Recovery procedures need to be tested and documented.
- Monitoring and automated alerts are not yet implemented.

---

## Planned Improvements

- Implement automated ZFS snapshots
- Establish an off-system backup
- Test SMB access using the Pi-hole local DNS hostname
- Implement monitoring and alerts
- Test restoration of file-server data
- Document permission and user-management procedures
- Implement secure remote access
- Test automatic recovery following a Proxmox reboot

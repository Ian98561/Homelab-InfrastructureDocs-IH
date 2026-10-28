# Pi-hole

## Overview

Pi-hole is used as the homelab's local DNS filtering and ad-blocking service.

The service runs inside a dedicated Debian LXC container hosted by Proxmox.

Pi-hole is configured as the DNS server for the home network through the Eero Pro 7 router.

It is also used to manage local DNS records so that Homelab applications can be accessed using hostnames instead of IP addresses.

---

## Container Information

| Setting | Value |
| --- | --- |
| Container ID | CT104 |
| Hostname | pihole |
| Type | LXC |
| Operating System | Debian 13 |
| CPU | 1 core |
| Memory | 1024 MB |
| Swap | 512 MB |
| Storage | 8 GB |
| Storage Pool | TCloud |
| Networking | vmbr0 |
| Address Assignment | DHCP |
| Address | x.x.x.x - Sanitized |
| Unprivileged | Yes |
| Start at Boot | Yes |

CT104 is configured to start automatically when the Proxmox host boots.

Pi-hole is installed directly inside the Debian container rather than using Docker.

---

## Architecture

Internet
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
CT104 - Pi-hole
|
|
Pi-hole FTL
|
+--- DNS Filtering
|
+--- Local DNS Records
|
+--- Cloudflare Upstream DNS
|
|
Network Clients

The Eero Pro 7 is still responsible for routing and DHCP.

Pi-hole handles DNS requests sent to it and filters requests using its configured blocklists.

---

## Installation

### Operating System

Debian 13 was installed inside a new Proxmox LXC container.

The container was configured with:

- 1 CPU core
- 1024 MB RAM
- 512 MB swap
- 8 GB storage
- Unprivileged container
- vmbr0 networking
- DHCP
- Start at Boot enabled

### Pi-hole Installation

Pi-hole was installed directly through its native installation process.

Cloudflare was selected as the upstream DNS provider.

After installation:

- Pi-hole FTL started successfully
- DNS service was listening on port 53
- DNS blocking was enabled
- Administrator password was configured
- Web administration interface became available
- Container DNS resolution was verified

---

## DNS Configuration

### Upstream DNS

**Provider:** Cloudflare

Pi-hole forwards DNS requests that are not answered locally or blocked to the configured upstream DNS provider.

### DNS Port

`53`

### Web Interface Port

`80`

### Eero DNS Configuration

The Eero Pro 7 has been configured to use Pi-hole through its Custom DNS settings.

Pi-hole address:

`x.x.x.x`

The router continues to handle DHCP and local network routing.

This configuration allows network clients using the configured DNS path to benefit from Pi-hole filtering.

---

## Local DNS

Pi-hole is also used to provide local DNS records for Homelab services.

### Internal Domain

`example.internal`

This is a sanitized placeholder for the actual internal domain.

The real internal domain is used for accessing applications on the local network.

### Current DNS Records

| Hostname | Address | Service |
| --- | --- | --- |
| proxmox.example.internal | x.x.x.x | Proxmox VE |
| fileserver.example.internal | x.x.x.x | SMB File Server |
| immich.example.internal | x.x.x.x | Immich |
| joplin.example.internal | x.x.x.x | Joplin Server |
| pihole.example.internal | x.x.x.x | Pi-hole |

These records allow local devices to resolve service names without manually entering their IP addresses.

All hostnames and addresses shown above are sanitized examples of the actual configuration.

---

## DNS Resolution

### Before Pi-hole

Homelab services were primarily accessed using their IP addresses.

Example:

`http://x.x.x.x:22300`

### After Pi-hole

Services can now be accessed using local hostnames.

Example (sanitized):

`http://joplin.example.internal:22300`

Other service address examples include:

Proxmox:

`https://proxmox.example.internal:8006`

Immich:

`http://immich.example.internal:2283`

Pi-hole:

`http://pihole.example.internal/admin`

These are sanitized hostname-based address formats. Not every application URL has been separately verified.

Local DNS resolution does not automatically provide HTTPS or trusted certificates.

---

## Ad Blocking

Pi-hole blocks DNS requests to domains listed in its configured blocklists.

This can reduce:

- Advertising domains
- Tracking domains
- Unwanted third-party requests
- Some malicious or suspicious domains

Pi-hole provides DNS-based filtering rather than modifying website content directly.

This means it cannot reliably remove advertisements served from the same domains as normal website or video content.

---

## Testing

### Container DNS

DNS resolution was tested from inside the Pi-hole container.

Results:

- Pi-hole installation completed
- FTL service was running
- DNS port 53 was listening
- Blocking was enabled
- DNS resolution worked

### Eero DNS

The Eero Pro 7 Custom DNS configuration was changed to use Pi-hole.

After the change, network clients needed to refresh their connection to receive or use the updated DNS settings.

### Desktop Testing

The Linux desktop initially continued using the previous ISP DNS servers.

DNS configuration was checked using:

`resolvectl status`

The Ethernet connection was disconnected and reconnected.

After reconnecting, Pi-hole ad blocking worked successfully.

A known advertising domain was tested and confirmed to be blocked.

### Mobile Testing

Additional mobile DNS testing is still needed to verify the behavior on other devices.

---

## Service Management

### Confirm Container Status

From Proxmox:

`pct status 104`

### Enter Container

`pct enter 104`

### Check Pi-hole Status

Inside CT104:

`pihole status`

### Check FTL Service

`systemctl status pihole-FTL`

### Restart FTL

`systemctl restart pihole-FTL`

### View Service Logs

`journalctl -u pihole-FTL`

### Follow Service Logs

`journalctl -u pihole-FTL -f`

---

## Web Administration

The Pi-hole web interface can be accessed locally using:

`http://x.x.x.x/admin`

The web interface is used for:

- Viewing DNS requests
- Reviewing blocked queries
- Managing blocklists
- Managing local DNS records
- Changing upstream DNS settings
- Reviewing system statistics
- Managing application settings

The administrator password is excluded from public documentation.

---

## Troubleshooting

### October 2026 DHCPv6/Networking Failure

After Pi-hole was selected as the router's DNS resolver, a network outage occurred despite the router appearing online. The Pi-hole service process existed, but DNS requests failed while the container lacked a usable IPv4 address and default route. The file-server container exhibited a similar network startup failure.

The affected Proxmox LXC network settings used IPv6 DHCP in an environment without a usable DHCPv6 response. Changing `ip6=dhcp` to `ip6=manual` restored normal IPv4 service; a local `dig @127.0.0.1 google.com` query returned `NOERROR`, and DNS resolution recovered.

This is a **verified recovery for the observed incident**, not a complete cold-boot, lease-renewal, or DNS redundancy test. Pi-hole remains a single DNS dependency.

---

### DNS Not Updating on Desktop

Problem:

The desktop was still using the old ISP DNS servers after configuring Eero Custom DNS.

Symptoms:

- Pi-hole was installed and running.
- Eero Custom DNS was configured.
- DNS configuration still showed the previous servers.
- Ad blocking was not initially working.

Investigation:

Checked DNS configuration using:

`resolvectl status`

Solution:

Disconnected and reconnected the Ethernet connection.

Result:

The desktop began using the updated DNS configuration and Pi-hole blocking worked.

---

### Joplin Invalid Origin

Problem:

Joplin returned an Invalid origin error when accessed using the new local hostname.

Error (sanitized):

`Invalid origin: http://joplin.example.internal:22300`

Cause:

The Joplin application URL did not match the hostname being used to access the server.

Solution:

Updated the Joplin Docker Compose configuration located at:

`/opt/joplin/compose.yaml`

Changed the application configuration to the following sanitized equivalent:

`APP_BASE_URL=http://joplin.example.internal:22300`

The Compose configuration required sudo permissions to edit.

Applied the changes using Docker Compose.

Result:

Joplin worked using the new local hostname.

---

## Troubleshooting Flow

If Pi-hole stops working:

1. Confirm Proxmox is running.
2. Confirm CT104 is running.
3. Check the Pi-hole FTL service.
4. Confirm DNS port 53 is listening.
5. Confirm the container has network connectivity.
6. Confirm the configured upstream DNS is reachable.
7. Verify the Eero Custom DNS settings.
8. Check DNS configuration on the client.
9. Test DNS resolution.
10. Review Pi-hole logs if the problem continues.

This separates problems into:

- Proxmox
- LXC
- Network
- DNS
- Pi-hole FTL
- Router configuration
- Client configuration

---

## Startup and Recovery

CT104 is configured to start automatically with Proxmox.

Expected startup sequence:

1. Proxmox boots.
2. CT104 starts automatically.
3. Debian starts.
4. Pi-hole FTL starts.
5. DNS filtering becomes available.
6. Network clients can resolve DNS requests through Pi-hole.

Because Pi-hole now provides DNS for the home network, its availability is more important than a normal optional application.

A failed Pi-hole container could prevent dependent devices from resolving domain names.

Automatic startup has been configured, but recovery following a full host restart still needs to be tested.

---

## Security

Current security considerations include:

- Pi-hole is hosted on the local network.
- Administrative access requires authentication.
- DNS is intended for trusted local network devices.
- DNS port 53 should not be publicly exposed.
- Sensitive information is excluded from public documentation.
- Internal IP addresses are sanitized.
- Internal DNS names use fictional placeholders.
- The web interface currently uses local HTTP.
- Remote administration has not yet been configured.

---

## Backup Considerations

Important configuration includes:

- Pi-hole settings
- Local DNS records
- Blocklists
- Custom allowlists and denylists
- CT104 configuration
- Upstream DNS settings

Pi-hole supports exporting configuration data through its backup features.

### Current Backup Status

No verified automated Pi-hole backup process has been implemented.

Important settings should be backed up before major changes or application upgrades.

---

## Current Limitations

- File-server and Pi-hole LXC networking recovered from DHCPv6-related startup timeouts, but long-term lease/reboot reliability remains unverified.
- Pi-hole is currently a single DNS server.
- No secondary local DNS server has been configured.
- Recovery after a full Proxmox restart has not been tested.
- Behavior during a Pi-hole outage has not been tested.
- Automated Pi-hole configuration backups are not implemented.
- Automated DNS availability monitoring is not implemented.
- Mobile and additional client testing is still needed.
- DHCP reservation or otherwise persistent addressing for CT104 needs verification.
- DNS filtering does not block every advertisement.

---

## Planned Improvements

- Verify consistent DNS filtering across devices
- Confirm persistent Pi-hole addressing
- Configure Pi-hole backups
- Test DNS recovery after a Proxmox restart
- Test DNS behavior when Pi-hole is unavailable
- Investigate secondary DNS redundancy
- Configure DNS monitoring and alerts
- Review blocklist effectiveness
- Monitor DNS query performance
- Investigate secure remote administration
- Document any future DNS configuration changes

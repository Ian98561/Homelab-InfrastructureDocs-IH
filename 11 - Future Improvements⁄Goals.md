# Future Improvements & Goals

## Overview

This section tracks planned improvements and projects for the homelab.

The goal is to gradually improve reliability, security, networking, storage, monitoring, and self-hosted services.

Projects are added as the environment grows.

* * *

## Current Projects

### GameServer1

- [x] Create dedicated Debian VM
- [x] Install SteamCMD
- [x] Install Valheim Dedicated Server
- [x] Configure world and server settings
- [x] Configure persistent world storage
- [x] Set up SSH access
- [x] Test local networking
- [x] Configure Playit.gg
- [x] Test PlayFab crossplay
- [ ] Resolve remaining external connection problems
- [ ] Improve connection latency
- [ ] Configure automatic Valheim startup through systemdTBD
- [ ] Configure automatic restart after failures
- [ ] Configure regular world backups
- [ ] Test recovery after a Proxmox reboot
- [ ] Investigate Discord-controlled server startup
- [ ] Investigate automatic shutdown when the server is empty

### Joplin Server

- [x] Create dedicated Debian VM
- [x] Configure SSH access
- [x] Install Docker and Docker Compose
- [x] Configure PostgreSQL
- [x] Deploy Joplin Server
- [x] Create separate administrator and personal accounts
- [x] Configure laptop and desktop synchronization
- [x] Enable end-to-end encryption
- [x] Import Homelab documentation
- [ ] Verify Markdown and embedded image rendering
- [ ] Investigate database and keychain warnings
- [ ] Configure secure HTTPS remote access
- [x] Enable VM automatic startup
- [ ] Test VM recovery after Proxmox reboot
- [ ] Configure PostgreSQL backups
- [ ] Test complete restoration
- [ ] Finalize GitHub documentation export workflow

### Pi-hole

- [x] Create dedicated Debian LXC container
- [x] Install Pi-hole
- [x] Configure Cloudflare upstream DNS
- [x] Configure Eero Custom DNS
- [x] Verify DNS filtering on desktop
- [x] Create local DNS records
- [x] Configure custom internal domain
- [x] Test Joplin with local hostname
- [ ] Verify Pi-hole on additional devices
- [ ] Confirm persistent Pi-hole addressing
- [ ] Configure Pi-hole backups
- [ ] Test DNS recovery after Proxmox reboot
- [ ] Investigate secondary DNS redundancy
- [ ] Configure automated DNS monitoring

### Documentation

- [x] Establish documentation folder structure
- [x] Create initial Overview
- [x] Document hardware
- [x] Document network architecture
- [x] Document Proxmox
- [x] Document ZFS
- [x] Document File Server
- [ ] Complete updated Immich documentation
- [ ] Complete updated Joplin documentation
- [ ] Complete GameServer1 documentation
- [ ] Update all current documentation
- [ ] Sanitize documentation for GitHub
- [ ] Commit and push updated Markdown files
- [ ] Continue daily logs
- [ ] Add updated network diagrams

* * *

## Network Improvements

- [ ] Establish a more structured network configuration
- [ ] Investigate VLAN segmentation
- [ ] Separate infrastructure and client networks
- [ ] Separate IoT and security cameras
- [x] Implement Pi-hole
- [x] Configure local DNS
- [ ] Improve hostname-based access
- [ ] Configure secure remote administration
- [ ] Continue testing ISP CGNAT workarounds
- [ ] Improve external game server connectivity
- [ ] Replace long patch cable runs with Cat6 drops

* * *

## Proxmox Improvements

- [ ] Establish regular maintenance procedures
- [ ] Verify automatic startup for all guests
- [ ] Configure startup order where needed
- [ ] Implement automatic guest backups
- [ ] Improve resource monitoring
- [ ] Add additional Linux testing environments
- [ ] Test guest recovery procedures
- [ ] Document VM and LXC creation procedures

* * *

## ZFS Improvements

- [ ] Configure scheduled scrubs
- [ ] Implement automated snapshots
- [ ] Establish snapshot retention
- [ ] Configure SMART monitoring
- [ ] Configure pool health notifications
- [ ] Monitor storage usage
- [ ] Implement off-system backups
- [ ] Test disk replacement procedures
- [ ] Test file recovery from snapshots

* * *

## File Server Improvements

- [ ] Implement automated snapshots
- [ ] Improve backup procedures
- [ ] Improve hostname-based access
- [ ] Establish better permission documentation
- [ ] Add monitoring and alerts
- [ ] Test restoration procedures
- [ ] Improve secure remote access

* * *

## Immich Improvements

- [ ] Establish PostgreSQL backups
- [ ] Configure off-system media backups
- [ ] Implement automated snapshots
- [ ] Improve monitoring
- [ ] Verify update procedures
- [ ] Test complete restoration
- [ ] Monitor storage growth
- [ ] Document exact storage and Docker configuration

* * *

## Monitoring Improvements

- [ ] Implement centralized monitoring
- [ ] Monitor host CPU and RAM
- [ ] Monitor individual virtual machines
- [ ] Monitor containers
- [ ] Monitor ZFS health
- [ ] Monitor SMART disk data
- [ ] Set up service availability checks
- [ ] Configure alert notifications
- [ ] Establish regular maintenance checks

* * *

## Backup & Recovery Improvements

- [ ] Establish Proxmox backup schedule
- [ ] Implement ZFS snapshots
- [ ] Configure PostgreSQL backups
- [ ] Back up Valheim worlds
- [ ] Back up Joplin data
- [ ] Back up Immich media and database
- [ ] Create off-system backups
- [ ] Test restoring individual files
- [ ] Test restoring containers- Verify Proxmox automatic startup

- [ ] Test restoring virtual machines
- [ ] Document full disaster recovery procedures

* * *

## Hardware Improvements

- [ ] Install or repair UPS
- [ ] Improve power protection
- [ ] Improve server closet cooling
- [ ] Add PoE security cameras
- [ ] Install permanent Cat6 drops
- [ ] Improve cable management
- [ ] Replace aging hardware as needed

* * *

## Long-Term Goals

- Improve reliability and recovery
- Reduce manual maintenance
- Improve network security
- Expand Linux administration experience
- Learn more about infrastructure automation
- Build and maintain useful self-hosted services
- Develop stronger troubleshooting skills
- Continue documenting technical projects
- Use the homelab as a portfolio for future employment

* * *

## Project Brainstorming/Additions

- TailScale
- JellyFin
    - Maybe custom IPTV with VLC player for future projects
    - Rip owned CDs for content plus "ISO" extract way
- HomeVPN Purchase
- Personal Website
    - CloudFlare domain purchase / Static IPv4 Purchase
- HomeAssistant for Personal Cameras
    - Have Purchased ReoLink Doorbell Camera waiting for install at home  
        - Tough Install because front door is not already wired
- NBA Spurs Game Stream Site
    - Game Calendar + Notifications
    - Stream Site Integration
    - Discord Integration with custom watch together party
    - Personal Use or TailScaled in for privacy
- Random Album Recommendation
    - Ratings, shuffle by genre, shuffle by not including specific genre, shuffle by obscurity yet highly rated like RYM, shuffle by year if possible
- Discord Clip Bot
    - Longer custom clips
    - joins VC and says clip and leaves
- Discord ScreenShare Clipping
    - Everyone can send clips to it and it could share them to server
    - /clip NAME - creates clips with custom name and stores for group chat
    - Look into screensharing clips instead of posting in chat
- FULL Network Rack Install/Fix
    - Currently Server is propped up on paint cans in office closet and switch/Router on shelf
    - Loose Patch Cables Dropped
    - Cat6 Runs to all Rooms
        - Actual walls look easy to fish
    - Rack/Patch Panel Purchase
    - Need Switch Rack Ears to mount it on rack
- Custom Wall Calendar Screen
    - Integrated w/ Joplin maybe
    - Large Screen with access from phone to edit calendar/home notes
    - Rasp Pi purchase/ Screen Purchase  
        - Focus on other needed projects first. Maybe this is a Post-Grad Type project when I have time
- Network+ Cert (Need Funds)
- Cloud+ Cert (Need Funds)
- A+ Cert (Need Funds)
- CCNA Cert (Need Funds)

# Cheap Home Lab Project
A detailed look at how I made a high availability homelab with $20 and a dream.
## Architecture Overview
The core network is routed through 2 Cisco 2901 routers set up for high availability into a Dell N1524P switch that connects to 2 server computers, a Dell Optiplex and a Dell PowerEdge R905. The R905 is running TrueNas Core while the Optiplex is running Proxmox to handle the DHCP, web server, and remote management with Tailscale.

```mermaid
flowchart TD
    WAN((Internet)) --> R1["Cisco 2901 Router<br/>Primary"]
    WAN((Internet)) --> R2["Cisco 2901 Router<br/>HA Backup"]
    
    subgraph core ["Core Network"]
        R1 --> SW1["Dell N1524P Switch"]
        R2 --> SW1
    end

    subgraph compute ["Compute & Storage"]
        SW1 --> OPT["Dell Optiplex<br/>Proxmox"]
        SW1 --> R905["Dell PowerEdge R905<br/>TrueNAS CORE"]
    end

    subgraph services ["Optiplex Services"]
        OPT -.-> DHCP["DHCP Server"]
        OPT -.-> WEB["Web Server"]
        OPT -.-> MGMT["Remote Management"]
    end
```
## Hardware Inventory

| Device | Role | Form Factor | CPU / Memory | Ports & Storage / Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Cisco 2901 (Primary)** | Core Router / Firewall | 1U Rackmount | 512MB DRAM | 2x 1GbE WAN, 4x EHWIC slots |
| **Cisco 2901 (HA Backup)**| Redundant Router | 1U Rackmount | 512MB DRAM | Configured for High Availability |
| **Dell N1524P** | Main Switch | 1U Rackmount | 1GB CPU Memory | 24x 1GbE PoE+ (30W), 4x 10GbE SFP+ |
| **Dell Optiplex** | Hypervisor (Proxmox) | Tower | Intel Core i7-4790 / 32GB DDR3 | 1x 1gbE LAN / Hosts DHCP, Web Server, & Mgmt VMs |
| **Dell PowerEdge R905** | NAS (TrueNAS CORE) | 4U Rackmount | 4x AMD Opteron 8384 / 128GB DDR2 | PERC 6/i RAID Controller, 2x 10gbE LAN / Basic storage device, stores ISOs for Optiplex |

## Why These Devices
If you are trying to replicate this, you have to be patient in finding any kind of networking equipment. Most of these devices came from an office shutting down and giving away their networking equipment and computers. The only computer I bought was the R905 for $20 on Facebook Marketplace. This may seem like luck but companies do get rid of old tech and list it on marketplaces quite frequently and it just takes time.

### Routers
The company gave me 2 routers that they were using to separate networks within their office but, at least for my use case, I don't need 2 different networks. One of the best ways to make a reliable network setup is to have high availability with 2 or more routers. This might seem a little overkill but having the best uptime sets you aside from others especially when self-hosting.

### Switch
The switch was also given to me by the company and works very well within this setup. It has 24 gbE ports with POE+ so if I wanted to add an access point, it would be very easy to do so. It is also a managed switch which is very helpful for setting up the VLANs and the uplink ports.

## Guides and Runbooks

* [Network Deployment & Cisco HA Setup](./network-deployment.md)
* [Server Trouble](./server-deployment.md)
* [Tailscale Deployment Guide for TrueNAS Core](./tailscale-deployment-guide.md)

## Roadmap & Future Plans

- [ ] Deploy a UPS (Uninterruptible Power Supply) and configure Proxmox and TrueNAS for graceful automated shutdowns.
- [ ] Set up an automated cron job to backup the Cisco 2901 HA configurations directly to a TrueNAS SMB share.
- [ ] Implement a reverse proxy (e.g., Nginx Proxy Manager) to route local domain names to the Optiplex web services.
- [ ] Upgrade the Dell N1524P SFP+ uplinks to 10GbE and add 10gbE card to the Optiplex to make a true 10gb Networking setup.

## Acknowledgments

* The [r/homelab](https://reddit.com/r/homelab) and [r/networking](https://reddit.com/r/networking) communities for architecture inspiration.
* The [TrueNAS Forums](https://forums.truenas.com/) for guidance on FreeBSD jail networking and Tailscale routing.
* [Tailscale](https://tailscale.com/) for making the coolest networking project ever.

## Author

**Mason Blick**  
* GitHub: [@mblick4](https://github.com/mblick4)
* LinkedIn: [Mason Blick](https://www.linkedin.com/in/mason-blick-78747a43b/)

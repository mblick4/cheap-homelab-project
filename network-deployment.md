# Network & Server Deployment Guide

This is an oversimplification of what I did to make my exact setup from scratch.

## Phase 1: Core Routing (Cisco 2901s)

The dual Cisco 2901 routers serve as the foundation of the network. They must be configured first to establish the gateway and High Availability (HA) protocol.

1. **Physical Connection:** Connect to the primary Cisco 2901 console port using a mini-B USB cable.
2. **Terminal Session:** Open PuTTY (or your preferred terminal) and connect at **9600 baud**.
3. **Initial Configuration & Recovery:** 
   * *Note: If locked out of a second-hand unit, send a break signal during bootstrap into ROMMON, execute `confreg 0x2142`, and reload to bypass the old startup-config.*
   * Configure the console login and enable secrets.
   * Save the new configuration to memory (`copy running-config startup-config`).
4. **HA Setup:** Configure HSRP (Hot Standby Router Protocol) or VRRP between the primary and backup 2901 routers to ensure failover redundancy.

## Phase 2: Core Switching (Dell N1524P)

Once the gateway is established, the Dell switch is configured to distribute traffic and handle VLANs.

1. **Console Access:** Connect via the serial console at **9600 baud** (8 data bits, 1 stop bit, no parity/flow control).
2. **Remote Management Setup:**
   * Generate crypto keys for SSH: `crypto key generate rsa/dsa`
   * Enable the SSH server: `ip ssh server`
   * Enable the web GUI (optional): `ip http secure-server`
3. **Trunking & VLANs:** 
   * Configure the uplink ports to the Cisco 2901s.
   * Provision standard access ports for the Optiplex and the PowerEdge R905.

## Phase 3: Compute & Storage Provisioning

With the network layer active, the servers can be brought online and assigned static IPs.

1. **TrueNAS CORE (Dell PowerEdge R905):**
   * Boot the R905 and configure the PERC 6/i RAID controller via the Dell BIOS.
   * Install TrueNAS CORE.
   * Assign a static IP from the Dell N1524P and configure SMB/NFS shares to act as the primary storage backend.
2. **Proxmox Hypervisor (Dell Optiplex):**
   * Install Proxmox on the Intel i7-4790 Optiplex.
   * Mount the TrueNAS CORE shares from the R905 as network storage for ISOs and backup images.
3. **Core Services Deployment:**
   * Within Proxmox, deploy the virtual machines or containers that will handle foundational network services:
     * **DHCP Server**
     * **Web Server**
     * **Remote Management Tools**

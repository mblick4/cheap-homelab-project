# Tailscale Deployment on TrueNAS CORE

This guide details the deployment of Tailscale within a TrueNAS CORE jail. It includes the necessary configurations for subnet routing and static routes to ensure you can access the TrueNAS host web GUI and other local devices remotely.

## Step 1: Create the Tailscale Jail

We must create a jail with specific networking properties to allow Tailscale to create its virtual network interface.

1. Navigate to **Jails** > **Add** > **Advanced Jail Creation**.
2. **Name:** `tailscale`
3. **Release:** Choose the latest available (e.g., 13.x-RELEASE).
4. **VNET:** Check this box (Do NOT use NAT).
5. **IPv4 Address:** Assign a static IP address on the **same subnet** as your TrueNAS host, but a **different IP** (e.g., if TrueNAS is `192.168.1.50`, use `192.168.1.51`).
6. **Custom Properties:** Scroll down and check the box for **`allow_tun`**. *This is required for Tailscale to function.*
7. Save and start the jail.

## Step 2: Install Tailscale

Open the shell for your new `tailscale` jail from the TrueNAS GUI and run the following commands.

Ensure you are pulling the latest packages by editing the FreeBSD repo file:
```bash
sed -i '' 's/quarterly/latest/g' /etc/pkg/FreeBSD.conf
pkg update
```

Install the Tailscale package:
```bash
pkg install tailscale
```

Enable and start the service so it runs on boot:
```bash
sysrc tailscaled_enable="YES"
service tailscaled start
```

## Step 3: Authenticate and Advertise Routes

To access other devices on the network (like the Dell N1524P and Proxmox host), we will advertise the local subnet.

1. Run the following command in the jail shell (adjust the IP range if your main LAN subnet is different):
   ```bash
   tailscale up --advertise-routes=192.168.1.0/24
   ```
2. Copy the authentication link provided in the console output and open it in your web browser to log in.
3. Open the **Tailscale Admin Console** in your browser.
4. Find the TrueNAS jail machine in your device list, click the three dots (...), and select **Edit route settings**.
5. Toggle on the subnet route (`192.168.1.0/24`) you just advertised. 
6. *Recommended:* Select **Disable key expiry** for this machine so it doesn't disconnect after a few months.

## Step 4: Configure TrueNAS Host Routing

By default, the TrueNAS host itself won't know how to reply to incoming Tailscale traffic. We must add a static route telling TrueNAS to send Tailscale traffic through the jail.

1. In the TrueNAS web GUI, go to **Network** > **Static Routes** > **Add**.
2. **Destination:** `100.64.0.0/10` (This covers the entire Tailscale IP range).
3. **Gateway:** Enter the static IP address you assigned to the `tailscale` jail in Step 1 (e.g., `192.168.1.51`).
4. Save the route. 

You should now be able to securely access the TrueNAS CORE web interface and your other network devices using their standard local IP addresses while connected to your Tailscale VPN.

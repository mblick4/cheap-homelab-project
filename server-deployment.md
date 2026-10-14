# Server Troubles
## Operating System
One of the biggest problems I had when making this project was the limitations on the Poweredge R905. The AMD Opteron 8384 runs x86-64-v1 which is unsupported on just about any supported operating system. I had to get creative and find a legacy version of an operating system. I would have liked to use the 128gb of ram in order to run a virtual environment like Proxmox but I settled on TrueNAS Core since I still had the Optiplex that could run those containers.

## Fan Control
These rack mount servers are notorious for having some of the loudest fans you will ever hear even though, if you are deploying the server by itself, the fans are very overkill. To solve this noise issue, I removed the fans and replaced them with 4 120mm fans and connected them to a PWM power controller that takes in 12V USB-C and outputs 6 4-pin power connectors. You do have to run another cable outside of the case because the USB on the computer does not supply enough power so you will need an outside power source but it is much quieter.

## Storage
When I first loaded into TrueNAS Core, all of the drives showed up as one large drive which TrueNAS did not like. Booting into the Storage Management window shows that the server was doing hardware RAID. This does work but because I want to monitor the drives separately, I would rather they all show as different drives in TrueNAS and use software RAID. You can set up the drives as virtual disks in the Storage Management and then set up the RAID pool in TrueNAS Core.

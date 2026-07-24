<!-- 02-network-setup.md
Last Updated: June 30, 2026

 -->

**Create a Custom Host-Only Network**

This step is critical because it creates a dedicated isolated network for the lab VMs.

In VMware Fusion:
1. Open **VMware Fusion** → Menu bar → **VMware Fusion** → **Settings** (or Preferences)
2. Click **Network**
3. Click the lock icon at the botton left corner. You will be prompted to authenticate.
4. Click the **+** button to add a new network
5. Name it something like `vmnet2` (or it may auto-name)
6. **UNCHECK** "Allow virtual machines on this network to connect to external networks (using NAT)" — this is what creates the air-gap
7. **UNCHECK** "Connect the host Mac to this network" — prevents your Mac from routing into it
8. **UNCHECK** "Provide addresses on this network via DHCP" — we assign static IPs manually so every VM always has the same address
> **Note:** Depending on your system, you will not be able to set subnet IP when you uncheck **"Provide addresses on this network via DHCP"** so do one of the following options: 

**Option A:** 

Leave "Provide addresses via DHCP" checked — this keeps the subnet field editable so you can set subnet IP:  192.168.36.0 and subnet mask 255.255.255.0
Then inside each VM, configure a static IP manually in the guest OS network settings. e.g: 

``` text
- Kali - Attacker: static 192.168.36.10
- Security Onion - Defender: static 192.168.36.20
- Window server - victim: 192.168.36.30
- Window 10 - victim: 192.168.36.40
- Window 11 - victim: 192.168.36.50
- etc.
```

This gives you a defined subnet and static IPs per VM — while keeping Fusion's UI cooperative. The DHCP server existing but unused is harmless. This is the path of least resistance. 

> **Important** Options A could result into **IP conflicts**:

If every VM on this air-gap network is going to use a static IP, then DHCP never gets asked by anyone,

Your DHCP server is configured to hand out addresses from some range. Usually something like: `192.168.36.128–254`. Said range can be viewed in: /Library/Preferences/VMware Fusion/vmnet2/dhcpd.conf

> **Note:** vmnet2 is the custom or air-gapped network name you in **step 5**.

If you statically assign a VM an address inside that DHCP range (e.g., static 192.168.36.130), and later another VM asks DHCP for an address, DHCP might hand out .130 to the second VM — now two machines have the same IP. That's a conflict, resulting to address collision.

To avoid address collision, assign static IPs OUTSIDE the DHCP range. 


DHCP range: 192.168.36.128–254 (the upper half, left for DHCP)
Your statics: 192.168.36.10–99 (the lower range, safely outside DHCP)

**Option B:** Edit the network config files directly (full manual control):

If you want true manual host-side control, Fusion stores network config in files you can edit directly (bypassing the UI limitations):
The main files are typically:

`/Library/Preferences/VMware Fusion/networking`

and per-network:

`/Library/Preferences/VMware Fusion/vmnet2/`



You'd edit the networking file to define your custom vmnet with the exact subnet you want. This requires sudo, and after editing you restart Fusion's networking via the terminal:

```
sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --stop
sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --start
```

9. Set **Subnet IP**: `192.168.36.0`
10. Set **Subnet Mask**: `255.255.255.0`


> **Why disable DHCP?** DHCP automatically assigns IPs to devices on a network. If it is enabled, a VM might get `192.168.36.105` one day and `192.168.36.87` the next. Our attack tools are configured to target specific IPs, so we need them to never change. Static IPs = predictability.

<!-- 
# 🚧 Coming Soon

<p align="center">
  <img src="https://img.shields.io/badge/Status-Coming%20Soon-orange?style=for-the-badge" alt="Coming Soon">
</p>
-->

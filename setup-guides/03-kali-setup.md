<!-- 03-kalli-setup.md
Last Updated: June 30, 2026

 -->


# Kali Linux Virtual Machine Setup

## Overview

This guide walks through creating and configuring a Kali Linux virtual machine using VMware Fusion. Kali Linux will serve as the primary attack workstation within this lab and will be used to perform penetration testing, command and control (C2) operations, payload generation, and network analysis.

The virtual machine will be connected exclusively to the lab's **Host-Only** network to ensure all activities remain isolated from the public Internet.

---

## Objectives

Upon completion of this guide, you will have:

- A functioning Kali Linux virtual machine
- VMware Tools installed
- A static IP address configured
- Network connectivity to other lab machines
- A clean snapshot ready for future exercises

---

## Prerequisites

Before beginning, ensure you have completed the following:

- [VMware Setup](setup-guides/01-vmware-setup.md)
- [Network Configuration](setup-guides/02-network-setup.md)
- At least **120 GB** of available storage is available.
- At least **16 GB** of physical RAM is installed (32 GB recommended for multiple VMs).
- The latest Kali Linux Installer ISO has been downloaded.



---

# Step 1 — Download Kali Linux

1. Visit the official Kali Linux Downloads page:
   https://www.kali.org/get-kali/#kali-installer-images
2. Download the latest **Apple Silicon (ARM64 bit) Installer ISO**.
![Kali iso Download Page](../screenshots/kali-setup/01-iso-selection.png)
3. Save the ISO to your **Downloads** folder or another known location.

Expected download:

```text
kali-linux-YYYY.X-installer-arm64.iso
```

> **Note**
>
> Only download Kali Linux from the official Kali website to ensure the installation media has not been modified.

---

# Step 2 — Create a New Virtual Machine

1. Open **VMware Fusion**.
2. From the menu bar, select **File → New**.
![image](../screenshots/kali-setup/02-file->new.png)
3. Select **Install from disc or image**.
![](../screenshots/kali-setup/03-install-from-disc-image.png)
4. Browse to the Kali Linux ISO you downloaded to drag and drop.
![](../screenshots/kali-setup/04-drag-iso.png) 
![](../screenshots/kali-setup/04-dragged-iso.png) |
5. Click **Continue**.
6. VMware Fusion should automatically detect **Linux** as the OS.
![](../screenshots/kali-setup/05-choose-os.png)
If prompted, choose **Linux** as the operating system and **Debian 13.x 64-bit Arm** (or the latest available version) as the guest operating system.

7. Click **Continue**. ![](../screenshots/kali-setup/06-click-sustomize.png)

---

# Step 3 — Configure Virtual Hardware

Before you click **Finish**, select **Customize Settings** and review the following configuration.

Configure the following:

| Component | Value |
|----------|------|
| CPUs | 2 |
| Memory | 4096 MB or greater |
| Hard Disk | 80 GB |

![](../screenshots/kali-setup/07-cpu-memory.png)

Click **Apply**.

![](../screenshots/kali-setup/08-disksize.png)


---

# Step 4 — Install Kali Linux

<!-- Start here -->

### Boot menu loads up: 
 
 - Select **Graphical install** (easier than text install; same result)
 ![](../screenshots/kali-setup/09-installer-method.png)
 - Language: English (or your preference)
 ![](../screenshots/kali-setup/10-language.png)
 
 - Location: United States (or your preference)
 ![](../screenshots/kali-setup/11-location.png)
 - Keyboard: American English (or your preference)
 ![](../screenshots/kali-setup/12-keyboard-selection.png)
 - Network — hostname:
 ![](../screenshots/kali-setup/13-network-hostname.png)
 
 > **Note:** leave as kali unless you want a custom name
 
 *→* Network —> domain name:

![](../screenshots/kali-setup/14-network-domain-name.png) 

> **Note:** Leave it blank — just hit Continue. You don't need a domain for a lab VM.
 
 ### Set up User Account:
 
*→* Full name for your account. (e.g. kali)
 ![](../screenshots/kali-setup/14-network-domain-name.png)
 
 *→* Username for your account:
 ![](../screenshots/kali-setup/15-username.png)

 > **Note:** Pick a username (e.g. kali or your handle) — this is your login name . Use something simple you'll remember
 
*→* Password for your account:
![](../screenshots/kali-setup/16-user-password.png)

  > **Note:** Set a strong password and write it down. Even in a lab, build the habit; a passphrase you'll remember but isn't trivial
 
- Select your timezone: 

 ![](../screenshots/kali-setup/17-timezone.png)
 
### Disk partitioning (the part people fear — keep it simple):
 
- Partitioning method:  "Guided - use entire disk"

 ![](../screenshots/kali-setup/18-partition-method.png)

  > **Note:** This is the right choice. It's a VM with a virtual disk — there's nothing else on it to protect, so let the installer handle everything automatically. Don't overthink this.

 - Select disk to partition: There's only one (your virtual disk) → select it

  ![](../screenshots/kali-setup/19-selecy-disk2partition.png)
 
 - Partitioning scheme: Choose **"All files in one partition (recommended for new users)"**

 > **Important:** Recommended for a lab VM. Simpler. The other options (separate /home, etc.) add complexity you don't need here.
 
  ![](../screenshots/kali-setup/20-partitioning-scheme.png)

   
 
 - Review disk partition Configuration: Select **"Finish partitioning and write changes to disk"** 
  ![](../screenshots/kali-setup/21-review-partition-configuration.png)

 - Write the changes to disks?
  > **⚠️ Note** This is the point of no return for the disk, but it's a fresh virtual disk so there's nothing to lose. Confirm Yes.

 ![](../screenshots/kali-setup/22-write2disk.png)


The base system installs now — takes a few minutes.



### Part 4 — Software selection (important choices):

- Software selection screen — this determines what gets installed. Recommended:
![](../screenshots/kali-setup/23-software2install.png)

> **Note** Hit Continue — this is the longest step as it downloads and installs the toolset. Be patient.

Part 5 — GRUB bootloader:
Install the GRUB boot loader? → Yes
Device for bootloader installation → Select the disk (it'll show something like /dev/vda or /dev/sda) — select the actual disk, not "Enter device manually"

Recommended: pick the listed disk entry


### Installation complete:

- Continue → the VM reboots

![](../screenshots/kali-setup/24-installation-complete.png)


# Step 5 — First Boot 

## To-dos (the clean version):

- Log in with the username/password you created. 

![](../screenshots/kali-setup/25-first-log-in.png)

- Click **Log In**
![](../screenshots/kali-setup/26-desktop-screen.png)

- Open the **terminal** and type the following and hit **Enter**, then type user password when requested:  

```bash
sudo apt update && sudo apt full-upgrade -y
```
![](../screenshots/kali-setup/27-open-terminal.png)

> **Note** This should be small and clean fresh install. 


- Gets your clipboard sharing and proper screen resizing.

```bash
 sudo apt install -y open-vm-tools open-vm-tools-desktop
```

![](../screenshots/kali-setup/28-vm-tools.png)

- Rebook the VM so changes can take effect: 

```bash
sudo reboot
```
![](../screenshots/kali-setup/29-vm-reboot.png)

Then immediately take a Snapshots of the clean state to revert to when you break something - I gurantee you, you will lol:

Fusion → Virtual Machine → Snapshots → Take Snapshot → name it Clean baseline

> **Important:** That snapshot is your insurance. Anytime you break something experimenting, revert to it in seconds. Take a new snapshot after each major setup milestone.

<!-- End Here -->
# Step 9 — Configure the Network

Verify the virtual machine is connected to the Host-Only adapter. 



Confirm the assigned IP address.

Example:

```text
192.168.100.10/24
```

Verify connectivity to the remaining lab systems once they have been deployed.

---

# Step 10 — Update the Operating System

If Internet access is temporarily enabled during the initial build, update Kali Linux before isolating the environment.

```bash
sudo apt update
sudo apt full-upgrade -y
```

After updating:

- Shut down the virtual machine.
- Disconnect Internet access.
- Reconnect the Host-Only adapter.

The remainder of this lab should operate entirely within the isolated environment.

---

# Step 11 — Create a Baseline Snapshot

Once the installation is complete and verified:

1. Shut down the virtual machine.
2. Create a VMware snapshot.

Suggested snapshot name:

```text
Fresh Kali Installation
```

This snapshot provides a clean restore point before installing offensive security tools or performing lab exercises.

---

# Verification Checklist

Confirm the following before proceeding:

- VMware Fusion boots the VM successfully
- Kali Linux loads without errors
- Host-Only networking is functional
- Static IP address configured
- VMware Tools installed
- System updated
- Baseline snapshot created

---

# Next Steps

Continue with the following guides:

- Windows 10 Victim Setup
- Windows Server Setup
- Security Onion Setup
- Host-Only Network Configuration

---

# Troubleshooting

## Virtual machine does not boot

- Verify the ISO was mounted correctly.
- Confirm the firmware is set to UEFI.
- Ensure sufficient RAM has been allocated.

---

## No network connectivity

- Verify the network adapter is configured as **Host-Only**.
- Confirm the correct virtual network is selected.
- Verify the assigned IP address.

---

## VMware Tools installation fails

- Confirm the VMware Tools ISO is mounted.
- Reboot the virtual machine.
- Retry the installation.

---

## References

- Kali Linux Documentation
- VMware Fusion Documentation

<!-- # 🚧 Coming Soon

<p align="center">
  <img src="https://img.shields.io/badge/Status-Coming%20Soon-orange?style=for-the-badge" alt="Coming Soon">
</p>
-->


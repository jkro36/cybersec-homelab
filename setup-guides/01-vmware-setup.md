<!-- 01-vmware-setup.md
Last Updated: June 29, 2026

 -->

# VMWare Setup (macOS):

## Overview

This guide walks through downloading, installing, and performing the initial configuration. VMware Fusion will be used as the hypervisor for this Lab to host multiple isolated virtual machines.

---

## Prerequisites

Before you begin, ensure your system meets the following requirements.

### Hardware

- Apple Silicon (M1, M2, M3, or newer) or Intel Mac
- Minimum 16 GB RAM (32 GB recommended)
- At least 500GB available disk space (1TB recommended)

### Software

- macOS (supported version)
- Administrator account

---

## Step 1 — Download VMware Fusion

1. Google **VMware Fusion Download** and select the first link or [Click here](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion) it should take you to the download page. 
2. Click **DOWNLOAD NOW**. or [Click here](https://support.broadcom.com/). You will be redirected to the Broadcom Support Portal.   
> **Note:** VMware Fusion is distributed by Broadcom,so creating a Broadcom account is required.
3. Verify your email address by following the instructions sent to your inbox.
> **Important:** Thi step is required before you can sign in.
4. [sign in.](https://access.broadcom.com/default/ui/v1/signin/)
5. Select the latest **VMware Fusion Pro** release.
6. Choose the installer that matches for your Mac:
   - **Apple Silicon** (M1, M2, M3, M4, M5, etc.)
   - **Intel**
7. Download the installer.

Expected download:

```text
VMware-Fusion-*.dmg
```

---

## Step 2  — Verify the Download

1. Open your **Downloads** folder.
2. Confirm that the VMware Fusion installer (`.dmg`) has finished downloading. 
**(Optional)**

If Broadcom provides a **SHA-256 checksum**, verify the integrity of the downloaded file before installing it.

---

## Step 3 — Install VMware Fusion

1. Double-click the downloaded `.dmg` file.
2. A Finder window will open displaying the VMware Fusion application.
3. Drag **VMware Fusion** into the **Applications** folder.
4.Wait for the copy to complete.

---

## Step 4 — Launch VMware Fusion

1. Open the Applications folder.
2. Launch VMware Fusion.
3. If prompted by Gatekeeper, select **Open**.
4. To save Fusion Pro in the Mac Dock, right-click the icon, and select OptionsKeep in Dock.

---

## Step 5 — Grant Permissions

Depending on your macOS version, VMware Fusion may request:

- System Extension approval
- Network permissions
- Full Disk Access

Grant each permission when prompted.

---

## Step 6 — Complete Initial Setup

After launching VMware Fusion:

- Accept the license agreement.
- Choose the default configuration.
- Complete the setup wizard.

---

## Step 7 — Verify Installation

Confirm VMware Fusion opens successfully.

Verify you can access:

- File → New
- Virtual Machine Library
- Preferences

---

## Next Steps

Continue with:

- [Network Configuration](setup-guides/02-network-setup.md)
- VM #1 - Attacker - [Kali Linux Setup](setup-guides/03-kali-setup.md)
- VM #2 - Defender - [Security Onion Setup](setup-guides/04-security-onion-setup.md)
- VM #3 Victim - [Windows 10 Setup](setup-guides/05-windows-victim-setup.md)

---

## Troubleshooting

### VMware will not open

- Verify macOS compatibility.
- Ensure VMware Fusion is located in the Applications folder.
- Restart macOS.

### Installer is blocked

Navigate to:

System Settings → Privacy & Security

Allow VMware Fusion.

---

## References

- [VMware Fusion Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/desktop-hypervisors/fusion-pro/13-0/using-vmware-fusion/getting-started-with-vmware-fusion/system-requirements-for-vmware-fusion.html)


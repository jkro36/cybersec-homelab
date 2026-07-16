<!-- 05-windows11-setup.md
Last Updated: July 15, 2026

 -->

# Windows 11 ARM Setup
> VMware Fusion on Apple Silicon (M1/M2/M3/M4/M5)

---

## Overview

This guide walks through the complete optimized setup of a Windows 11 ARM virtual machine on VMware Fusion for use in a cybersecurity home lab. The goal is a domain-joinable, attack-ready Windows 11 workstation that integrates with an Active Directory environment.

---

## Prerequisites

- VMware Fusion Pro [installed](setup-guides/01-vmware-setup.md).
- Windows 11 ARM ISO downloaded via Microsoft website or use VMware Fusion feature **`Get Windows from Microsoft`**. 
- Internet Connection (NAT or Bridge Mode)

![](../screenshots/windows11/ )
---
## Step 1 — Download and Create a New Virtual Machine

1. Open **VMware Fusion**.
2. From the menu bar, select **File → New**.
![image](../screenshots/kali-setup/02-file->new.png)
3. Select **Get Windows from Microsoft**.
![](../screenshots/windows11/02-get-windows-from-ms.png)
4. Click **Continue**.
![](../screenshots/windows11/03-dwnld-and-install.png)
5. Select Windows Edition: `Professional' and Language: `English (en-us)` (or your preference) 
![](../screenshots/windows11/04-edi-lang.png)
6. Select `UEFI` and check `UEFI Secure Boot`
![](../screenshots/windows11/05-firmware-type.png )
7. Choose VM Encrytion, and type in your password. 
![](../screenshots/windows11/06-vm-encryption.png)
8. Choose Virtual Disk, then Click `Continue`.
![](../screenshots/windows11/07-choose-virtual-disk.png)

---

# Step 2 — Configure Virtual Hardware

 Before you click **Finish**, select **Customize Settings** and review the following configuration.
![](../screenshots/windows11/08-customize-setting.png)

- Configure the following:

| Component | Value |
|----------|------|
| CPUs | 2 |
| Memory | 4096 MB or greater |
| Hard Disk | 80 GB |

![](../screenshots/windows11/09-cpu-memory.png)

10. Click **Apply**.

![](../screenshots/windows11/10-disksize.png)

# Step 3 — Install Windows 11

### Boot menu loads up: 

![](../screenshots/windows11/11-boot-menu.png )

If you see this screen instead, go to next step.
>
![](../screenshots/windows11/12-no-media.png)

You will need to manually select the iso from your file system: 

- From the menu bar, click `Virtual Machine` -> `CD/DVD (SATA)` -> `Choose Disc or Disc Image`.

![](../screenshots/windows11/13-go-to-iso.png)

- In your file system, go to: ~/Username/Virtual Machines/VMWIsoImages/Windows11_2...arm64.iso
![](../screenshots/windows11/14-select-iso.png)

- Click `Enter`. 

![](../screenshots/windows11/11-boot-menu.png )

Start the VM and follow the installation wizard:

- `Language:` English and `Time and Currency formart`: English (United States) (or your preference)

![](../screenshots/windows11/15-lang-and-time.png)

- click `Next`
- Select Keyboard settings
![](../screenshots/windows11/16-keyboard-input-method.png)

- Select setup option
![](../screenshots/windows11/17-setup-option.png)

- Enter Product Key if you have to activate the OS, or you can skip for the free version. Click `"I don't have a product key"`. Evaluation mode — fully functional. 
![](../screenshots/windows11/18-product-key.png)

- Image Edition Selection: `Windows 11 Pro
   (NOT Home — Pro is required for AD domain joining)
   → Click Next`
![](../screenshots/windows11/19-image-selection.png)

- Click `Accept` Software license terms
![](../screenshots/windows11/20-license-terms.png
)
- Select the disk to install the OS, then click `Next`. 
![](../screenshots/windows11/21-disk-partition.png)

- Leave everything as it is, and click `Install`. 
![](../screenshots/windows11/22-ready-to-install.png)

- Installation begins. 
   Takes 10 to 20 minutes. 
   ![](../screenshots/windows11/23-windows-installing.png)

- VM restarts automatically once done.
![](../screenshots/windows11/24-installing-in-progress.png)

### Complete OS Setup

- Country: select United States (or your preference) then click `Yes`
![](../screenshots/windows11/25-country-region.png)

- Keyboard Layout:    US (or your preference) then click `Yes`
![](../screenshots/windows11/26-keyborard-layout.png)

- Second Layout:      Skip
![](../screenshots/windows11/27-second-keybd-layout.png)

## User Account Setup

> ***Note:*** On the next screen, you will be prompted to login with Microsoft account. We will skip that for now and use the local account. If you do not see the "I don't have internet" option, it is because your VM still has internet, use the below steps to bypass MS Account Login.

### Bypass Microsoft Account (Important)

**Method 1. Disconnect Network:**
- Disconnect the NAT Network: From the menu bar, select `Virtual Machine` -> `Network Adapter` -> `Disconnet Network Adapter`
![](../screenshots/windows11/28-disconnect-network.png)

**Method 2. Command Prompt Bypass:**

- Press: `Shift + F10` to open CMD window.

- Type:  oobe\bypassnro

- Press: Enter
![](../screenshots/windows11/29-oobe-bypassnro.png)

- VM will restarts, and you should see `I don't have internet option` appears on the next screen. 

- Then select: `I don't have internet option`.
![](../screenshots/windows11/30-idonthaveinternet.png)




![](../screenshots/windows11/ )
![](../screenshots/windows11/ )

---

### Local User Account setup:


- PC Name: `WIN11-LAB`  (or your preference)
- Username: `admin` (or your preference)
- Password:           XXXXXXXXXXX
   > **Important:** write this down, it is your local user password and you will need it to login.
   
   ![](../screenshots/windows11/31-enter-password.png)

   - Confirm your password: 
   ![](../screenshots/windows11/32-confirm-password.png)

- Privacy Settings — Turn ALL OFF:
   ```
   - Location
   - Find my device
   - Diagnostic data
   - Inking and typing
   - Tailored experiences
   - Advertising ID
   ```
   ![](../screenshots/windows11/33-privacy-setting.png)

- Click `Accept` and the following loading screens will appear. 
![](../screenshots/windows11/34-getting-things-ready.png)

![](../screenshots/windows11/35-this-might-take-few-min.png)
- You will be taking to the `Desktop` for the first time. 
![](../screenshots/windows11/36-first-time-desktop.png)

---

## Step 4. First Boot  Immediate Actions

Complete these steps immediately after reaching the desktop.

### Pause Windows Update
```
Click `Start' -> Settings -> Windows Update → Pause Updates
→ Pause for 5 weeks
```
![](../screenshots/windows11/37-pause-updates.png)

## Step 5 — Install VMware Tools

Critical for performance and integration. VMware Tools provides:
- Better display resolution
- Improved mouse integration
- Better network performance
- Shared clipboard (use carefully in lab)

- Go to the menu bar, and click: `Virtual Machine` -> `Install VMware Tools`
![](../screenshots/windows11/38-vmware-tool.png)

- When prompted, click `Install` to connect the `VMware Tools Install CD`
![](../screenshots/windows11/39-vmware-tools-installer-cd.png)


- Inside Windows VM:
   - Open File Explorer
   - DVD Drive with VMware Tools appears
   ![](../screenshots/windows11/40-file-explore-with-cd.png)
   - Double click: `setup64.exe` 
   - then click `Yes`
   ![](../screenshots/windows11/41-install-popup.png)

- Complete installation
![](../screenshots/windows11/42-setup-type.png)
![](../screenshots/windows11/43-ready-to-install.png)
![](../screenshots/windows11/44-finish-installation.png)

- When prompted to restart: Click `Yes`
![](../screenshots/windows11/45-restart-to-changes.png)

---

## Step 6 — Snapshot Before First Boot

- Before starting or log back in the VM, take an initial snapshot:

- From the menu bar, click `Window` 
- Select `Virtual Machine Library`
- Right click the VM → Snapshots → Take Snapshot
![](../screenshots/windows11/46-vm-selection.png)

- Right click the `Current State`, and select `Take Snapshot`
![](../screenshots/windows11/47-rightclick-curr-state.png)

- Name it: `Fresh-State - Pre-Installation & Configuration`
![](../screenshots/windows11/48-name-snapshot.png)

- Snapshot done
![](../screenshots/windows11/49-snapshot-done.png)

![](../screenshots/windows11/)

> This is your most important snapshot. Restore here before each lab exercise.

---
## Step 7 — Join Active Directory Domain

<h1 style="color:red">Coming Soon</h1>



## Useful Verification Commands

Run these in PowerShell after setup to verify everything is correct:

```powershell
# Check Windows version
winver

# Check IP configuration
ipconfig /all

# Check domain membership
systeminfo | findstr /B /C:"Domain"

# Check current user
whoami

# List local users
net user

# Check SMB status
Get-SmbServerConfiguration | Select EnableSMB1Protocol, EnableSMB2Protocol

# Check firewall status
netsh advfirewall show allprofiles | findstr State

# Check Windows Defender status
Get-MpComputerStatus | Select RealTimeProtectionEnabled

# Check Secure Boot status
Confirm-SecureBootUEFI
```

---


## Troubleshooting

### VM will not boot after Secure Boot change?
```
Shut down VM completely
Virtual Machine → Settings → Advanced
Toggle Secure Boot off then back on
Start VM
```

---




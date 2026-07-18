# Windows 11 Setup (Victim Machine)

## Step 1. Set Static IP Address
```
Settings → Network & Internet → Ethernet → Edit IP assignment → Manual
→ IPv4: On

IP Address:     192.168.36.30
Subnet Mask:    255.255.255.0
Gateway:        192.168.36.1
DNS Primary:    192.168.36.30   ← Your Domain Controller IP
DNS Alternate:  8.8.8.8
→ Save
```

> **Important:** DNS must point to your Domain Controller before joining the domain.

---

## Step 2. Disable Security Features for Lab Use

> ⚠️ **Warning:** These steps deliberately weaken security for attack practice. Never perform these on a real or production machine.

### Disable Windows Defender
```
Windows Security → Virus & Threat Protection
→ Manage Settings
→ Real time protection:           OFF
→ Cloud delivered protection:     OFF
→ Automatic sample submission:    OFF
```

### Disable Windows Firewall
```powershell
# Open PowerShell as Administrator (right click Start → Terminal Admin)

netsh advfirewall set allprofiles state off

# Verify
netsh advfirewall show allprofiles
# Should show: State: OFF for all profiles
```

### Disable SmartScreen
```
Windows Security → App & Browser Control
→ Reputation based protection settings
→ Turn all SmartScreen options: OFF
```

### Enable SMBv1 (for Legacy Attack Techniques)
```powershell
# Enables vulnerability to older exploits (EternalBlue etc)
Enable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# Restart required after this command
```
---
## Step 3. Enable Remote Desktop
```
Settings → System → Remote Desktop
→ Toggle: On → Confirm
```

## ⚠️ Disclaimer

> This setup deliberately weakens security features for educational purposes only. All activities are performed in a completely isolated lab environment. Never apply these configurations to production or personal machines. Never use these techniques against systems you do not own or have explicit written permission to test.

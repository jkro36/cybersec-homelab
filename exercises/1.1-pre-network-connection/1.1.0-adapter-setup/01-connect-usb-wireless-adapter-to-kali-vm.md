
# Adapter Setup — Steps

**Date:** July 2026
**Status:** Complete
**Findings:** [findings.md](./findings.md)

---

## Environment

| Component | Details |
|-----------|---------|
| Machine | Kali Linux ARM64 — 10.0.0.10 |
| Hypervisor | VMware Fusion (Apple Silicon Mac) |
| Wireless adapter | [Your adapter model — e.g., Alfa AWUS036ACH] |
| Chipset | [Your chipset — e.g., RTL8812AU] |
| Interface name | wlan0 (confirm with `ip a` — may differ) |
| Snapshot taken | kali-pre-exercise-1.1-2026-07 |

---

## Prerequisites

- [x] Kali Linux VM running
- [x] USB wireless adapter physically connected to Mac
- [x] `aircrack-ng` suite installed: `sudo apt install aircrack-ng -y`
- [x] Snapshot taken before starting

---

## Step 1 — Pass the USB Adapter Through to the Kali VM

The Mac's built-in Wi-Fi adapter cannot be passed to a VM — macOS
manages it directly. The USB adapter connects to the USB bus, which
VMware Fusion can hand off entirely to Kali.

```
1. Plug the USB wireless adapter into the Mac.
2. In VMware Fusion menu bar:
   Virtual Machine → USB & Bluetooth → [your adapter name]
3. Select "Connect to [VM Name]"
4. Confirm the dialog if macOS prompts.
```

The adapter now belongs to Kali. macOS continues using its
built-in adapter for internet — unaffected.

---

## Step 2 — Verify the Adapter Is Visible in Kali

```bash
ip a
```

Look for a new interface — typically `wlan0` or `wlan1`.

**Expected output (abbreviated):**
```
1: lo: <LOOPBACK,UP,LOWER_UP> ...
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> ...
    inet 10.0.0.10/24 ...
3: wlan0: <BROADCAST,MULTICAST> mtu 1500 ...
    link/ether aa:bb:cc:dd:ee:ff brd ff:ff:ff:ff:ff:ff
```

`wlan0` appearing confirms Kali sees the adapter.

**If wlan0 does not appear:**
```bash
lsusb
```
If the adapter appears in `lsusb` but not `ip a`, the driver is
not loaded — check the chipset and install the matching driver.
If it does not appear in `lsusb` at all, USB passthrough did not
complete — repeat Step 1.

---

## Step 3 — Verify Monitor Mode and Packet Injection Support

Not all adapters support monitor mode or packet injection.
Verify before proceeding.

**Check monitor mode support:**
```bash
iw list 
```

**Look for the following expected output:**
```
Supported interface modes:
     * managed
     * AP
     * monitor        ← must be present
     * mesh point
```

If `monitor` is not listed, this adapter cannot be used for
wireless security testing. A different chipset is required.

---

## Step 4 — (Optional) Change the MAC Address

Changing the MAC address before scanning prevents your real adapter
MAC from appearing in access point logs.

```bash
# Bring the interface down:
sudo ip link set wlan0 down

# Set a new MAC address:
sudo ip link set wlan0 address 00:11:22:33:44:55

# Bring it back up:
sudo ip link set wlan0 up

# Verify:
ip link show wlan0
```

**Expected output:**
```
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> ...
    link/ether 00:11:22:33:44:55 ...
```

**If the MAC keeps resetting after commands:**

NetworkManager is restoring it automatically. Fix:

```bash
sudo vim /etc/NetworkManager/NetworkManager.conf
```

Add to the bottom:

```ini
[device]
wifi.scan-rand-mac-address=no

[connection]
ethernet.cloned-mac-address=preserve
wifi.cloned-mac-address=preserve
```

```bash
sudo systemctl restart NetworkManager
```

---

## Step 5 — Enable Monitor Mode

Three methods are documented. Method 3 (`airmon-ng`) is recommended
because it automatically handles processes that interfere with monitor mode.

### Method 1 — `iw` (most manual control)

```bash
sudo ip link set wlan0 down
sudo systemctl stop NetworkManager
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up
```

Verify:
```bash
iw dev
```
Look for `type monitor` under wlan0.

Return to managed mode when finished:
```bash
sudo ip link set wlan0 down
sudo iw dev wlan0 set type managed
sudo ip link set wlan0 up
sudo systemctl restart NetworkManager
```

---

### Method 2 — `iwconfig`

```bash
sudo ifconfig wlan0 down
sudo systemctl stop NetworkManager
sudo iwconfig wlan0 mode monitor
sudo ifconfig wlan0 up
```

Verify:
```bash
iwconfig wlan0
```
Look for `Mode:Monitor`.

Return to managed mode:
```bash
sudo ifconfig wlan0 down
sudo iwconfig wlan0 mode managed
sudo ifconfig wlan0 up
sudo systemctl start NetworkManager
```

---

### Method 3 — `airmon-ng` (recommended)

```bash
# Kill processes that interfere with monitor mode:
sudo airmon-ng check kill
```

This terminates NetworkManager, wpa_supplicant, and dhclient —
processes that hold the adapter open and block monitor mode.
Always run this before enabling monitor mode.

```bash
# Enable monitor mode:
sudo airmon-ng start wlan0
```

**Expected output:**
```
Found 2 processes that could cause trouble.
Killed: NetworkManager (PID 1234)
Killed: wpa_supplicant (PID 5678)

PHY     Interface   Driver      Chipset
phy0    wlan0       rtl88XXau   Realtek Semiconductor...

(mac80211 monitor mode vif enabled on [phy0]wlan0mon)
(mac80211 station mode vif disabled for [phy0]wlan0)
```

The interface may rename to `wlan0mon`. Use `wlan0mon` in all
subsequent commands if this happens.

Verify:
```bash
iwconfig wlan0mon
```
```
wlan0mon  IEEE 802.11  Mode:Monitor  Frequency:2.412 GHz
```

Return to managed mode when finished:
```bash
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```

---

## Step 6 — Verify Packet Injection

```bash
aireplay-ng --test wlan0mon
```

**Expected output:**
```
Trying broadcast probe requests...
Injection is working!
```

If injection fails, the adapter does not support it despite
showing monitor mode. A different adapter is needed for
exercises that require active packet injection (WEP, deauth).

---

## Step 7 — Configure the Frequency Band

**Check supported bands:**
```bash
iw list | grep -A 30 "Frequencies:"
```

2.4 GHz channels appear as 2412–2484 MHz.
5 GHz channels appear as 5180–5825 MHz.

**Set a specific frequency:**

```bash
# 2.4 GHz — Channel 6 (most common home router channel):
sudo ip link set wlan0mon down
sudo iw dev wlan0mon set freq 2437
sudo ip link set wlan0mon up

# 5 GHz — Channel 36:
sudo ip link set wlan0mon down
sudo iw dev wlan0mon set freq 5180
sudo ip link set wlan0mon up
```

**Verify:**
```bash
iw dev wlan0mon info
```

---

## Step 8 — Scan for Lab Networks

```bash
sudo airodump-ng wlan0mon
```

**Reading the output:**

```
BSSID              PWR  CH  ENC   AUTH  ESSID
AA:BB:CC:DD:EE:FF  -45   6  WPA2  PSK   lab-wpa2
BB:CC:DD:EE:FF:AA  -52  11  WEP         lab-wep
CC:DD:EE:FF:AA:BB  -61  36  WPA3  SAE   lab-wpa3
```

| Field | Meaning |
|-------|---------|
| BSSID | MAC address of the access point |
| PWR | Signal strength — closer to 0 is stronger |
| CH | Channel the AP broadcasts on |
| ENC | Encryption protocol |
| AUTH | Authentication method — PSK=password, SAE=WPA3, OPN=open |
| ESSID | Network name |

Note the channel for each lab network — used in every
protocol-specific exercise that follows.

**Scan both 2.4 GHz and 5 GHz simultaneously:**
```bash
sudo airodump-ng --band abg wlan0mon
```

Note: the adapter hops between frequencies when scanning both bands,
which means packets on one band may be missed while listening on the
other. For targeted capture in an exercise, lock to the specific
channel of the target AP.

---

## Next

Adapter is configured and verified.
Proceed to: [findings.md](./findings.md) to document what was observed.
Then begin protocol exercises starting with [../wep/steps.md](../wep/steps.md).
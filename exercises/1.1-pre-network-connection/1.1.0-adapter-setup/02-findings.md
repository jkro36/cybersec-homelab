# Adapter Setup — Findings

**Date:** July 2026
**Steps:** [steps.md](./steps.md)

---

## Findings Summary

Fill this in after completing the steps. Replace every bracketed
value with your actual results.

| Item | Result |
|------|--------|
| Adapter visible in Kali | Yes — [interface name, e.g., wlan0] |
| Monitor mode supported | Yes / No |
| Packet injection supported | Yes / No |
| MAC address change successful | Yes / No |
| Monitor mode method used | airmon-ng (Method 3) |
| Interface name after airmon-ng | [e.g., wlan0mon] |
| 2.4 GHz supported | Yes / No |
| 5 GHz supported | Yes / No |
| Lab networks visible in airodump-ng | Yes — [list SSIDs seen] |
| Lab AP channel (2.4 GHz) | [e.g., Channel 6] |
| Lab AP channel (5 GHz) | [e.g., Channel 36] |

---

## What I Observed During Setup

Write 3-5 sentences describing what actually happened when you
ran through the steps. Did anything fail first? Did the interface
name change after airmon-ng? Did any processes need killing?
Did the MAC change stick or require the NetworkManager fix?

This section should be specific to your run — not generic.

Example:
> When I ran `airmon-ng check kill`, it found and killed two
> processes: NetworkManager (PID 1847) and wpa_supplicant (PID 1923).
> After enabling monitor mode, the interface renamed from wlan0 to
> wlan0mon. The MAC change stuck without needing the NetworkManager
> config fix. airodump-ng immediately picked up three networks:
> lab-wep on channel 6, lab-wpa2 on channel 11, and lab-wpa3 on
> channel 36.

---

## Detection Analysis

### Monitoring Context

This exercise operates under the architectural constraint in
[ADR-001](../../docs/adr/ADR-001.md). Security Onion cannot
passively capture wireless traffic in the current lab topology.
Detection analysis uses the following approach:

- **Elastic Agent telemetry** — what endpoint logs captured on Kali
- **PCAP import** — what Security Onion found in the imported capture
- **What my attack put on the wire** — specific observable artifacts
  from the commands I ran, and what those look like in logs

---

### Elastic Agent — What Was Captured on Kali

Elastic Agent on Kali (10.0.0.10) logged the following process
and system events during this exercise:

```
Process started: airmon-ng — user: kali
Process started: airodump-ng — user: kali
Process killed:  NetworkManager (PID [fill in])
Process killed:  wpa_supplicant (PID [fill in])
Network interface state change: wlan0 → monitor mode (wlan0mon)
```

Elastic Agent does not process raw 802.11 frames. The above confirms
the exercise ran but provides no wireless-specific network telemetry.

---

### What My Attack Put on the Wire

This section is based on the specific commands I ran and what
they transmitted — not a generic description.

**`airodump-ng wlan0mon` — passive scan:**

Running airodump-ng in monitor mode causes the adapter to send
802.11 probe request frames. My adapter MAC at the time was
`[fill in — the MAC you used, spoofed or original]`. These
probe requests were transmitted from that MAC on every channel
the adapter hopped through.

Any access point within range would have logged:
```
Probe request received from: [your adapter MAC]
SSID requested: broadcast (empty — scanning all)
Signal strength: [varies by distance]
```

This is passive scanning behavior — the adapter is listening
but also passively announcing its presence via probes.

**`aireplay-ng --test wlan0mon` — injection test:**

The injection test sends a small number of broadcast probe frames
to verify that raw 802.11 frames can be transmitted. My adapter
sent broadcast probe requests from MAC `[fill in]` to verify
injection was functional.

Any WIDS monitoring the channel would have seen:
```
Probe request frames from [MAC] at unusually high rate
No subsequent association to any AP
Pattern consistent with injection test
```

---

### PCAP Analysis

```bash
# Start capture before running steps:
sudo tcpdump -i wlan0mon -w /tmp/adapter-setup-$(date +%Y%m%d).pcap

# Stop with Ctrl+C when steps are complete.

# Transfer to Security Onion:
scp /tmp/adapter-setup-[date].pcap admin@10.0.0.20:/tmp/

# Import into Security Onion:
so-import-pcap /tmp/adapter-setup-[date].pcap
```

**After import — document what Kibana showed:**

Replace the placeholder below with your actual Kibana findings
after running the import.

```
Suricata alerts from PCAP import:
  [Document any alerts that fired — or "No alerts generated.
   Passive scanning did not trigger any Suricata signatures."]

Zeek logs from PCAP import:
  conn.log:    [document connections observed]
  weird.log:   [document any anomalies Zeek flagged]
  notice.log:  [document any Zeek notices generated]
```

---

### What a Fully Monitored Environment Would See

Based specifically on what my commands transmitted:

**Probe requests from my adapter MAC:**
Any enterprise access point or WIDS controller in range
would log probe requests from `[your adapter MAC]`. If that
MAC is not in the authorized device list, it would generate:
```
Alert: Unknown device probing network
Source MAC: [your adapter MAC]
Action: Log and flag for investigation
```

**No association following probes:**
A device that sends many probe requests but never associates
with an AP is a behavioral anomaly. Enterprise WIDS (Cisco,
Aruba, Meraki) specifically look for this pattern as a
passive reconnaissance indicator.

**Injection test frames:**
The aireplay-ng test transmits broadcast probe frames at a
rate higher than normal client behavior. A WIDS with rate
analysis enabled would flag:
```
Alert: Anomalous probe request rate
Source MAC: [your adapter MAC]
Rate: [X] frames/second — above normal threshold
Possible tool: wireless injection test or attack tool
```

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Why it applies |
|--------|-----------|----|----------------|
| Discovery | Network Sniffing | T1040 | Monitor mode enabled to passively capture wireless frames |
| Discovery | System Network Configuration Discovery | T1016 | airodump-ng used to map visible networks, channels, and BSSIDs |

---

## Skills Demonstrated

- USB device passthrough from macOS to VMware Fusion guest VM
- Wireless adapter capability verification before use (`iw list`)
- MAC address spoofing via `ip link` and NetworkManager configuration
- Monitor mode configuration using three methods: `iw`, `iwconfig`, `airmon-ng`
- Conflicting process identification and termination (`airmon-ng check kill`)
- Packet injection verification (`aireplay-ng --test`)
- Frequency band configuration for targeted wireless capture
- Wireless network reconnaissance and output interpretation (`airodump-ng`)
- PCAP capture and Security Onion import workflow
- Honest documentation of monitoring limitations and compensating controls

---

## Remediation

What defends against the behavior demonstrated in this exercise:

**1. Wireless Intrusion Detection System (WIDS)**
Enterprise access points (Cisco, Aruba, Meraki) include built-in
WIDS that detect monitor mode behavior, rogue adapters, and
devices probing without associating. A WIDS on this network would
have flagged my adapter MAC within seconds of airodump-ng starting.

**2. Authorized device lists (MAC filtering)**
While MAC filtering alone is not a strong control (MACs are spoofable
as demonstrated in this exercise), combining it with WIDS creates
a detection layer: an unknown MAC probing the network triggers an alert
regardless of whether the MAC is spoofed or real.

**3. Minimizing wireless footprint**
Disabling legacy 802.11b/g rates reduces the attack surface for
older injection tools. Switching to 5 GHz only reduces the number
of devices that can monitor the network since fewer adapters support
5 GHz monitor mode.

**4. WPA3-only networks**
WPA3 SAE eliminates offline dictionary attacks against captured
handshakes — directly relevant to the WPA2 exercise that follows.
Even if an attacker captures traffic in monitor mode, WPA3
provides no crackable PSK to extract.

---

## References

- https://attack.mitre.org/techniques/T1040/
- https://www.aircrack-ng.org/documentation.html
- https://wireless.wiki.kernel.org/en/users/documentation/iw
# Network Diagram — v2 (Current)
**Status:** Active  
**Last Updated:** July 2026  
**Previous Version:** [network-diagram-v1.md](network-diagram-v1.md)  
**Change Log:** [ADR-001](./decisions/ADR-001-network-topology-change.md)

---

## Current Architecture Overview

All lab devices are connected to a Local Area Network (LAN) via bridged networking. Static IPs are assigned manually outside the network DHCP range to prevent IP conflicts. This is an interim solution pending acquisition of a dedicated network switch for full isolation.

---

## Current Network Diagram

```
Internet (WAN)
    │
Local Area Network (LAN)
Network DHCP Range: [DHCPRange: 10.0.0.99 - 10.0.0.253 — do not use IP in theis IP range for Static IP]
    │
    ├── Mac (Apple Silicon)
    │   ├── Host OS — internet access (LAN via DHCP)
    │   └── VMware Fusion — 
    │       │
    │       ├── Kali Linux - Bridged to LAN
    │       │   Role:      Attacker
    │       │   OS:        Kali Linux ARM64
    │       │   IP:        10.0.0.10 (static, outside DHCP range)
    │       │   Tools:     Metasploit, Sliver, Nmap, BloodHound
    │       │   Agent:     Elastic Agent (Fleet enrolled)
    │       │
    │       └── Windows 11 ARM VM
    │           Role:      Victim Workstation
    │           OS:        Windows 11 Pro ARM64
    │           IP:        10.0.0.31 (static, outside DHCP range)
    │           State:     Defender OFF, Firewall OFF, SMBv1 ON
    │           Domain:    lab.local (pending DC setup)
    │           Agent:     Elastic Agent (Fleet enrolled)
    │
    └── Intel x86/x64 Compatible Machine
        ├── Ethernet — Home network (connected to router)
        └── VMware Workstation — Bridged to home network
            │
            ├── Security Onion VM
            │   Role:      Defender / SIEM / Fleet Server
            │   OS:        Security Onion 2.4 (x86)
            │   IP:        10.0.0.20 (static, outside DHCP range)
            │   Receives:  Elastic Agent telemetry from all lab machines
            │   Receives:  Manual PCAP imports from lab exercises
            │   Does NOT:  Passively monitor entire network traffic
            │   Agent:     Elastic Agent (self monitored)
            │
            ├── Windows Server 2022 VM
            │   Role:      Primary Domain Controller
            │   OS:        Windows Server 2022 (x86)
            │   IP:        10.0.0.40 (static, outside DHCP range)
            │   Domain:    lab.local
            │   Agent:     Elastic Agent (Fleet enrolled)
            │
            └── Windows Server 2025 VM
                Role:      Secondary Domain Controller
                OS:        Windows Server 2025 (x86)
                IP:        10.0.0.41 (static, outside DHCP range)
                Domain:    lab.local (replicates from 2022)
                Agent:     Elastic Agent (Fleet enrolled)
```

---

## Device Inventory

| Device | Role | Host Machine | Hypervisor | OS | IP | Elastic Agent |
|---|---|---|---|---|---|---|
| Kali Linux | Attacker | Apple Silicon Mac | VMware Fusion | Kali ARM64 | 10.0.0.10 | Yes |
| Security Onion | Defender | Intel x86/x64 Machine | VMware Workstation | Security Onion 3.1.0 | 10.0.0.20 | Yes (self) |
| Windows 10 | Victim Workstation | Intel x86/x64 Machine| VMware Workstation | Win 10 x64 | 10.0.0.30 | Yes |
| Windows 11 | Victim Workstation | Apple Silicon Mac | VMware Fusion | Win 11 ARM | 10.0.0.31 | Yes |
| Windows Server 2022 | Primary DC | Intel x86/x64 Machine| VMware Workstation | WS 2022 x64 | 10.0.0.40 | Yes |
| Windows Server 2025 | Secondary DC | Intel x86/x64 Machine | VMware Workstation | WS 2025 x64 | 10.0.0.41 | Yes |

---

## IP Address Scheme

### Subnet
```
Lab Static IP Range: 10.0.0.0/24
```

### Static IP Assignments
```
10.0.0.10   Kali Linux ARM (Attacker)
10.0.0.20   Security Onion (Defender)
10.0.0.30   Windows 10 x64 (Victim Workstation)
10.0.0.31   Windows 11 ARM (Victim Workstation)
10.0.0.40   Windows Server 2022 (Primary DC)
10.0.0.41   Windows Server 2025 (Secondary DC)
```

> All IPs are assigned statically outside the Local Area Network DHCP range.  
> Check network DHCP settings to confirm your IP range. If your home network uses 10.0.0.x you will need to adjust this range.

### DNS Configuration
```
All lab machines DNS Primary:   10.0.0.30  (Windows Server 2022 DC)
All lab machines DNS Alternate: 8.8.8.8
```

---

## Security Monitoring Architecture

### Method 1 — Elastic Agent via Fleet (Primary)

```
Lab Machines (all)
        │
        └── Elastic Agent installed
                │
                └── Enrolled in Fleet
                        │
                        └── Security Onion Fleet Server (10.0.0.50)
                                │
                                ├── Endpoint logs
                                ├── Process telemetry
                                ├── Authentication events
                                ├── Network connection logs
                                ├── File integrity events
                                └── Kibana dashboard visualization
```

**Machines with Elastic Agent:**
- Kali Linux ARM (10.0.0.10)
- Security Onion itself (10.0.0.20)
- Windows 11 ARM (10.0.0.30)
- Windows 11 ARM (10.0.0.31)
- Windows Server 2022 (10.0.0.40)
- Windows Server 2025 (10.0.0.41)


**Machines without agents (intentionally):**
- All personal home network devices
- No automatic monitoring of non-lab machines

---

### Method 2 — PCAP Import (Secondary)

```
Lab Exercise Running
        │
        └── Capture traffic on relevant machine
                │
                └── Save as .pcap file
                        │
                        └── Import into Security Onion manually
                                │
                                ├── Suricata analysis
                                ├── Zeek log generation
                                └── Full packet inspection
```

**PCAP capture commands:**

```bash
# On Kali — capture traffic during an exercise
sudo tcpdump -i eth0 -w /tmp/exercise-01-c2.pcap

# Stop capture with Ctrl+C when exercise complete
# Transfer pcap to Security Onion for import

# Transfer via SCP
scp /tmp/exercise-01-c2.pcap admin@10.0.0.20:/tmp/

# Import into Security Onion
# Security Onion Console → PCAP Import → Upload file
```

---

## VMware Network Adapter Settings

### Mac (VMware Fusion)
```
Kali VM:        Network Adapter → Bridged → Autodetect
Windows 11 VM:  Network Adapter → Bridged → Autodetect
```

### Intel x86 Machine (VMware Workstation)
```
Security Onion:      Network Adapter → Bridged → Intel Ethernet (auto)
Windows Server 2022: Network Adapter → Bridged → Intel Ethernet (auto)
Windows Server 2025: Network Adapter → Bridged → Intel Ethernet (auto)
```

---

## Communication Verification

Run these tests to confirm all devices can communicate before starting lab exercises:

```bash
# From Kali VM — ping all lab devices
ping -c 4 10.0.0.20    # Security Onion
ping -c 4 10.0.0.30    # Windows 10 
ping -c 4 10.0.0.31    # Windows 11 
ping -c 4 10.0.0.40    # Windows Server 2022
ping -c 4 10.0.0.41    # Windows Server 2025


# From Windows 11 (PowerShell)
ping 10.0.0.10         # Kali
ping 10.0.0.40         # Windows Server 2022
ping 10.0.0.20         # Security Onion

# From Security Onion
ping 10.0.0.10         # Kali
ping 10.0.0.31         # Windows 11
ping 10.0.0.40         # Windows Server 2022
```

---

## Known Limitations

| Limitation | Impact | Mitigation |
|---|---|---|
| No passive packet capture | Cannot auto-capture all lab traffic | PCAP import workflow per exercise |
| Lab traffic on Local Area Network | Attack traffic on LAN subnet | Static IPs scoped to lab range, controlled exercises |
| No network isolation | Less realistic enterprise environment | Elastic Agent Fleet compensates |
| Other non-lab devices on same subnet | Risk of accidental targeting | Lab IPs clearly defined and scoped |

---

## Subnet History

| Version | Subnet | Notes |
|---|---|---|
| v1 — Original plan | 192.168.36.0/24 | Planned isolated lab network |
| v2 — Current | 10.0.0.0/24 | Static IPs outside LAN DHCP range |

---

## Planned Future Architecture (v3)

When network switch is acquired this will be upgraded to a fully isolated lab network:

```
LAN — internet access for Mac personal use only
    │
    └── Mac LAN WiFi (personal use, no lab traffic)

Dedicated Lab Switch (192.168.36.0/24) — Isolated, no internet
    │
    ├── Mac (via USB Ethernet adapter)
    │   ├── Kali VM        192.168.36.10
    │   └── Windows 11 VM  192.168.36.31
    │
    └── Intel x86 Machine (via Ethernet)
        ├── Security Onion  192.168.36.20
        │   └── Passive monitoring interface (span/mirror)
        ├── Win Server 2022 192.168.36.40
        ├── Win Server 2022 192.168.36.30
        └── Win Server 2025 192.168.36.41
```

**Additional capability in v3:**
- Security Onion passive monitoring interface on the switch
- Full packet capture of all lab traffic without manual PCAP import
- Elastic Agent Fleet retained as additional endpoint telemetry layer
- True network isolation — no lab traffic on home network

**Hardware needed:**
- Switch with capability to handle lab requirements.
- USB to Ethernet adapter for Mac.

---

## Version History

| Version | Date | Change |
|---|---|---|
| v1 | June 2026 | Original air-gapped design — planned only |
| v2 | July 2026 | Bridged Local Area Network, static IPs 10.0.0.0/24, Elastic Agent Fleet approach |
| v3 | TBD | Dedicated isolated lab network with passive monitoring |

---

## Related Documents

- [ADR-001 — Network Topology Change](./decisions/ADR-001-network-topology-change.md)
- [Network Diagram v1 — Original Plan](network-diagram-v1.md)
- [Elastic Agent Installation Guide](../setup-guides/elastic-agent-setup.md)
- [PCAP Import Workflow](../setup-guides/pcap-import-workflow.md)
- [Windows 11 Setup Guide](../setup-guides/05-windows11-setup.md)
- [Security Onion Setup Guide](../setup-guides/04-security-onion-setup.md)
- [Fleet Configuration Guide](../setup-guides/fleet-setup.md)

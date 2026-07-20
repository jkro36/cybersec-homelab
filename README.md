# Cybersecurity Home Lab

### Overview
A multi-VM cybersecurity research environment built across two physical machines separated by architecture compatibility. This lab simulates real-world attack and defense scenarios to develop practical offensive and defensive security skills.
 
> **Note:** The original design called for a fully isolated air-gapped single-host environment. Architecture constraints encountered during implementation required a redesign. See [ADR-001](architecture/decisions/ADR-001-network-topology-change.md) for the full context, options considered, and decisions made.
 
---

## Lab Architecture
 
### Machine 1 — Apple Silicon Mac
- **Hypervisor:** VMware Fusion
- **Role:** Attack and ARM victim workstation
- **Network:** Bridged Mode
### Machine 2 — Intel x86/x64 Compatible Machine
- **Hypervisor:** VMware Workstation
- **Role:** Windows infrastructure and security monitoring
- **Network:** Bridged Mode
### Network
- **Topology:** Bridged (interim — isolated switch planned)
- **Subnet:** 10.0.0.0/24
- **IP Assignment:** Static, outside home router DHCP range

### VMs

```
Apple Silicon Mac (VMware Fusion)
├── Kali Linux - Attacker (C2 Server)- -  - 10.0.0.10
└── Windows 11 — Victim Workstation -  -  - 10.0.0.31
 
Intel x86/x64 Machine (VMware Workstation)
├── Security Onion — Defender / SIEM  - - - 10.0.0.20
├── Windows Server 2022 — Primary DC- - - - 10.0.0.40
├── Windows Server 2025 — Secondary DC- - - 10.0.0.41
└── Windows 10 — Victim Workstation - - - - 10.0.0.30
```
---
## Security Monitoring Approach
 
Traditional passive network monitoring is not viable in the current bridged architecture without a dedicated switch. The following hybrid approach is used:
 
| Method | Description | Status |
|---|---|---|
| **Elastic Agent via Fleet** | Agents installed on all lab machines ship endpoint telemetry, logs, and network connection data to Security Onion Fleet server | Primary method |
| **PCAP Import** | Packet captures from lab exercises manually imported into Security Onion for network level analysis | Secondary method |
 
**Machines with Elastic Agent enrolled in Fleet:**
- Kali Linux (10.0.0.10)
- Security Onion itself (10.0.0.20)
- Windows 10 (10.0.0.30)
- Windows 11 (10.0.0.31)
- Windows Server 2022 (10.0.0.40)
- Windows Server 2025 (10.0.0.41)

> All personal home network devices are intentionally excluded from monitoring.
 
---
 

## Tools and Frameworks
- **C2 Frameworks:**
Metasploit, Sliver
 
- **Detection and Monitoring:**
Security Onion, Suricata, Zeek, Kibana, Elastic Agent, Fleet
 
- **Analysis:**
Wireshark, NetworkMiner
 
- **Payloads:**
msfvenom
 
- **Reconnaissance and Scanning:**
Nmap
 
- **Network Attacks (MITM):**
ARP spoofing/poisoning, Bettercap
 
- **Wireless Attacks:**
airodump-ng
 
- **Web Application Security:**
Burp Suite
 
- **Exploitation (Client-Side/Browser):**
BeEF
 
- **Active Directory:**
BloodHound, Impacket, CrackMapExec, Mimikatz
 
---


## Exercises Completed
- [Exercise 01 — Air-gapped Network Setup](exercises/01-air-gapped-network-setup)
- [Exercise 02 — Post Network Connection Attacks](exercises/02-post-network-connection)

## Skills Demonstrated

- Multi-machine lab architecture design and troubleshooting
- Architecture decision making under hardware constraints
- Penetration testing methodology (reconnaissance → exploitation → post-exploitation)
- Active Directory attack techniques (Kerberoasting, Pass-the-Hash, DCSync)
- C2 framework operation (Metasploit, Sliver)
- Endpoint telemetry collection via Elastic Agent Fleet
- Threat detection with SIEM (Kibana), IDS (Suricata), NSM (Zeek)
- Custom detection rule development
- MITRE ATT&CK framework mapping
- Incident response procedures
- Technical documentation and Architecture Decision Records (ADR)
---
 


## Setup Guides
 
- [VMware Setup](setup-guides/01-vmware-setup.md)
- [Network Configuration](setup-guides/02-network-setup.md)
- [Kali Linux Setup](setup-guides/03-kali-setup.md)
- [Security Onion Setup](setup-guides/04-security-onion-setup.md)
- [Windows 11 Setup](setup-guides/05-windows11-lab-setup.md)
- [Windows Victim Setup](setup-guides/06-windows-victim-setup.md)
- [Fleet Setup](setup-guides/07-fleet-setup.md)
- [Elastic Agent Setup](setup-guides/08-elastic-agent-setup.md)
---
 ## Architecture Documentation
 
- [Network Diagram v1 — Original Plan](architecture/network-diagram-v1.md)
- [Network Diagram v2 — Current](architecture/network-diagram-v2.md)
- [ADR-001 — Network Topology Change](architecture/decisions/ADR-001-network-topology-change.md)
---
 
## Planned Upgrades
 
- [ ] Acquire network switch for dedicated isolated lab network (v3 architecture)
- [ ] Enable passive Security Onion network monitoring via switch span port
- [ ] Add Windows 10 VM to Intel x86 machine
- [ ] Complete Active Directory configuration (lab.local domain)
- [ ] Enroll all machines in Elastic Agent Fleet
- [ ] Add PCAP import exercise walkthroughs
---
 
## ⚠️ Disclaimer
 
This lab is for **educational purposes only**. All activities are performed
in a controlled environment. Attack techniques are scoped exclusively to
known lab machines within the defined IP range (10.0.0.0/24).
Never perform these techniques on systems you do not own or have
explicit written permission to test.
 


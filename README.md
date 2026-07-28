# Cybersecurity Home Lab

### Overview
A multi-VM cybersecurity research environment built across two physical machines separated by architecture compatibility. This lab simulates real-world attack and defense scenarios to develop practical offensive and defensive security skills. Due to hardware limitations documented in [ADR-001](architecture/decisions/ADR-001-network-topology-change.md), the defensive portion of the lab is intentionally limited to endpoint telemetry and the documentation of potential indicators of compromise rather than full passive network monitoring.
 
> **Note:** The original design called for a fully isolated air-gapped single-host environment. Architecture constraints encountered during implementation required a redesign. See [ADR-001](architecture/decisions/ADR-001-network-topology-change.md) for the full context, options considered, and decisions made.
 
---

## Quick Start

If you are new to this repository, start here:

1. Read the overview and the architecture decision record in [ADR-001](architecture/decisions/ADR-001-network-topology-change.md).
2. Review the lab architecture and network assumptions below.
3. Follow the setup guides in order:
   - [Network Configuration](architecture/network-diagram-v2.md)
   - [Software and Tools](architecture/software-and-tools.md)
   - [VMware Setup](setup-guides/01-vmware-setup.md)
   - [Kali Linux Setup](setup-guides/03-kali-setup.md)
   - [Windows 10 Setup](setup-guides/06-windows10-setup.md)
   - [Security Onion Setup](setup-guides/04-security-onion-setup.md)
   - [Windows 11 Setup](setup-guides/05-windows11-setup.md)
   - [Fleet Setup](setup-guides/07-fleet-setup.md)
   - [Elastic Agent Setup](setup-guides/08-elastic-agent-setup.md)
   - [Windows Server 2022](setup-guides/07-windows-server-22-setup.md)
   - [Windows Server 2025](setup-guides/08-windows-server-25-setup.md)
4. Continue with the exercises in the order listed under Exercises Completed.
---
 
## Exercises Completed
- [Exercise 01: Air-gapped Network Architechture Design and ADR](exercises/01-air-gapped-network-setup) — Completed. Original air-gapped design revised to bridged architecture. See [ADR-001](architecture/decisions/ADR-001-network-topology-change.md).
- [Exercise 1.1: Pre-Network Connection](exercises/1.1-pre-network-connection) — Wireless adapter setup, monitor mode, and lab reporting workflow
- [Exercise 02: Post Network Connection Attacks](exercises/02-post-network-connection)


## Skills Demonstrated

- Multi-machine lab architecture design and troubleshooting
- Architecture decision making under hardware constraints
- Penetration testing methodology (reconnaissance → exploitation → post-exploitation)
- Active Directory attack techniques (Kerberoasting, Pass-the-Hash, DCSync)
- C2 framework operation (Metasploit, Sliver)
- Endpoint telemetry collection via Elastic Agent Fleet
- Threat detection with Kibana, Suricata, Zeek
- Documentation of potential indicators of compromise (IoCs) and likely attack paths
- Limited defensive monitoring workflows based on endpoint telemetry
- Custom detection rule development concepts
- MITRE ATT&CK framework mapping
- Incident response documentation and reasoning
- Technical documentation and Architecture Decision Records (ADR)
---
 
## Setup Guides

- [Network Configuration](architecture/network-diagram-v2.md) 
- [VMware Setup](setup-guides/01-vmware-setup.md)
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
 
- [X] Add Windows 10 VM to Intel x86/x64 machine
- [ ] Complete Active Directory configuration (lab.local domain)
- [ ] Add PCAP import exercise walkthroughs
- [ ] Acquire network switch for dedicated isolated lab network (v3 architecture)
- [ ] Enable passive Security Onion network monitoring via switch span port
---
 
## ⚠️ Disclaimer
 
This lab is for **educational purposes only**. All activities are performed
in a controlled environment. Attack techniques are scoped exclusively to
known lab machines within the defined IP range (10.0.0.0/24).
Never perform these techniques on systems you do not own or have
explicit written permission to test.
 


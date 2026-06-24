# Cybersecurity Home Lab

## Overview
A fully isolated multi-VM security research environment using VMware Fusion. This lab simulates real-world attack and defense scenarios to practical offensive and defensive security skills. 

## Lab Architecture
- **Host:** macOS (Apple Silicon)
- **Hypervisor:** VMware Fusion
- **Network:** Host-Only (192.168.100.0/24)

### VMs:

├── Kali Linux C2 Server     — 192.168.100.10

├── Windows Victim        — 192.168.100.20

├── Windows Server Victim    — 192.168.100.30

└── Security Onion Defender  — 192.168.100.40

## Tools and Frameworks
- **C2 Frameworks:** Metasploit, Sliver
- **Detection:** Security Onion with Suricata, Zeek, Kibana
- **Analysis:** Wireshark, NetworkMiner
- **Payloads:** msfvenom

## Exercises Completed
- [Exercise 01 — Basic C2 Setup and Session Establishment](exercises/exercise-01-basic-c2.md)
- [Exercise 02 — C2 Traffic Detection with Security Onion](exercises/exercise-02-detection.md)

## Skills Demonstrated
- Network segmentation and air-gapped lab design
- Penetration testing methodology (reconnaissance → exploitation → post-exploitation)
- Active Directory attack techniques (Kerberoasting, Pass-the-Hash, DCSync)
- C2 framework operation (Metasploit, Sliver)
- Threat detection with SIEM (Kibana), IDS (Suricata), NSM (Zeek)
- Custom detection rule development
- MITRE ATT&CK framework mapping
- Incident response procedures

## Setup Guides
- [Kali Linux Setup](setup-guides/kali-setup.md)
- [Windows Victim Setup](setup-guides/windows-victim-setup.md)
- [Security Onion Setup](setup-guides/security-onion-setup.md)
- [Network Isolation Configuration](setup-guides/network-isolation.md)

## ⚠️ Disclaimer
This lab is for **educational purposes only**. All activities are performed
in an isolated, air-gapped environment with no internet connectivity.
Never perform these techniques on systems you do not own or have 
explicit written permission to test.




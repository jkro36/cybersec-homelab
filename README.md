# Cybersecurity Home Lab

## Overview
A fully isolated multi-VM security research environment using VMware Fusion. This lab simulates real-world attack and defense scenarios to practical offensive and defensive security skills. 

## Lab Architecture
- **Host:** macOS (Apple Silicon)
- **Hypervisor:** VMware Fusion
- **Network:** Host-Only (192.168.36.0/24)

### VMs:

├── Kali Linux - Attacker (C2 Server)     — 192.168.36.10

├── Security Onion Defender — 192.168.36.20

├── Windows 11 - Victim    — 192.168.36.30

├── Windows Server 2022 - Victim    — 192.168.36.40

├── Windows 10 - Victim   — 192.168.36.31

└── Windows Server 2025 - Victim    — 192.168.36.41

## Tools and Frameworks
- **C2 Frameworks:** Metasploit, Sliver
- **Detection:** Security Onion with Suricata, Zeek, Kibana
- **Analysis:** Wireshark, NetworkMiner
- **Payloads:** msfvenom
- **Reconnaissance / Scanning:** nmap
- **Network Attacks (MITM):** arp spoofing/poisoning, bettercap
- **Wireless Attacks:** airodump-ng
- **Web Application Security:** Burp Suite
- **Exploitation (Client-Side/Browser):** BeEF

## Exercises Completed
- [Exercise 01 — Air-gapped Network Setup](exercises/01-air-gapped-network-setup)
- [Exercise 02 — Post Network Connection Attacks](exercises/02-post-network-connection)

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
- [VMWare Setup](setup-guides/01-vmware-setup.md)
- [Network Configuration](setup-guides/02-network-setup.md)
- [Kali Linux Setup](setup-guides/03-kali-setup.md)
- [Security Onion Setup](setup-guides/04-security-onion-setup.md)
- [Windows Victim Setup](setup-guides/05-windows-victim-setup.md)

## ⚠️  Disclaimer
This lab is for **educational purposes only**. All activities are performed
in an isolated, air-gapped environment with no internet connectivity.
Never perform these techniques on systems you do not own or have 
explicit written permission to test.




# Software and Tools Specifications

## Overview

This document outlines the software, operating systems, and security tools used throughout this Lab. The environment is designed to simulate a small enterprise network for learning offensive and defensive securities, threat detection, and incident response in an fully isolated environment.

---

## Host Systems

| Component | Specification |
|-----------|---------------|
| Operating System | macOS, Windows 10|
| Hypervisor | VMware Fusion and Workstation|
| Network Mode | Local Area Network / Bridge Mode |
| Lab Network | 10.0.0.0/24 |

---
## Network Configuration

| Setting | Value |
|----------|-------|
| Network Type | Host-Only |
| Subnet | 10.0.0.0/24 |
| IP Assignment | Static (Static IP, outside LAN DHCP range)|

---

## Design Principles

- Fully isolated environment
- Static IP addressing
- Principle of least privilege
- Repeatable lab exercises
- Snapshot-based recovery
- Comprehensive documentation
- Defensive monitoring of offensive activity

---

## Virtual Machines

| Virtual Machine | IP Address | Purpose |
|-----------------|-----------|---------|
| Kali Linux | 10.0.0.10 | Attack workstation / Command & Control |
| Security Onion | 10.0.0.20 | Network monitoring and threat detection |
| Windows 11 | 10.0.0.30 | Domain-joined victim workstation |
| Windows 2022 Server | 10.0.0.40 | Active Directory Domain Controller |
| Windows 2022 Server | 10.0.0.41 | Active Directory Domain Controller |
| Windows 10 | 10.0.0.31 | Domain-joined victim workstation |

---

## Kali Linux

### **Purpose:**

Kali Linux serves as the primary offensive security workstation used to conduct penetration testing, post-exploitation, payload generation, and network analysis.

---

## Security Onion

### Purpose:

Security Onion provides security monitoring, intrusion detection, logs collection, and security analytics.

---

## Windows Server

### Purpose:

Windows Server provides enterprise infrastructure services for the lab environment.

---

## Windows 10

### Purpose:

Windows 10 represents a standard enterprise workstation used during offensive and defensive security exercises.

---
## Security Monitoring Approach
 
Full passive network monitoring is not viable in the current bridged architecture without a dedicated switch. Because Security Onion is limited to endpoint-based visibility in this environment, the defensive focus is on documenting potential indicators of compromise (IoCs), likely attack paths, and observable behaviors from endpoint telemetry rather than claiming complete network-side detection coverage.
 
The following hybrid approach is used:
 
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

> All non-lab devices are intentionally excluded from monitoring.
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
Nmap, Netcat
 
- **Network Attacks (MITM):**
ARP spoofing/poisoning, Bettercap
 
- **Wireless Attacks:**
airodump-ng
 
- **Web Application Security:**
Burp Suite
 
- **Client-Side Exploitation:**
BeEF (Browser Exploitation Framework)
 
- **Active Directory:**
BloodHound, Impacket, CrackMapExec, Mimikatz
- **Password Auditing:**
John the Ripper, Hashcat, Hydra
 
---

# Documentation Tools

| Tool | Purpose |
|------|---------|
| Git | Version control |
| GitHub | Repository hosting |
| Markdown | Technical documentation |
| vim | Text Editor|

---

# Future Expansion
Planned additions include:

- Metasploitable 2
- macOS
- Ubuntu Server

## ⚠️  Disclaimer
This lab is for **educational purposes only**. All activities are performed
in an isolated, air-gapped environment with no internet connectivity.
Never perform these techniques on systems you do not own or have 
explicit written permission to test.
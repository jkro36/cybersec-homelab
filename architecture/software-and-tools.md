# Software and Tools Specifications

## Overview

This document outlines the software, operating systems, and security tools used throughout this Lab. The environment is designed to simulate a small enterprise network for learning offensive and defensive securities, threat detection, and incident response in an fully isolated environment.

---

# Host Systems

| Component | Specification |
|-----------|---------------|
| Operating System | macOS, Windows 10|
| Hypervisor | VMware Fusion and Workstation|
| Network Mode | Local Area Network / Bridge Mode |
| Lab Network | 10.0.0.0/24 |

---

# Virtual Machines

| Virtual Machine | IP Address | Purpose |
|-----------------|-----------|---------|
| Kali Linux | 192.168.36.10 | Attack workstation / Command & Control |
| Security Onion | 192.168.36.20 | Network monitoring and threat detection |
| Windows 11 | 192.168.36.30 | Domain-joined victim workstation |
| Windows 2022 Server | 192.168.36.40 | Active Directory Domain Controller |
| Windows 2022 Server | 192.168.36.41 | Active Directory Domain Controller |
| Windows 10 | 192.168.36.31 | Domain-joined victim workstation |

---

# Kali Linux

## Purpose

Kali Linux serves as the primary offensive security workstation used to conduct penetration testing, post-exploitation, payload generation, and network analysis.

### Installed Tools

| Tool | Purpose |
|------|---------|
| Metasploit and Sliver | Command and Control (C2) Exploitation framework |
| msfvenom | Payload generation |
| Nmap | Network discovery and service enumeration |
| Wireshark | Packet capture and protocol analysis |
| Netcat | Network troubleshooting and shell connectivity |
| Burp Suite Community | Web application testing |
| John the Ripper | Password auditing |
| Hashcat | Password recovery |
| Hydra | Online password attacks |

---

# Security Onion

## Purpose

Security Onion provides centralized monitoring, network security monitoring (NSM), intrusion detection, log collection, and security analytics.

### Installed Components
| Component | Purpose |
|----------|---------|
| Suricata | Intrusion Detection System (IDS) |
| Zeek | Network Security Monitoring (NSM) |
| Kibana | Visualization and dashboards |
| Elasticsearch | Data indexing and search |
| Fleet | Endpoint management |
| SOC | Security operations dashboard |

---

# Windows Server

## Purpose

Windows Server provides enterprise infrastructure services for the lab environment.

### Installed Roles

| Role | Purpose |
|------|---------|
| Active Directory Domain Services | Identity management |
| DNS Server | Name resolution |
| Group Policy | Centralized configuration management |

---

# Windows 10

## Purpose

Windows 10 represents a standard enterprise workstation used during offensive and defensive security exercises.

### Configuration

- Domain Joined
- Microsoft Defender enabled
- Windows Firewall enabled
- Sysmon installed
- PowerShell logging enabled
- Remote Desktop disabled by default

---

# Network Analysis Tools

| Tool | Purpose |
|------|---------|
| Wireshark | Packet capture |
| NetworkMiner | Network forensic analysis |
| tcpdump | Command-line packet capture |

---

# Offensive Security Tools

| Tool | Purpose |
|------|---------|
| Metasploit | Exploitation framework |
| Sliver | Command and Control |
| msfvenom | Payload generation |
| Nmap | Reconnaissance |
| Netcat | Reverse and bind shells |

---

# Defensive Security Tools

| Tool | Purpose |
|------|---------|
| Security Onion | Security monitoring platform |
| Suricata | Intrusion detection |
| Zeek | Network monitoring |
| Kibana | Log visualization |
| Sysmon | Endpoint telemetry |


---

# Documentation Tools

| Tool | Purpose |
|------|---------|
| Git | Version control |
| GitHub | Repository hosting |
| Markdown | Technical documentation |

---

# Network Configuration

| Setting | Value |
|----------|-------|
| Network Type | Host-Only |
| Subnet | 192.168.36.0/24 |
| Internet Access | Disabled during lab exercises |
| DHCP | Disabled |
| IP Assignment | Static |

---

# Design Principles

- Fully isolated (air-gapped) environment
- Static IP addressing
- Principle of least privilege
- Repeatable lab exercises
- Snapshot-based recovery
- Comprehensive documentation
- Defensive monitoring of offensive activity

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
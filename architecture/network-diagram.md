


```
┌─────────────────────────────────────────────────────────────────────┐
│                              HOST MACHINE                           │
│                                                                     │
│   ┌──────── Host-Only Network: 192.168.36.0/24 (AIR-GAPPED) ─────┐  │
│   │                                                              │  │
|   |                     NO INTERNET ACCESS                       |  |
│   │       ┌──────────────────┐    ┌──────────────────┐           │  │
│   │       │   KALI LINUX     │    │  SECURITY ONION  │           │  │
│   │       │  (Attacker/C2)   │    │  (Defender/SOC)  │           │  │
│   │       │  192.168.36.10   │    │  192.168.36.20   │           │  │
│   │       │                  │    │                  │           │  │
│   │       │  • Metasploit    │    │  • Suricata IDS  │           │  │
│   │       │  • Sliver C2     │    │  • Zeek NSM      │           │  │
│   │       │  • msfvenom      │    │  • Elasticsearch │           │  │
│   │       │  • Impacket      │    │  • Kibana        │           │  │
│   │       │  • BloodHound    │    │                  │           │  │
│   │       └──────────────────┘    └──────────────────┘           │  │
│   │           │  attacks           │  monitors                   │  │
│   │           └──────────┬─────────┘                             │  │
│   │                      ▼                                       │  │
│   │  ┌──────────────────────────────────────────────────────┐    │  │
│   │  │         VICTIM MACHINES (what gets attacked)         │    │  │
│   │  │                                                      │    │  │
│   │  │  Windows Server            Windows 10 Workstation    │    │  │
│   │  │  192.168.36.30             192.168.36.40             │    │  │
│   │  │  (Active Directory DC)    (regular employee PC)      │    │  │
│   │  │                                                      │    │  │
│   │  │  Windows Server            Windows 10 Workstation    │    │  │
│   │  │  192.168.36.30             192.168.36.40             │    │  │
│   │  │  (Active Directory DC)    (regular employee PC)      │    │  │
│   │  │                                                      │    │  │
│   │  │  Windows Server            Windows 10 Workstation    │    │  │
│   │  │  192.168.36.30             192.168.36.40             │    │  │
│   │  │  (Active Directory DC)    (regular employee PC)      │    │  │
│   │  └──────────────────────────────────────────────────────┘    │  │
│   │                                                              │  │
│   │                                                              │  │
│   └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```
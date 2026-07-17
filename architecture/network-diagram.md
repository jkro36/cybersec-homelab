


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
│   │  │  Windows 2022 Server       Windows Server 2025       │    │  │
│   │  │  192.168.36.40             192.168.36.41.            │    │  │
│   │  │  (Active Directory DC)     (Active Directory DC)     │    │  │
│   │  │                                                      │    │  │
│   │  │  Windows 11 Workstation     Windows 10 Workstation   │    │  │
│   │  │  192.168.36.30              192.168.36.31            │    │  │
│   │  │  (regular PC)               (regular PC)             │    │  │
│   │  │                                                      │    │  │
│   │  │  Mobile (Android)          Mobile (ios)              │    │  │
│   │  │  IP: Coming soon           IP: Coming soon           │    │  │
│   │  │  (Mobile Device)          (Mobile Device)            │    │  │
│   │  └──────────────────────────────────────────────────────┘    │  │
│   │                                                              │  │
│   │                                                              │  │
│   └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```
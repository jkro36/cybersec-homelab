# ADR-001 — Network Topology Change
**Status:** Accepted  
**Date:** July 2026  
**Author:** J. Kro

---

## Context

During the initial lab build phase the original network design called for a fully air-gapped isolated lab environment using a dedicated host-only network in VMware Fusion on an Apple Silicon Mac. All VMs including the attacker, victim, and Security Onion defender were planned to run on the same Mac in an isolated network with no internet access.

During implementation two sequential problems were discovered that required a redesign of the network topology. The solution to the first problem introduced the second problem, which then required its own solution.

---

## Problem 1 — Architecture Incompatibility Between Lab Components and Apple Silicon

The Apple Silicon Mac runs VMware Fusion which only supports ARM64 guest operating systems. Several required lab components are x86/x64 only and have no ARM release:

- **Security Onion** — compiled for x86/x64 only, no ARM support
- **Windows Server 2022** — no ARM release exists
- **Windows Server 2025** — has a limited ARM release but critical Active Directory features including Virtualization Based Security (VBS) and Credential Guard are unavailable when running in a VM on Apple Silicon, making it unsuitable for an AD security lab

Running all lab components on the Apple Silicon Mac in a single host-only network was therefore not viable.

### Solution to Problem 1 — Separate Lab Components by Architecture Compatibility

The only viable solution was to separate lab components across two physical machines based on architecture compatibility:

- **Apple Silicon Mac** — runs ARM64 compatible VMs only (Kali Linux ARM, Windows 11 ARM) via VMware Fusion
- **Intel x86/x64 compatible machine** — runs all x86 required components (Security Onion, Windows Server 2022, Windows Server 2025) via VMware Workstation

This separation resolved the architecture incompatibility but immediately introduced a second problem.

---

## Problem 2 — Separation Across Physical Machines Breaks Network Communication

Host-only networking in VMware Fusion exists only within the Mac hypervisor. It has no physical network presence outside the Mac. Once lab components were separated across two physical machines the VMs on the Mac and the VMs on the Intel machine could no longer communicate with each other through a host-only network.

A solution was needed to allow all lab VMs across both physical machines to communicate on the same network.

## Options Considered


### Option A — Dedicated Isolated Lab Network via Network Switch

Purchase switch and a USB to Ethernet adapter for the Mac. Connect both physical machines to the switch to create a dedicated isolated lab network segment with no internet access.

**Pros:**
- Fully isolated from home network
- Most realistic enterprise lab topology
- No lab attack traffic on home network
- Enables passive network monitoring on Security Onion in future
- Clean and professional lab architecture

**Cons:**
- Additional hardware cost
- Requires time to acquire hardware
- Additional setup and configuration

**Decision:** Deferred — viable and planned as future upgrade (v3 architecture)

---

#### Option B — Cloud Based Lab via Azure Free Tier

Run Windows Server VMs in Azure and connect local VMs via VPN to enable cross machine communication.

**Pros:**
- No hardware limitations
- Full x86 support
- Free tier available

**Cons:**
- Requires internet connectivity for all lab exercises
- Added latency introduces unrealistic attack conditions
- Complex VPN networking setup
- Not suitable for offline or air-gapped exercise scenarios

**Decision:** Rejected for primary lab — considered for future cloud focused exercises only


---

### Option C — Bridged Mode via Local Area Network with Static IPs Outside DHCP Range

Connect all VMs on both physical machines to the existing home router via bridged networking. Assign static IPs manually outside the home router DHCP range to prevent IP conflicts with other home network devices.

**Pros:**
- No additional hardware required
- Immediate implementation
- All VMs across both machines communicate on same subnet
- Static IPs outside DHCP range prevent conflicts with home devices

**Cons:**
- Lab attack traffic runs on home network
- True passive network packet capture not viable without span port
- Less realistic enterprise isolation
- Home devices share subnet with lab machines

**Decision:** Accepted as interim solution

---

## Accepted Solution to Problem 2 — Option B (Bridged Mode via Home Network)

Option C was accepted as the interim solution due to immediate availability and zero additional hardware cost. All VMs on both physical machines are connected to the home router via bridged networking with manually assigned static IPs in the 10.0.0.0/24 range, outside the home router DHCP assignment range to prevent IP conflicts.

---

## Security Monitoring Approach

Accepting Option C introduced a monitoring constraint. Without a dedicated switch and span port, Security Onion cannot passively capture all lab network traffic. The following hybrid approach was adopted to compensate:

### Primary Method — Elastic Agent via Fleet
Elastic Agents are installed directly on each monitored lab machine and enrolled in the Security Onion Fleet server. This provides host based endpoint telemetry including:

- Process monitoring and execution logs
- Authentication and account activity
- Network connection logs per endpoint
- File integrity monitoring
- Endpoint detection and response telemetry

**Machines receiving Elastic Agent:**
- Kali Linux ARM (10.0.0.10)
- Security Onion (10.0.0.20)
- Windows 10 ARM (10.0.0.30)
- Windows 11 ARM (10.0.0.31)
- Windows Server 2022 (10.0.0.40)
- Windows Server 2025 (10.0.0.41)


**Machines intentionally excluded:**
- All personal home network devices — not monitored, not enrolled

### Secondary Method — PCAP Import
Packet captures generated during specific lab exercises are manually imported into Security Onion for network level analysis. This enables full packet inspection and Suricata/Zeek analysis of targeted exercises without passive monitoring of the home network.

---

## Subnet History

| Version | Subnet | Notes |
|---|---|---|
| v1 — Original plan | 192.168.36.0/24 | Planned isolated lab network — never implemented |
| v2 — Current | 10.0.0.0/24 | Static IPs outside home router DHCP range |

---

## Current Lab Architecture (v2)

```
Home Router
    │
    ├── Apple Silicon Mac
    │   └── VMware Fusion (Bridged)
    │       ├── Kali Linux ARM    10.0.0.10
    │       └── Windows 11 ARM    10.0.0.31
    │
    └── Intel x86/x64 Machine
        └── VMware Workstation (Bridged)
            ├── Security Onion    10.0.0.20
            ├── Windows 10 x64    10.0.0.30
            ├── Win Server 2022   10.0.0.40
            └── Win Server 2025   10.0.0.41
```

---

## Original Planned Architecture (v1 — Not Implemented)

```
Isolated Host-Only Network — Air Gapped
        │
        └── Apple Silicon Mac
            └── VMware Fusion Host-Only
                ├── Kali Linux ARM     192.168.36.10
                ├── Windows 10 x64     192.168.36.31
                ├── Windows 11 ARM     192.168.36.31
                ├── Security Onion     192.168.36.20  ← not viable on ARM
                ├── Windows Server 22  192.168.36.40  ← not viable on ARM
                └── Windows Server 25  192.168.36.41  ← not viable on ARM
```

---

## Consequences and Tradeoffs

| Tradeoff | Impact | Mitigation |
|---|---|---|
| Lab traffic on Local Area network | Attack traffic on LAN subnet | Controlled exercises scoped to lab IPs only |
| No passive packet capture | Cannot auto-capture all lab traffic | Manual PCAP import per exercise |
| Non-lab devices on same subnet | Risk of accidental targeting | Lab IPs clearly defined in 10.0.0.x range |
| Security Onion blind to LAN traffic | Noisy or incomplete alerts | Elastic Agent Fleet compensates with endpoint telemetry |

---

## Actions Required

- [x] Separate lab components across two physical machines by architecture
- [x] Assign static IPs in 10.0.0.0/24 range outside home DHCP
- [ ] Install Elastic Agent on all monitored lab machines
- [ ] Configure Fleet server in Security Onion
- [ ] Establish PCAP import workflow for lab exercises
- [ ] Acquire network switch for v3 isolated architecture upgrade

---

## Lessons Learned

1. **ARM architecture limitations are significant** — Verify hypervisor and guest OS architecture compatibility before designing lab topology around specific hardware

2. **Solving one problem can introduce another** — Separating by architecture resolved incompatibility but broke network communication, requiring a second round of solution design

3. **Passive monitoring requires physical network access** — Without a dedicated switch and span port, passive full packet capture is not viable in a home lab bridged to a consumer router

4. **Elastic Agent Fleet is a viable monitoring alternative** — Host based telemetry provides meaningful visibility and is representative of how modern enterprise SOCs operate

5. **Document subnet changes explicitly** — IP range changes affect all configurations across every machine. Tracking the history prevents confusion during troubleshooting

6. **Static IPs outside DHCP range are essential** — Prevents conflicts with dynamically assigned home network devices sharing the same subnet

---

## Future Upgrade Path (v3)

**Hardware needed:**
- Switch with capability to handle lab requirements.
- USB to Ethernet adapter for Mac.

**What changes:**
- Both machines connect to dedicated switch instead of Local Area Network
- Lab network fully isolated from LAN
- Security Onion gains passive monitoring interface
- Elastic Agent Fleet retained as additional endpoint telemetry layer
- Lab attack traffic no longer touches LAN

---

## References

- [Network Diagram v1 — Original Plan](../network-diagram-v1.md)
- [Network Diagram v2 — Current](../network-diagram-v2.md)
- [Security Onion Documentation](https://docs.securityonion.net)
- [Elastic Agent Fleet Documentation](https://www.elastic.co/guide/en/fleet/current/fleet-overview.html)
- [VMware Fusion Apple Silicon Compatibility](https://knowledge.broadcom.com/external/article/315602)

---

*This document follows the Architecture Decision Record (ADR) format.*  
*ADRs capture the context, options, decision, and consequences of significant architectural choices.*

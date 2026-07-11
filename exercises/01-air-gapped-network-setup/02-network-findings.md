<!-- 02-network-setup.md
Last Updated: June 29, 2026
-->

# Network Configuration (VMware Fusion)

## Overview

This document describes the virtual network design for the lab, the isolation-vs-internet-access problem encountered during design, the options considered, and the two-adapter solution adopted. It includes both the reasoning (so the design is understood, not just copied) and the step-by-step configuration.

---

## Goals

The lab network must satisfy two requirements that initially appear to conflict:

1. **Isolation** — Lab machines (attacker and victims) must be able to communicate with each other on a private network that has **no path to the internet or the host's production network**. This contains exploit and (future) malware activity.

2. **Selective internet access** — Some machines (primarily the attacker, Kali) periodically need internet access to download and update tools. Victim machines should generally remain isolated.

---

## The Problem

The initial design placed all VMs on a single air-gapped network (`vmnet2`, `192.168.36.0/24`, NAT disabled). This satisfied isolation but created a conflict:

- With NAT disabled on the network, **no VM** on it could reach the internet — including the attacker machine that needs to download tools.
- Enabling NAT on that same network would give **every VM** internet access simultaneously (NAT is a property of the whole network, not a single VM). This is all-or-nothing and would expose victim machines that should stay isolated.

**Core issue:** Internet access controlled at the *network* level cannot selectively apply to a single VM. A different approach is needed to give internet to one machine while keeping others isolated — without repeatedly reconfiguring the host network.

---

## Options Considered

### Option 1 — Toggle NAT on the air-gap network as needed

Enable the network's NAT checkbox only when internet is needed, then disable it afterward.

- **Rejected.** NAT applies to the entire network, so this exposes *all* VMs at once, not just the one that needs internet. It also requires repeated host-network reconfiguration (and admin authentication) each time, and depends on remembering to disable it — a manual step easily forgotten. It cannot give internet to one VM while isolating others.

### Option 2 — Switch a single adapter between air-gap and NAT per VM

Give each VM one adapter and change which network it attaches to depending on need.

- **Rejected.** Switching the adapter's network also breaks the VM's addressing (the static air-gap IP is on the wrong subnet for NAT), requiring IP reconfiguration each switch. Tedious and error-prone.

### Option 3 — Two network adapters per VM (SELECTED)

Give each VM **two** virtual network adapters, each attached to a different network:

- **Adapter 1 → NAT** (DHCP) — provides internet access; can be connected/disconnected per individual VM.
- **Adapter 2 → air-gap `vmnet2`** (static IP) — the isolated lab network, always present.

- **Selected.** This provides **per-VM** internet control (toggle one VM's NAT adapter without affecting others), keeps the air-gap addressing constant on Adapter 2, and requires no host-network reconfiguration to go online or offline.

---

## Decision

**Option 3 (two adapters per VM) is adopted.**

Each VM has:

| Adapter | Network | Addressing | Purpose |
|---|---|---|---|
| Adapter 1 | NAT | DHCP | Internet access (toggleable per VM) |
| Adapter 2 | Air-gap (`vmnet2`) | Static (`192.168.36.x`) | Isolated lab communication |

**Usage model:**

- To give a specific VM internet (e.g. Kali downloading tools): connect that VM's NAT adapter.
- To isolate it: disconnect that VM's NAT adapter. The air-gap adapter (Adapter 2) remains, so lab communication is unaffected.
- Victim machines can be given **no NAT adapter at all** (only the air-gap adapter) so they are structurally incapable of reaching the internet — the strongest isolation for machines that should never be online.

---

## Key Concepts (Why This Works)

- **Network configuration is per-interface, not per-machine.** A single VM can have one adapter on DHCP (internet) and another on a static IP (air-gap) simultaneously, with no conflict, because each interface is configured independently and belongs to its own network.

- **Both adapters operate at the same time.** They are not mutually exclusive. The routing table sends traffic destined for `192.168.36.0/24` out the air-gap adapter and internet-bound traffic out the NAT adapter (the default route). Only the *default route* is singular; local-network traffic and internet traffic are handled in parallel.

- **Static IPs and DHCP coexist without conflict.** A statically-configured interface never requests an address from DHCP, so the air-gap static IPs are never overridden. To avoid address collisions, static IPs are assigned **outside** any DHCP hand-out range.

- **Internet identity is not affected by adapter placement.** Internal (private) IPs and MAC addresses do not traverse the internet — MAC addresses are rewritten at each router hop, and NAT rewrites the private source IP to the router's public IP. Internet-bound traffic exits via the public IP regardless of which internal adapter originated it.

---

## Network Reference

| Network | VMware Type | Subnet | NAT | DHCP | Purpose |
|---|---|---|---|---|---|
| `vmnet2` (air-gap) | Custom | `192.168.36.0/24` | Disabled | Enabled but unused (static IPs) | Isolated lab communication |
| NAT (default) | NAT | VMware-assigned | Enabled | Enabled | Internet access for tool downloads |

**Static IP assignments (air-gap adapter):**

| VM | Air-gap IP |
|---|---|
| Kali (attacker) | `192.168.36.10` |
| Windows 10 (victim) | `192.168.36.40` |
| Windows Server | `192.168.36.30` |

(Assign statics in a low range, well outside the DHCP hand-out range, to prevent collisions.)

---

## Configuration Steps

### Part A — Create the air-gap network (`vmnet2`)

1. VMware Fusion → menu bar → **Settings** → **Network**.
2. Click the **padlock** (bottom-left) and authenticate with the Mac admin password. *(Required — without unlocking, changes silently fail to save.)*
3. Click **+** to add a custom network (e.g. `vmnet2`).
4. Set **Subnet IP:** `192.168.36.0`, **Subnet Mask:** `255.255.255.0`.
5. **Uncheck** "Allow virtual machines on this network to connect to external networks (using NAT)" — this creates the isolation.
6. Leave DHCP as-is (static IPs will be set inside each VM; DHCP sits unused). *(Leaving DHCP enabled keeps the subnet field editable in the UI.)*
7. Apply.

### Part B — Add two adapters to each VM

For each VM (with the VM powered off):

1. VM **Settings** → **Add Device** → **Network Adapter** (so the VM has two adapters total).
2. Set **Adapter 1** → **NAT**.
3. Set **Adapter 2** → the custom **air-gap network** (`vmnet2`).

### Part C — Configure addressing inside each guest

1. Identify which interface is which. In the guest, list interfaces (e.g. `ip a` on Linux) and match by the address received: the interface with a NAT-range address (e.g. `192.168.x.x`) is the NAT adapter; the other is the air-gap adapter.
2. Leave the **NAT adapter** on **DHCP** (automatic).
3. Set the **air-gap adapter** to a **static IP** from the table above, using the guest's network manager (e.g. NetworkManager on Kali — GUI or `nmcli` — not `/etc/network/interfaces`, which Kali does not use for managed interfaces).

### Part D — Verify

1. Confirm each interface has the expected address (`ip a`).
2. From one lab VM, ping another's air-gap IP to confirm lab connectivity:
   ```
   ping 192.168.36.20
   ```
3. Confirm internet works when the NAT adapter is connected, and stops when it is disconnected — while air-gap connectivity remains.

---

## Findings & Lessons Learned

- **Network-level NAT is all-or-nothing.** Controlling internet access at the network level cannot single out one VM. Per-VM control requires a per-VM mechanism — a dedicated adapter.
- **Two adapters provide clean separation of concerns.** One interface owns internet, one owns isolation; each is toggled or configured independently.
- **Interfaces, not machines, are the unit of network configuration.** This is why a single VM can be simultaneously internet-connected and air-gap-isolated.
- **Structural isolation beats manual toggling.** A victim VM with *no* NAT adapter cannot accidentally be exposed; there is no switch to forget.

---

## Skills Demonstrated

- **Virtual network design** — designing an isolated lab network with selective, per-host internet access.
- **Multi-homing** — configuring hosts with multiple interfaces on different networks for different purposes.
- **Static vs. DHCP addressing** — per-interface IP configuration and collision avoidance.
- **Requirement conflict resolution** — reconciling isolation with selective connectivity by evaluating options against constraints.
- **Hypervisor networking** — VMware Fusion custom networks, NAT vs. isolated networks, per-adapter attachment.
- **Technical documentation** — recording the design decision, reasoning, and reproducible steps.

---

## Next Steps

- VM #1 — Attacker — [Kali Linux Setup](03-kali-setup.md)
- VM #2 — Defender — [Security Onion Setup](04-security-onion-setup.md)
- VM #3 — Victim — [Windows 10 Setup](05-windows-victim-setup.md)

---

## Troubleshooting

### "Apply" does nothing / network changes don't save

Click the **padlock** in the Network settings pane and authenticate first. Network changes require admin authorization; without it, Apply silently fails. If it still fails, fully quit and relaunch VMware Fusion, then unlock before making changes.

### Subnet field grays out when disabling DHCP

Leaving DHCP **enabled** keeps the subnet field editable. Static IPs set inside the guests take precedence regardless — a statically-configured interface never requests a DHCP address, so DHCP sits unused.

### Static IP appears to conflict / lose internet when switching networks

Static IPs are tied to a specific subnet. Rather than switching one adapter between networks, use the two-adapter design so the static air-gap IP stays on its own adapter while a separate NAT adapter handles internet.

---

## References

- [VMware Fusion Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/desktop-hypervisors/fusion-pro.html)

# Exercise 1.1 — Pre-Network Connection

## Purpose

This exercise folder documents the setup and validation of a wireless
analysis environment in Kali Linux, followed by hands-on testing of
common wireless security protocols in a controlled, authorized lab
environment. It covers everything required before active wireless
attack exercises can begin, from  hardware configuration, monitor mode
verification, and protocol-specific reconnaissance and attack workflows
across WEP, WPA, WPA2, and WPA3.

---

## Objectives

By completing this exercise folder, I was able to:

- Connect and verify a compatible USB wireless adapter inside a Kali Linux VM
- Enable monitor mode and confirm packet injection capability
- Understand the differences between WEP, WPA, WPA2, and WPA3 security protocols
- Capture and analyze wireless traffic for each protocol type
- Demonstrate the practical weaknesses of legacy protocols and the improvements in modern ones
- Document findings, indicators of compromise, and detection considerations for each protocol
- Practice safe, authorized wireless testing in an isolated lab environment

---

## Structure

Each sub-exercise is split into two files:

- `steps.md` — what I did and how: commands, configuration, and procedure
- `findings.md` — what I found: results, detection analysis, MITRE ATT&CK mapping, skills demonstrated, and remediation

---

## Files in This Exercise

| Folder / File | Description |
|---------------|-------------|
| [adapter-setup/steps.md](./adapter-setup/steps.md) | USB passthrough, monitor mode setup, frequency configuration, and adapter validation |
| [adapter-setup/findings.md](./adapter-setup/findings.md) | Adapter setup findings, detection analysis, MITRE mapping, skills, remediation |
| [wep/steps.md](./wep/steps.md) | WEP capture and cracking — procedure and commands |
| [wep/findings.md](./wep/findings.md) | WEP findings, detection analysis, MITRE mapping, skills, remediation |
| [wpa-wpa2/steps.md](./wpa-wpa2/steps.md) | WPA and WPA2 handshake capture and offline cracking — procedure and commands |
| [wpa-wpa2/findings.md](./wpa-wpa2/findings.md) | WPA/WPA2 findings, detection analysis, MITRE mapping, skills, remediation |
| [wpa3/steps.md](./wpa3/steps.md) | WPA3 Transitional and Personal mode testing — procedure and commands |
| [wpa3/findings.md](./wpa3/findings.md) | WPA3 findings, offline crack resistance demonstration, detection analysis |
| [lab-report.md](./lab-report.md) | Consolidated summary of findings across all protocols |

---

## Recommended Workflow

Complete in this order. Each step depends on the previous one.

1. **Adapter setup** — `adapter-setup/steps.md` then `adapter-setup/findings.md`
   Do not proceed until monitor mode and packet injection are both verified.

2. **WEP** — `wep/steps.md` then `wep/findings.md`
   Start here, this confirms the full toolchain works end to end.

3. **WPA and WPA2** — `wpa-wpa2/steps.md` then `wpa-wpa2/findings.md`

4. **WPA3** — `wpa3/steps.md` then `wpa3/findings.md`

5. **Consolidated report** — `lab-report.md`
   Complete this last after all protocol exercises are finished.

---

## Disclaimer

This exercise was performed exclusively on personally owned access points
and devices in a controlled lab environment. All wireless networks tested
are owned and operated by me for the sole purpose of security education.
Never perform wireless reconnaissance, capture, or attacks against networks
or devices you do not own or have explicit written authorization.
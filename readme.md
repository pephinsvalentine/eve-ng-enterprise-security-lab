# 🧠 Home Lab Project — Enterprise Security + NetDevOps Automation + Pentesting

**Owner:** Pephins Moyo  
**Date started:** 2026-10-29  
## 🗺️ Network Topology & Architecture

<img width="2748" height="1818" alt="image" src="https://github.com/user-attachments/assets/47160b32-d979-444f-85e4-103ba05be745" />


*The multi-zone enterprise architecture featuring dual-cloud hybrid WAN (Rain Networks & Internet NAT), FortiGate perimeter security, DMZ tier, internal switching fabric, and multi-node Kali instances.*

## Summary

This repository documents the full design, deployment, optimization, and security testing of my multi-zone enterprise lab built in **EVE-NG** running on a 16 GB RAM laptop host. 

Successfully integrating an enterprise-grade **FortiGate Firewall** alongside Cisco/Arista routing and switching components without causing host memory exhaustion required careful resource tuning. This lab models a production-grade enterprise environment complete with multi-homed ISP connections, a DMZ tier, an internal core network, and a hybrid multi-node Kali Linux deployment used for both administrative automation and offensive security testing.

The lab contains:
- **Perimeter & WAN Routing:** ISP-A (Rain Networks Cloud / Bridged to physical home network via 'Cloud1'), ISP-B (Internet NAT Cloud via 'Cloud0'), and Edge-R2 routing tier.
- **Firewall Security:** FortiGate Firewall configured for perimeter defense, traffic segmentation, zone policies, IDS/IPS, and web filtering.
- **Switching Fabric:** Cisco and Arista core and access switches establishing enterprise VLANs, trunking, and L3 core routing ('Core-SW1').
- **DMZ Tier ('DMZ-SW1'):** Metasploitable 2 and Windows Server (Active Directory & DNS).
- **Internal Corporate Blue Zone:** 
  - 'Kali-Jumpserver': Administrative workstation running Python automation scripts and network diagnostics.
  - 'Insider-Threat': Simulated internal endpoint used to test policy violations, internet restriction bypasses, and subsequent lateral movement.
- **External Red Zone:** 
  - 'Attacker-machine': External Kali Linux instance residing on the isolated internet segment ('ISP-B') to execute real-world perimeter scans and exploit chains.
---

## Notes
- **Hardware Optimization:** Running a full FortiGate firewall and multiple virtual machines on a 16 GB host required tuning memory allocation profiles and adjusting CPU parameters to prevent hypervisor locking and swap thrashing.
- **Dual-Cloud Architecture:** 'ISP-A' bridges directly into the physical home network ('Rain Networks') to test home network exposure and perimeter defense, while 'ISP-B' provides an isolated internet path for the external attacker machine.
- **Multi-Kali Role Segregation:** Kali Linux is deployed across three distinct operational contexts: External Threat Actor ('ISP-B'), Internal Jumpserver (Management/Automation), and Insider Threat Node (Internal Zone).

---

## 🧩 Tech Stack
- **EVE-NG Community / Pro** — Virtual network topology hypervisor  
- **VMware Workstation** — Host hypervisor layer and network adapter bridging ('pnet0' / 'pnet1')
- **FortiGate Firewall** — Perimeter security, NAT, firewall policies, and IDS/IPS inspection
- **Cisco & Arista Switches/Routers** — Enterprise routing (OSPF/eBGP), VLAN segmentation, and switching
- **Kali Linux (x3 Nodes)** — External attacker, internal jump-server, and insider threat simulation
- **Metasploitable 2** — Vulnerable DMZ target machine
- **Windows Server** — DMZ Active Directory & DNS server
- **Python (Netmiko / Paramiko / Requests)** — Network automation, configuration backups, health checks, and diagnostics
- **Wireshark**: Packet Caputure/Analyzer
---

## 📁 Contents
- **/docs/** — Detailed Low-Level Design (LLD), High-Level Design (HLD), and complete lab setup guides.
- **/screenshots/** — Visual evidence of lab topology, FortiGate policy configuration, Wireshark captures, and test results.  
- **/configs/** — Low-level device configurations (FortiGate, Edge routers, Core/Access switches).
- **/scans/** — Nmap, SMB enumeration, and vulnerability assessment results from both external and internal vantage points.
- **/automation/** — Python scripts for automated config backups, health monitoring, and threat containment automation.
- **README.md** — Project overview, architecture evolution, and testing framework.

---

## Quick reference — Planned Subnet Architecture (Pending Phase 2 LLD)

*The exact IP allocations and port matrices are currently being designed in Phase 2 (Low-Level Design).*

 **WAN Tier (ISP-A)** | Rain Networks Cloud (Bridged to Home LAN) | 'DHCP / Static (Pending LLD)' |
 **WAN Tier (ISP-B)** | Internet Cloud (NAT Segment for External Attacker) | 'DHCP / Static (Pending LLD)' |
 **Perimeter / Core Link** | Interconnects between Edge-R2, FortiGate, and ISPs | '10.x.x.x /30 (Pending LLD)' |
 **DMZ Tier** | Metasploitable 2 & Windows Server (AD/DNS) | '192.168.200.x /24 (Pending LLD)' |
 **Internal Blue Zone** | Core switching, Access switches, Insider Threat node | '192.168.10.x /24 (Pending LLD)' |
 **Management & NetDevOps** | Kali Jumpserver & Ansible automation targets | '192.168.30.x /24 (Pending LLD)' |
---

## 🛠️ Security & Pentesting Exercise Scope
1. **Perimeter Reconnaissance & Scanning:** Executing active Nmap scans from the external 'Attacker-machine' across 'ISP-B' targeting the IGW, DMZ, and internal routing boundaries.
2. **Insider Threat & Web Restriction Violations:** Simulating an internal user ('Insider-Threat') bypassing corporate policies to access unauthorized external internet resources, leading to a simulated endpoint compromise.
3. **Lateral Movement Simulation:** Pivoting from the compromised internal workstation to probe DMZ targets ('Metasploitable 2' / 'Windows Server') and core switching infrastructure.
4. **Defensive Enforcement (IDS/IPS & Whitelisting):** Configuring FortiGate Deep Packet Inspection (DPI), signature-based Intrusion Prevention Systems (IPS), and strict URL/application whitelisting/blacklisting to detect, log, and block the simulated attack traffic.

---

## Troubleshooting Notes
- **FortiGate RAM Exhaustion on 16GB Host:** Resolved by tuning memory allocation profiles in the EVE-NG node template settings and keeping concurrent VM boot counts minimized.
- **EVE-NG Cloud Bridging ('Rain Networks' vs 'Internet'):** Properly mapped VMware Adapter 1 to NAT ('VMnet8' / 'pnet0') and Adapter 2 to Bridged ('VMnet0' / 'pnet1'), followed by resetting EVE networking services and fixing permissions via CLI:
  '''bash
  systemctl restart networking
  /opt/unetlab/wrappers/unl_wrapper -a fixpermissions

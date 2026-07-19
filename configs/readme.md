# 🏫 Enterprise School Network

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=flat-square&logo=cisco&logoColor=white)
![Networking](https://img.shields.io/badge/Focus-Enterprise%20Networking-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Version%202%20Completed-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-Educational-lightgrey?style=flat-square)

---

## Project Overview

**Enterprise School Network** is a Cisco Packet Tracer project that simulates the network infrastructure of an educational institution following enterprise network design principles.

The project applies technologies commonly found in production environments, including network segmentation, centralized services, secure remote administration, simulated Internet access and High Availability.

The infrastructure has been developed in two stages:

- **Version 1** implements a complete enterprise campus network.
- **Version 2** introduces High Availability, redundancy and automatic failover.

---

# Project Versions

| Version | Description | Status |
|:-------:|-------------|:------:|
| [V1](topology/v1/) | Enterprise network with VLANs, VLSM, DHCP, ACLs, SSH, static routing and NAT/PAT | ✅ Completed |
| [V2](topology/v2/) | High Availability architecture with dual core switches, HSRP, Rapid PVST and redundant uplinks | ✅ Completed |

---

# Main Features

## Version 1 — Enterprise Campus Network

- VLAN segmentation
- VLSM addressing
- Inter-VLAN Routing
- Centralized DHCP Server
- DHCP Relay
- Static Routing
- Simulated ISP
- NAT/PAT
- SSH Secure Management
- Extended ACLs
- Internet Connectivity
- Network Validation

---

## Version 2 — High Availability

Version 2 expands the original architecture by introducing enterprise redundancy mechanisms:

- Dual Multilayer Core Switches
- HSRP Gateway Redundancy
- Gateway Load Balancing
- Rapid PVST
- Redundant Trunk Links
- Layer 2 Resiliency
- Automatic Gateway Failover
- High Availability Validation

---

# Technologies Used

- Cisco Packet Tracer
- Cisco IOS
- VLANs
- VLSM
- Inter-VLAN Routing
- DHCP
- DHCP Relay
- Static Routing
- NAT/PAT
- HSRP
- Rapid PVST
- ACLs
- SSH
- Layer 2 Switching
- Layer 3 Switching
- Enterprise Campus Design

---

# Repository Structure

```text
enterprise-school-network/
│
├── topology/
│   ├── v1/
│   └── v2/
│
├── configs/
│   ├── v1/
│   └── v2/
│
├── docs/
│   ├── v1/
│   └── v2/
│
├── security/
│   ├── v1/
│   └── v2/
│
├── tests/
│   ├── v1/
│   └── v2/
│
└── README.md
```

---

# Skills Demonstrated

This project demonstrates practical knowledge of:

- Enterprise Network Design
- Cisco Switching
- Cisco Routing
- High Availability
- First Hop Redundancy Protocol (HSRP)
- Layer 2 Redundancy
- IP Address Planning
- Network Security
- Infrastructure Documentation
- Network Validation

---

# Future Improvements

Potential future enhancements include:

- OSPF Dynamic Routing
- EtherChannel (LACP)
- Port Security
- SNMP Monitoring
- Syslog Server
- NTP
- IPv6 Support
- AAA Authentication
- Wireless Infrastructure

---

# License

This repository has been created for educational purposes and as part of my networking portfolio.

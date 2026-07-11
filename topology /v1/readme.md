# 🏫 Enterprise School Network - Version 1

## Overview

Version 1 represents the first complete implementation of the Enterprise School Network.

This version focuses on building a secure and scalable enterprise LAN while providing simulated Internet access.

---

## Main Features

- VLAN segmentation
- VLSM subnetting
- Inter-VLAN Routing
- Layer 3 Core Switch
- DHCP Server
- DHCP Relay
- ACL Security Policies
- SSH Restricted Management
- Simulated ISP
- NAT/PAT per VLAN

---

## Network Architecture

```
            INTERNET-SERVER
                   |
                R-ISP
                   |
             R-INTERNET
                   |
               MLS-CORE
      ┌──────┬──────┬──────┬──────┬──────┐
      │      │      │      │      │
 ALUMNOS PROFES INVITADOS SERVERS ADMINS
```

---

## Technologies

- Cisco Packet Tracer
- Cisco IOS
- VLANs
- VLSM
- Inter-VLAN Routing
- DHCP
- DHCP Relay
- ACLs
- SSH
- NAT/PAT
- Static Routing

---

## Goal

The objective of Version 1 is to build a functional enterprise network with secure communication between departments and simulated Internet connectivity.

This version serves as the foundation for future improvements.

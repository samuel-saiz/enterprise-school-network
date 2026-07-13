# 🏫 Enterprise School Network - Version 2

## Overview

Version 2 introduces High Availability (HA) into the Enterprise School Network by eliminating the single point of failure present in Version 1.

The network has been redesigned with redundant multilayer switches, gateway redundancy and Layer 2 resiliency mechanisms to improve reliability and ensure service continuity during infrastructure failures.

---

## What's New

Version 2 includes all features available in Version 1 and introduces the following improvements:

- Redundant Core Architecture
- Dual Multilayer Switches
- HSRP Gateway Redundancy
- Rapid PVST
- Redundant Uplinks
- Layer 2 Failover
- High Availability Validation

---

## Network Architecture

```text
                    INTERNET
                       |
                    ISP Router
                       |
                  Edge Router
                  /         \
        MLS-Core-1 ===== MLS-Core-2
            ||               ||
            ||               ||
      =============================
         Access Layer Switches
            |      |      |
          VLAN10 VLAN20 VLAN30
```

The architecture provides gateway redundancy through HSRP while Rapid PVST maintains a loop-free Layer 2 topology and automatically reconverges after link or device failures.

---

## Objectives

- Eliminate the single point of failure.
- Provide gateway redundancy using HSRP.
- Improve network availability.
- Ensure automatic Layer 2 failover.
- Prevent switching loops using Rapid PVST.
- Maintain inter-VLAN communication during failures.
- Preserve Internet connectivity after a core switch failure.

---

## Technologies

- Cisco Packet Tracer
- VLANs
- VLSM
- Inter-VLAN Routing
- DHCP
- DHCP Relay
- ACLs
- SSH
- NAT / PAT
- Static Routing
- HSRP
- Rapid PVST

---

## High Availability Design

### HSRP

HSRP provides a virtual default gateway for every VLAN.

- MLS-Core-1 operates as the Active gateway.
- MLS-Core-2 operates as the Standby gateway.
- If the active multilayer switch becomes unavailable, the standby device automatically assumes the gateway role without requiring changes on client devices.

---

### Rapid PVST

Rapid PVST is used to prevent switching loops while providing fast network convergence.

- MLS-Core-1 is configured as the Primary Root Bridge.
- MLS-Core-2 is configured as the Secondary Root Bridge.

This configuration guarantees a predictable spanning-tree topology and minimizes recovery time after failures.

---

## Validation Tests

The following scenarios are validated during testing:

- Core multilayer switch failure.
- Redundant uplink failure.
- HSRP gateway failover.
- Rapid PVST reconvergence.
- Inter-VLAN routing after failover.
- DHCP functionality after failover.
- Internet connectivity after failover.

---

## Repository Structure

```text
topology/
├── v1/
│   ├── enterprise-school-network-v1.pkt
│   └── README.md
│
└── v2/
    ├── enterprise-school-network-v2.pkt
    └── README.md
```

---

## Version Comparison

| Feature | Version 1 | Version 2 |
|----------|:---------:|:---------:|
| VLANs | ✅ | ✅ |
| DHCP | ✅ | ✅ |
| DHCP Relay | ✅ | ✅ |
| NAT / PAT | ✅ | ✅ |
| ACLs | ✅ | ✅ |
| SSH | ✅ | ✅ |
| Static Routing | ✅ | ✅ |
| Redundant Core | ❌ | ✅ |
| HSRP | ❌ | ✅ |
| Rapid PVST | ❌ | ✅ |
| High Availability | ❌ | ✅ |

---

## Lessons Learned

During the implementation of Version 2, the following concepts were reinforced:

- Enterprise network redundancy principles.
- High Availability design.
- HSRP operation and gateway failover.
- Root Bridge election using Rapid PVST.
- Layer 2 redundancy best practices.
- Fault-tolerant network design.
- Documentation and version control of network projects.

---

## Future Improvements

Potential future enhancements include:

- OSPF Dynamic Routing
- EtherChannel
- Dual ISP Redundancy
- IPv6 Support
- Syslog Server
- SNMP Monitoring
- NTP Synchronization
- Network Monitoring Dashboard

---

## Author

**Samuel Saiz Retama**

Enterprise School Network is a personal learning project developed to simulate enterprise network infrastructures using Cisco Packet Tracer.

The project focuses on practical networking skills, high availability, documentation and real-world network design principles.
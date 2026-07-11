# 🏫 Enterprise School Network - Version 2

## Overview

Version 2 expands the original network by introducing High Availability.

The objective is to remove the single point of failure represented by the multilayer switch and increase network resilience.

---

## New Features

Everything included in Version 1 plus:

- Redundant Core Switches
- HSRP
- Rapid PVST
- Dual Uplinks
- Layer 2 Redundancy
- Gateway Failover
- High Availability Validation

---

## Planned Architecture

```
                INTERNET-SERVER
                       |
                    R-ISP
                       |
                  R-INTERNET
                 /           \
         MLS-CORE-1     MLS-CORE-2
           |   \         /    |
           |    \_______/     |
           |                  |
      Access Switches (Dual Links)
```

---

## Objectives

- Eliminate single points of failure.
- Maintain connectivity if one core switch fails.
- Implement gateway redundancy using HSRP.
- Prevent switching loops using Rapid PVST.
- Validate failover without affecting end users.

---

## Technologies

- Cisco Packet Tracer
- HSRP
- Rapid PVST
- VLANs
- VLSM
- DHCP
- DHCP Relay
- ACLs
- SSH
- NAT/PAT
- Static Routing

---

## Future Validation

The final version will demonstrate that shutting down one multilayer switch does not interrupt communication between VLANs or Internet access.

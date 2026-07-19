# 🏫 Enterprise School Network - Version 2

## Overview

Version 2 transforms the original Enterprise School Network into a High Availability (HA) infrastructure.

The single point of failure present in Version 1 has been eliminated by introducing redundant multilayer switches, gateway redundancy, Layer 2 resiliency mechanisms and redundant uplinks.

The project simulates a real enterprise campus network where the failure of a core switch does not interrupt communication between VLANs or access to network services.

---

## What's New

Version 2 includes every feature implemented in Version 1 and introduces:

- Dual Core Architecture
- Redundant Multilayer Switches
- HSRP Gateway Redundancy
- Rapid PVST
- Layer 2 Redundancy
- Redundant Trunk Links
- Gateway Load Balancing
- Automatic Failover
- High Availability Validation

---

## Network Architecture

```text
                         INTERNET
                            |
                         ISP Router
                            |
                       Edge Router
                      /           \
             MLS-CORE-1 ===== MLS-CORE-2
              ||   ||           ||   ||
              ||   ||           ||   ||
     ==========================================
       SW-ALUMNOS  SW-PROFESORES  SW-INVITADOS
          |              |              |
      SW-SERVERS     SW-ADMINS

```

Both multilayer switches operate simultaneously.

HSRP provides gateway redundancy while Rapid PVST prevents Layer 2 loops and automatically reconverges after failures.

Each access switch is connected to both core switches, ensuring network availability even if a core device or uplink fails.

---

## High Availability Design

### Gateway Redundancy

HSRP provides a virtual default gateway for every VLAN.

| VLAN | Active Gateway |
|------|----------------|
| VLAN 10 - Students | MLS-CORE-1 |
| VLAN 20 - Teachers | MLS-CORE-2 |
| VLAN 30 - Administration | MLS-CORE-1 |
| VLAN 40 - Guests | MLS-CORE-2 |
| VLAN 50 - Servers | MLS-CORE-1 |
| VLAN 99 - Management | MLS-CORE-2 |

This configuration distributes traffic between both multilayer switches while maintaining automatic failover.

---

## Layer 2 Redundancy

Rapid PVST has been configured to:

- Prevent switching loops
- Block redundant paths when necessary
- Automatically recover after failures
- Align the STP Root Bridge with the active HSRP gateway

---

## Network Services

Version 2 maintains every service implemented in Version 1:

- Inter-VLAN Routing
- DHCP Server
- DHCP Relay
- NAT/PAT
- SSH Management
- Extended ACLs
- Static Routing
- Internet Simulation
- Web Server

All services continue operating during a core switch failure.

---

## Objectives

The main goals of Version 2 are:

- Eliminate the single point of failure
- Provide gateway redundancy using HSRP
- Increase network availability
- Implement Layer 2 resiliency
- Simulate enterprise network redundancy
- Validate automatic failover
- Document a production-style Cisco campus design

---

## Technologies

- Cisco Packet Tracer
- Cisco IOS
- VLANs
- VLSM
- Inter-VLAN Routing
- HSRP
- Rapid PVST
- DHCP
- DHCP Relay
- NAT/PAT
- ACLs
- SSH
- Static Routing
- High Availability

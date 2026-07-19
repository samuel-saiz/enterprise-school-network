# 🏫 Enterprise School Network

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=flat-square\&logo=cisco\&logoColor=white)
![Networking](https://img.shields.io/badge/Focus-Enterprise%20Networking-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Version%202%20Completed-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-Educational-lightgrey?style=flat-square)

---

## Project Overview

**Enterprise School Network** is a Cisco Packet Tracer project that simulates the network infrastructure of an educational institution using enterprise network design principles.

The project implements network segmentation, VLSM addressing, centralized services, secure remote administration, controlled inter-VLAN communication, simulated Internet access and High Availability.

The infrastructure was developed in two stages:

* **Version 1** implements a complete functional campus network using a single multilayer core switch.
* **Version 2** removes the main core-layer single point of failure by introducing redundant multilayer switches, gateway redundancy and Layer 2 resiliency.

---

## Project Versions

|       Version      | Description                                                                                                    |    Status   |
| :----------------: | -------------------------------------------------------------------------------------------------------------- | :---------: |
| [V1](topology/v1/) | Functional enterprise network with VLANs, VLSM, DHCP, ACLs, SSH, static routing and NAT/PAT                    | ✅ Completed |
| [V2](topology/v2/) | High Availability architecture with dual core switches, HSRP, Rapid PVST, redundant uplinks and route failover | ✅ Completed |

---

# Version 1 — Functional Enterprise Network

Version 1 establishes the base campus infrastructure.

A single Cisco Catalyst 3560 multilayer switch provides the VLAN gateways and performs inter-VLAN routing.

## Main Features

* VLAN-based network segmentation
* VLSM subnetting
* Inter-VLAN routing
* Centralized DHCP service
* DHCP Relay
* Extended ACL definitions
* Restricted SSH administration
* Static routing
* Simulated ISP connectivity
* NAT/PAT with a different public address per internal network
* Connectivity and security validation scenarios

## Version 1 Architecture

```text
                    INTERNET-SERVER
                         8.8.8.8
                            |
                          R-ISP
                            |
                       R-INTERNET
                            |
                         MLS-CORE
                            |
        ┌───────────┬───────────┬───────────┬───────────┬───────────┐
        │           │           │           │           │           │
   SW-ALUMNOS  SW-PROFESORES SW-ADMINS SW-INVITADOS SW-SERVERS
```

The main limitation of Version 1 is the presence of a single multilayer core switch. If `MLS-CORE` fails, the VLAN gateways and inter-VLAN routing become unavailable.

---

# Version 2 — High Availability Architecture

Version 2 retains the original VLAN design and centralized services while introducing redundancy at the core and distribution layers.

The single `MLS-CORE` device is replaced by:

```text
MLS-CORE-1
MLS-CORE-2
```

Both multilayer switches participate in gateway redundancy, Layer 2 path selection and connectivity toward the Internet edge router.

## High Availability Features

* Two Cisco Catalyst 3560 multilayer core switches
* HSRP redundant default gateways
* Rapid PVST
* Distributed root bridge roles
* Redundant access-switch uplinks
* Inter-core 802.1Q trunk
* Dual routed links toward `R-INTERNET`
* Primary and floating static routes
* Automatic gateway failover
* Layer 2 path recovery
* Redundant DHCP Relay configuration

## Version 2 Architecture

```text
                         INTERNET-SERVER
                              8.8.8.8
                                 |
                               R-ISP
                                 |
                            R-INTERNET
                           /          \
                  10.0.0.0/30      10.0.0.4/30
                       /                \
               MLS-CORE-1 ========= MLS-CORE-2
                    ||                    ||
                    ||  Redundant trunks  ||
                    ||                    ||
        ┌───────────┼───────────┬────────┼───────────┐
        │           │           │        │           │
   SW-ALUMNOS  SW-PROFESORES SW-ADMINS SW-INVITADOS SW-SERVERS
```

The connection between both core switches transports VLANs:

```text
10, 20, 30, 40, 50 and 99
```

---

# Network Segmentation

The internal network is based on:

```text
192.168.0.0/22
```

VLSM is used to allocate subnet sizes according to the estimated number of devices in each department.

| VLAN | Name       | Network            | Subnet mask       | Virtual gateway |
| ---: | ---------- | ------------------ | ----------------- | --------------: |
|   10 | ALUMNOS    | `192.168.0.0/23`   | `255.255.254.0`   |   `192.168.0.1` |
|   20 | PROFESORES | `192.168.2.0/26`   | `255.255.255.192` |   `192.168.2.1` |
|   40 | INVITADOS  | `192.168.2.64/27`  | `255.255.255.224` |  `192.168.2.65` |
|   50 | SERVERS    | `192.168.2.96/29`  | `255.255.255.248` |  `192.168.2.97` |
|   30 | ADMINS     | `192.168.2.112/29` | `255.255.255.248` | `192.168.2.113` |
|   99 | MANAGEMENT | `192.168.2.128/28` | `255.255.255.240` | `192.168.2.129` |

The VLAN 99 subnet was expanded in Version 2 to provide enough addresses for both core switches and all access switches.

---

# HSRP Gateway Redundancy

HSRP provides a virtual default gateway for every VLAN.

Client devices use the HSRP virtual address instead of the physical address of a specific multilayer switch.

If the active gateway becomes unavailable, the standby core assumes the virtual gateway role.

## Core IP Addresses

| VLAN | Virtual gateway |      MLS-CORE-1 |      MLS-CORE-2 |
| ---: | --------------: | --------------: | --------------: |
|   10 |   `192.168.0.1` |   `192.168.0.2` |   `192.168.0.3` |
|   20 |   `192.168.2.1` |   `192.168.2.2` |   `192.168.2.3` |
|   30 | `192.168.2.113` | `192.168.2.114` | `192.168.2.115` |
|   40 |  `192.168.2.65` |  `192.168.2.66` |  `192.168.2.67` |
|   50 |  `192.168.2.97` |  `192.168.2.99` | `192.168.2.100` |
|   99 | `192.168.2.129` | `192.168.2.130` | `192.168.2.131` |

## HSRP Role Distribution

|                 VLAN | Preferred active core |
| -------------------: | --------------------- |
|    VLAN 10 — ALUMNOS | `MLS-CORE-1`          |
| VLAN 20 — PROFESORES | `MLS-CORE-2`          |
|     VLAN 30 — ADMINS | `MLS-CORE-1`          |
|  VLAN 40 — INVITADOS | `MLS-CORE-2`          |
|    VLAN 50 — SERVERS | `MLS-CORE-1`          |
| VLAN 99 — MANAGEMENT | `MLS-CORE-2`          |

The active gateway role is distributed between both core switches instead of assigning all VLANs to a single device.

---

# Rapid PVST

Rapid PVST is used to prevent Layer 2 switching loops and provide faster convergence when a trunk or switch becomes unavailable.

The root bridge role is aligned with the preferred HSRP active gateway.

|                 VLAN | Preferred STP root |
| -------------------: | ------------------ |
|    VLAN 10 — ALUMNOS | `MLS-CORE-1`       |
| VLAN 20 — PROFESORES | `MLS-CORE-2`       |
|     VLAN 30 — ADMINS | `MLS-CORE-1`       |
|  VLAN 40 — INVITADOS | `MLS-CORE-2`       |
|    VLAN 50 — SERVERS | `MLS-CORE-1`       |
| VLAN 99 — MANAGEMENT | `MLS-CORE-2`       |

This alignment reduces unnecessary traffic paths between the core switches.

---

# Centralized DHCP

The DHCP service is hosted on the internal server:

```text
SERVER-WEB
IP address: 192.168.2.98
VLAN: 50 — SERVERS
Gateway: 192.168.2.97
```

DHCP Relay is configured on both multilayer core switches for the client VLANs:

```text
VLAN 10 — ALUMNOS
VLAN 20 — PROFESORES
VLAN 30 — ADMINS
VLAN 40 — INVITADOS
```

Both cores forward DHCP requests to:

```text
192.168.2.98
```

This allows DHCP operation to continue when the active HSRP gateway changes.

---

# Internet Connectivity

Internet access is simulated using two routers and an external server.

```text
Internal network
      |
R-INTERNET
      |
R-ISP
      |
INTERNET-SERVER — 8.8.8.8
```

## Routed Links

| Connection              | Network          | Device addresses              |
| ----------------------- | ---------------- | ----------------------------- |
| R-INTERNET ↔ MLS-CORE-1 | `10.0.0.0/30`    | `10.0.0.1` ↔ `10.0.0.2`       |
| R-INTERNET ↔ MLS-CORE-2 | `10.0.0.4/30`    | `10.0.0.5` ↔ `10.0.0.6`       |
| R-INTERNET ↔ R-ISP      | `203.0.113.0/30` | `203.0.113.2` ↔ `203.0.113.1` |
| R-ISP ↔ INTERNET-SERVER | `8.8.8.0/24`     | `8.8.8.1` ↔ `8.8.8.8`         |

---

# Redundant Static Routing

`R-INTERNET` contains a primary route and a floating static route for each internal network.

The primary route points toward the core that normally owns the active HSRP gateway.

The backup route uses administrative distance `10` and points toward the alternate core.

| Network            | Primary core | Backup core |
| ------------------ | ------------ | ----------- |
| `192.168.0.0/23`   | MLS-CORE-1   | MLS-CORE-2  |
| `192.168.2.0/26`   | MLS-CORE-2   | MLS-CORE-1  |
| `192.168.2.64/27`  | MLS-CORE-2   | MLS-CORE-1  |
| `192.168.2.96/29`  | MLS-CORE-1   | MLS-CORE-2  |
| `192.168.2.112/29` | MLS-CORE-1   | MLS-CORE-2  |

This design provides an alternate path when a routed core connection becomes unavailable.

---

# NAT/PAT

R-INTERNET performs NAT/PAT for the internal networks.

Each network is translated using a different simulated public IP address.

| Internal network | VLAN | Public address |
| ---------------- | ---: | -------------: |
| ALUMNOS          |   10 | `203.0.113.10` |
| PROFESORES       |   20 | `203.0.113.11` |
| INVITADOS        |   40 | `203.0.113.12` |
| ADMINS           |   30 | `203.0.113.13` |
| SERVERS          |   50 | `203.0.113.14` |

The translated addresses belong to:

```text
203.0.113.8/29
```

R-ISP contains a static route toward this block through R-INTERNET so return traffic reaches the NAT router.

---

# Network Security

The project includes several network security controls.

## SSH Administration

SSH is used instead of Telnet for remote device management.

Administrative access is designed to originate from:

```text
VLAN 30 — ADMINS
192.168.2.112/29
```

Real credentials and encrypted password hashes are removed from the published configuration files.

## Traffic Filtering

Extended ACLs are defined to represent policies such as:

* Preventing student devices from directly accessing the server network.
* Preventing guest devices from accessing internal networks.
* Allowing the DHCP traffic required for address assignment.
* Restricting remote device administration to the administrator network.

The configuration files and test documentation should be reviewed together to verify where each ACL is applied.

---

# Validation

The project includes documentation for testing the main network services and redundancy mechanisms.

## Connectivity Tests

* Communication with VLAN gateways
* Inter-VLAN connectivity
* Access to the internal server
* Connectivity with R-INTERNET
* Connectivity with R-ISP
* Connectivity with `8.8.8.8`

## DHCP Tests

* Address assignment for client VLANs
* Correct subnet mask
* Correct HSRP virtual gateway
* DHCP Relay operation through both core switches

## NAT/PAT Tests

* Translation from each internal VLAN
* Different public IP address per network
* Return traffic through R-ISP
* Inspection using `show ip nat translations`

## High Availability Tests

* HSRP active and standby states
* Gateway response during a core failure
* Rapid PVST root bridge roles
* Blocked and forwarding trunk states
* Layer 2 recovery after link failure
* Floating static route activation
* Recovery after restoring the preferred device or link

---

# Repository Structure

```text
enterprise-school-network/
│
├── README.md
│
├── topology/
│   ├── v1/
│   │   ├── Enterprise-School-Network-V1.pkt
│   │   └── Enterprise-School-Network-V1.png
│   │
│   └── v2/
│       ├── Enterprise-School-Network-V2.pkt
│       └── Enterprise-School-Network-V2.png
│
├── configs/
│   ├── README.md
│   ├── V1/
│   └── V2/
│
├── docs/
│   ├── addressing-plan.md
│   ├── vlsm-calculation.md
│   ├── vlan-design.md
│   └── network-services.md
│
├── security/
│   ├── acl-policy.md
│   ├── ssh-management.md
│   └── nat-policy.md
│
└── tests/
    ├── connectivity-tests.md
    ├── dhcp-tests.md
    ├── acl-tests.md
    ├── ssh-tests.md
    ├── nat-tests.md
    └── high-availability-tests.md
```

---

# Device Inventory

| Device          | Model               | Function                         |
| --------------- | ------------------- | -------------------------------- |
| MLS-CORE-1      | Cisco Catalyst 3560 | Primary multilayer core switch   |
| MLS-CORE-2      | Cisco Catalyst 3560 | Secondary multilayer core switch |
| R-INTERNET      | Cisco 2911          | Internet edge router and NAT/PAT |
| R-ISP           | Cisco 2911          | Simulated ISP                    |
| SW-ALUMNOS      | Cisco 2960          | VLAN 10 access switch            |
| SW-PROFESORES   | Cisco 2960          | VLAN 20 access switch            |
| SW-ADMINS       | Cisco 2960          | VLAN 30 access switch            |
| SW-INVITADOS    | Cisco 2960          | VLAN 40 access switch            |
| SW-SERVERS      | Cisco 2960          | VLAN 50 access switch            |
| SERVER-WEB      | Server-PT           | DHCP and internal web services   |
| INTERNET-SERVER | Server-PT           | Simulated external server        |

---

# Technologies Used

* Cisco Packet Tracer
* Cisco IOS CLI
* VLANs
* IEEE 802.1Q trunks
* VLSM
* Inter-VLAN routing
* HSRP
* Rapid PVST
* DHCP
* DHCP Relay
* Static routing
* Floating static routes
* Standard and extended ACLs
* SSH
* NAT
* PAT

---

# How to Use the Project

1. Download or clone the repository.
2. Open the required `.pkt` file with Cisco Packet Tracer.
3. Review the topology and device configurations.
4. Use the documentation to understand the addressing and security policies.
5. Execute the verification commands included in the configuration files.
6. Perform the connectivity and High Availability tests.

---

# Educational Purpose

This project was created for educational and portfolio purposes.

It demonstrates the design, implementation, documentation and validation of a segmented enterprise network with High Availability mechanisms.

The configurations and topology may be used as a learning reference in controlled laboratory environments.

They are not intended to be deployed directly in a production network without additional security review, hardware validation and operational testing.

---

# Author

**Samuel Saiz Retama**

ASIR student focused on:

* Network administration
* Cisco technologies
* Systems administration
* Infrastructure security
* High Availability

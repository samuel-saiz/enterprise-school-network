# 🏫 Enterprise School Network

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=flat-square&logo=cisco&logoColor=white)
![Networking](https://img.shields.io/badge/Focus-Enterprise%20Networking-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Version%202%20In%20Development-orange?style=flat-square)
![License](https://img.shields.io/badge/License-Educational-lightgrey?style=flat-square)

## Project Overview

**Enterprise School Network** is a Cisco Packet Tracer project that simulates the network infrastructure of an educational institution.

The project was designed to apply enterprise networking concepts in a realistic environment, including network segmentation, IP address planning, centralized services, secure administration, controlled inter-VLAN communication and simulated Internet access.

The network is being developed incrementally:

- **Version 1** implements a fully functional segmented network.
- **Version 2** introduces redundancy and High Availability.

---

## Project Versions

| Version | Description | Status |
|:-------:|-------------|:------:|
| [V1](topology/v1/) | Functional enterprise network with VLANs, VLSM, DHCP, ACLs, SSH and NAT/PAT | ✅ Completed |
| [V2](topology/v2/) | Redundant core architecture with HSRP, Rapid PVST and dual uplinks | 🚧 In development |

---

## Main Features

### Version 1 — Functional Network

- VLAN-based network segmentation
- VLSM subnetting
- Inter-VLAN routing
- Centralized DHCP service
- DHCP Relay
- ACL-based traffic filtering
- Restricted SSH administration
- Static routing
- Simulated ISP connectivity
- NAT/PAT with a different public IP per VLAN
- Connectivity and security validation

### Version 2 — High Availability

Version 2 builds on the original design and adds:

- Two multilayer core switches
- Redundant default gateways using HSRP
- Rapid PVST
- Primary and secondary root bridges
- Dual uplinks from the access layer
- Layer 2 failover
- Gateway failover
- Core failure validation

---

## Network Architecture

### Version 1

```text
                    INTERNET-SERVER
                           |
                         R-ISP
                           |
                       R-INTERNET
                           |
                        MLS-CORE
          ┌────────────┬────────────┬────────────┬────────────┐
          │            │            │            │            │
     SW-ALUMNOS  SW-PROFESORES SW-INVITADOS SW-SERVERS  SW-ADMINS
          │            │            │            │            │
         PCs          PCs          PCs        SERVER-WEB      PCs
```

Version 1 uses a collapsed core design in which `MLS-CORE` performs Layer 3 switching and inter-VLAN routing.

### Version 2

```text
                       INTERNET-SERVER
                              |
                            R-ISP
                              |
                          R-INTERNET
                         /          \
               MLS-CORE-1 ===== MLS-CORE-2
                    ||               ||
                    ||               ||
             ===============================
                   Access Layer Switches
```

Version 2 removes the single point of failure at the core layer by introducing a second multilayer switch and redundant uplinks.

---

## Network Segmentation

The internal addressing space is:

```text
192.168.0.0/22
```

VLSM is used to assign subnet sizes according to the estimated number of devices in each department.

| VLAN | Name | Network | Prefix | Gateway | Capacity |
|-----:|------|---------|:------:|---------|---------:|
| 10 | ALUMNOS | `192.168.0.0` | `/23` | `192.168.0.1` | 510 hosts |
| 20 | PROFESORES | `192.168.2.0` | `/26` | `192.168.2.1` | 62 hosts |
| 40 | INVITADOS | `192.168.2.64` | `/27` | `192.168.2.65` | 30 hosts |
| 50 | SERVERS | `192.168.2.96` | `/29` | `192.168.2.97` | 6 hosts |
| 99 | MGMT | `192.168.2.104` | `/29` | `192.168.2.105` | 6 hosts |
| 30 | ADMINS | `192.168.2.112` | `/29` | `192.168.2.113` | 6 hosts |

The largest subnet is assigned to the student network, while smaller infrastructure and administration networks use more restrictive prefixes to reduce address waste.

---

## Network Services

### Inter-VLAN Routing

The multilayer core switch provides Layer 3 routing between VLANs through Switch Virtual Interfaces.

```cisco
ip routing
```

Each SVI acts as the default gateway for its corresponding VLAN.

In Version 2, the physical SVI addresses are separated from the default gateway presented to clients. HSRP provides a shared virtual gateway between both multilayer switches.

---

### Centralized DHCP

DHCP is hosted on the internal server:

```text
SERVER-WEB
192.168.2.98
```

The following client networks receive their addressing dynamically:

- ALUMNOS
- PROFESORES
- INVITADOS
- ADMINS

DHCP Relay is configured on the VLAN interfaces:

```cisco
ip helper-address 192.168.2.98
```

Servers and management devices use static addressing.

---

### Internet Simulation

Internet access is simulated using:

- An edge router: `R-INTERNET`
- An ISP router: `R-ISP`
- An external server: `INTERNET-SERVER`

```text
Internal Network
       |
   R-INTERNET
       |
     R-ISP
       |
INTERNET-SERVER
```

The ISP transit link uses:

```text
203.0.113.0/30
```

The simulated external server uses:

```text
8.8.8.8/24
```

---

## Routing Design

Static routes provide connectivity between the internal network, the edge router and the simulated ISP.

### Internal default route

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

### Route to the internal network

```cisco
ip route 192.168.0.0 255.255.252.0 10.0.0.2
```

### Edge default route

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

Version 2 will extend this routing design so that both multilayer switches maintain connectivity with the edge layer.

---

## NAT/PAT Design

NAT/PAT is configured on `R-INTERNET`.

Each VLAN is translated through a different public IP address. This makes it possible to identify the source department from the translated address.

| VLAN | Private Network | Public NAT IP |
|-----:|-----------------|---------------|
| 10 — ALUMNOS | `192.168.0.0/23` | `203.0.113.10` |
| 20 — PROFESORES | `192.168.2.0/26` | `203.0.113.11` |
| 40 — INVITADOS | `192.168.2.64/27` | `203.0.113.12` |
| 30 — ADMINS | `192.168.2.112/29` | `203.0.113.13` |
| 50 — SERVERS | `192.168.2.96/29` | `203.0.113.14` |

Example configuration:

```cisco
ip nat inside source list NAT_ALUMNOS pool NAT_POOL_ALUMNOS overload
ip nat inside source list NAT_PROFESORES pool NAT_POOL_PROFESORES overload
ip nat inside source list NAT_INVITADOS pool NAT_POOL_INVITADOS overload
ip nat inside source list NAT_ADMINS pool NAT_POOL_ADMINS overload
ip nat inside source list NAT_SERVERS pool NAT_POOL_SERVERS overload
```

---

## Security Design

### Traffic Filtering

Extended ACLs control communication between departments.

| Source | Destination | Policy |
|--------|-------------|--------|
| ALUMNOS | SERVERS | Denied |
| INVITADOS | Internal networks | Denied |
| PROFESORES | SERVERS | Permitted |
| ADMINS | SERVERS | Permitted |
| ADMINS | Network management | Permitted |
| Other VLANs | SSH management | Denied |
| Internal VLANs | External network | Permitted through NAT/PAT |

Example student policy:

```cisco
ip access-list extended ACL_ALUMNOS
 deny ip 192.168.0.0 0.0.1.255 192.168.2.96 0.0.0.7
 permit ip any any
```

Example guest policy:

```cisco
ip access-list extended ACL_INVITADOS
 deny ip 192.168.2.64 0.0.0.31 192.168.0.0 0.0.3.255
 permit ip any any
```

---

### Secure Management

Remote administration is performed through SSH.

Only devices inside the administration VLAN are allowed to establish SSH management sessions.

```text
Administration network: 192.168.2.112/29
```

```cisco
ip access-list standard SSH_ADMINS_ONLY
 permit 192.168.2.112 0.0.0.7
 deny any
```

```cisco
line vty 0 15
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

---

## High Availability Design

Version 2 introduces redundancy at the core and distribution functions.

### HSRP

HSRP provides a virtual default gateway shared by both multilayer switches.

- `MLS-CORE-1` operates as the preferred active gateway.
- `MLS-CORE-2` operates as the standby gateway.
- Clients use the HSRP virtual IP as their default gateway.
- Gateway functionality moves to the standby switch if the active device fails.

### Rapid PVST

Rapid PVST provides a predictable, loop-free Layer 2 topology and faster reconvergence.

- `MLS-CORE-1` is configured as the preferred root bridge.
- `MLS-CORE-2` is configured as the secondary root bridge.
- Redundant paths remain available without creating switching loops.

### Redundant Uplinks

Access switches are connected to both core switches.

This design allows traffic to use an alternative path when a core-facing link or multilayer switch becomes unavailable.

---

## Validation

### Version 1 Tests

| Test | Expected Result | Status |
|------|-----------------|:------:|
| Client DHCP assignment | Address received from correct pool | ✅ |
| Inter-VLAN routing | Allowed networks communicate | ✅ |
| ALUMNOS to SERVERS | Traffic blocked | ✅ |
| INVITADOS to internal networks | Traffic blocked | ✅ |
| PROFESORES to SERVER-WEB | Connectivity successful | ✅ |
| ADMINS to SERVER-WEB | Connectivity successful | ✅ |
| Admin SSH access | Successful | ✅ |
| Non-admin SSH access | Blocked | ✅ |
| External server access | Successful through NAT/PAT | ✅ |
| NAT translation per VLAN | Correct public IP used | ✅ |

### Version 2 Tests

The following tests will be completed as part of Version 2:

- HSRP active and standby state verification
- Active gateway failure
- Core switch shutdown
- Access uplink failure
- Rapid PVST reconvergence
- Inter-VLAN connectivity after failover
- DHCP operation after failover
- Internet access after failover
- Recovery of the preferred core switch

---

## Useful Verification Commands

### VLAN and trunking

```cisco
show vlan brief
show interfaces trunk
```

### Layer 3 routing

```cisco
show ip interface brief
show ip route
```

### DHCP Relay

```cisco
show running-config interface vlan 10
```

### ACLs

```cisco
show access-lists
show ip interface
```

### NAT/PAT

```cisco
show ip nat translations
show ip nat statistics
```

### SSH

```cisco
show ip ssh
show users
```

### High Availability

```cisco
show standby brief
show spanning-tree
show spanning-tree root
```

---

## Repository Structure

```text
enterprise-school-network/
│
├── README.md
│
├── topology/
│   ├── v1/
│   │   ├── README.md
│   │   ├── topology.png
│   │   └── enterprise-school-network-v1.pkt
│   │
│   └── v2/
│       ├── README.md
│       ├── topology.png
│       └── enterprise-school-network-v2.pkt
│
├── configs/
│   ├── README.md
│   ├── MLS-CORE-1.txt
│   ├── MLS-CORE-2.txt
│   ├── R-INTERNET.txt
│   ├── R-ISP.txt
│   ├── SW-ALUMNOS.txt
│   ├── SW-PROFESORES.txt
│   ├── SW-INVITADOS.txt
│   ├── SW-SERVERS.txt
│   └── SW-ADMINS.txt
│
├── docs/
│   ├── addressing-plan.md
│   ├── vlsm-calculation.md
│   ├── vlan-design.md
│   └── network-services.md
│
├── security/
│   ├── acl-policy.md
│   └── ssh-management.md
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

## Documentation

### Project Versions

- [Version 1 — Functional Network](topology/v1/README.md)
- [Version 2 — High Availability](topology/v2/README.md)

### Network Design

- [Addressing Plan](docs/addressing-plan.md)
- [VLSM Calculation](docs/vlsm-calculation.md)
- [VLAN Design](docs/vlan-design.md)
- [Network Services](docs/network-services.md)

### Security

- [ACL Security Policy](security/acl-policy.md)
- [SSH Management](security/ssh-management.md)

### Validation

- [Connectivity Tests](tests/connectivity-tests.md)
- [DHCP Tests](tests/dhcp-tests.md)
- [ACL Tests](tests/acl-tests.md)
- [SSH Tests](tests/ssh-tests.md)
- [NAT Tests](tests/nat-tests.md)
- [High Availability Tests](tests/high-availability-tests.md)

### Device Configurations

- [Configuration Directory](configs/README.md)
- [MLS-CORE-1](configs/MLS-CORE-1.txt)
- [MLS-CORE-2](configs/MLS-CORE-2.txt)
- [R-INTERNET](configs/R-INTERNET.txt)
- [R-ISP](configs/R-ISP.txt)
- [SW-ALUMNOS](configs/SW-ALUMNOS.txt)
- [SW-PROFESORES](configs/SW-PROFESORES.txt)
- [SW-INVITADOS](configs/SW-INVITADOS.txt)
- [SW-SERVERS](configs/SW-SERVERS.txt)
- [SW-ADMINS](configs/SW-ADMINS.txt)

---

## Technologies and Concepts

- Cisco Packet Tracer
- Cisco IOS
- Enterprise Network Design
- Layer 2 Switching
- Layer 3 Switching
- VLANs and 802.1Q Trunking
- VLSM
- Switch Virtual Interfaces
- Inter-VLAN Routing
- DHCP and DHCP Relay
- Standard and Extended ACLs
- SSH Management
- Static Routing
- NAT/PAT
- ISP Simulation
- HSRP
- Rapid PVST
- High Availability
- Failure Testing

---

## Skills Demonstrated

This project demonstrates practical knowledge in:

- Designing segmented enterprise networks
- Planning IPv4 addressing with VLSM
- Configuring Cisco routers and switches
- Implementing centralized network services
- Applying traffic filtering policies
- Securing remote device administration
- Configuring NAT/PAT for external connectivity
- Designing redundant network infrastructure
- Testing and troubleshooting network failures
- Documenting technical projects using Git and Markdown

---

## Future Improvements

Potential future versions may include:

- EtherChannel
- OSPF dynamic routing
- Dual edge routers
- Dual ISP connectivity
- IPv6 addressing and routing
- Internal DNS services
- Syslog centralization
- SNMP monitoring
- NTP synchronization
- Port Security
- DHCP Snooping
- Dynamic ARP Inspection
- Network monitoring dashboard

---

## Author

**Samuel Saiz Retama**

ASIR Student | Networking & Systems Administration

This project was developed as a practical learning environment and portfolio project to demonstrate enterprise networking, security, High Availability and technical documentation skills.

---

## License

This repository is intended for educational purposes and portfolio demonstration.

The configurations and topology may be used as a learning reference in controlled laboratory environments.
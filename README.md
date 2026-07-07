# 🏫 Enterprise School Network

## 📌 Project Overview

This project simulates a **real-world enterprise network** for an educational institution using **Cisco Packet Tracer**.

The network is designed with segmentation, scalability and security in mind. It includes **VLANs**, **VLSM subnetting**, **inter-VLAN routing**, **centralized DHCP**, **ACL-based traffic filtering**, **SSH management restricted to the administration network**, and **NAT/PAT per VLAN** for simulated Internet access.

The design follows a hierarchical network model:

* **Core Layer** → Multilayer Switch
* **Access Layer** → Layer 2 Switches
* **Edge Layer** → Router for external network access
* **ISP Layer** → Simulated ISP router and Internet server

---

## 🎯 Objectives

* Design a realistic school network topology
* Segment the network using VLANs
* Apply VLSM subnetting based on real host requirements
* Configure inter-VLAN routing on a multilayer switch
* Deploy centralized DHCP from the server VLAN
* Apply ACLs to restrict access between departments
* Allow SSH management only from the admin network
* Add simulated Internet access using an ISP router
* Configure NAT/PAT per VLAN using different public IP addresses
* Validate the network using connectivity, DHCP, ACL, SSH and NAT tests

---

## 🧩 Network Architecture

```text
                    INTERNET-SERVER
                          |
                        R-ISP
                          |
                      R-INTERNET
                          |
                       MLS-CORE
          ┌────────────┬────────────┬────────────┬────────────┬────────────┐
          |            |            |            |            |
     SW-ALUMNOS   SW-PROFESORES  SW-INVITADOS  SW-SERVERS  SW-ADMINS
          |            |            |            |            |
         PCs          PCs          PCs        SERVER-WEB      PCs
```

---

## 🖥️ Devices Used

| Device Role     | Device Name       | Model                        |
| --------------- | ----------------- | ---------------------------- |
| ISP Router      | `R-ISP`           | Cisco 2911                   |
| Edge Router     | `R-INTERNET`      | Cisco 2911                   |
| Core Switch     | `MLS-CORE`        | Cisco 3560 Multilayer Switch |
| Students Switch | `SW-ALUMNOS`      | Cisco 2960                   |
| Teachers Switch | `SW-PROFESORES`   | Cisco 2960                   |
| Guests Switch   | `SW-INVITADOS`    | Cisco 2960                   |
| Servers Switch  | `SW-SERVERS`      | Cisco 2960                   |
| Admin Switch    | `SW-ADMINS`       | Cisco 2960                   |
| Internal Server | `SERVER-WEB`      | Server-PT                    |
| External Server | `INTERNET-SERVER` | Server-PT                    |
| Clients         | PCs               | PC-PT                        |

---

## 🌐 Main Internal Network

The main internal network used for this project is:

```text
192.168.0.0/22
```

This provides the following range:

```text
192.168.0.0 - 192.168.3.255
```

This range was selected to allow enough address space for all VLANs and future growth.

---

## 📊 VLAN & Addressing Plan

| VLAN | Name       | Network       | CIDR | Subnet Mask     | Gateway       | Broadcast     | Usable Hosts |
| ---- | ---------- | ------------- | ---- | --------------- | ------------- | ------------- | -----------: |
| 10   | ALUMNOS    | 192.168.0.0   | /23  | 255.255.254.0   | 192.168.0.1   | 192.168.1.255 |          510 |
| 20   | PROFESORES | 192.168.2.0   | /26  | 255.255.255.192 | 192.168.2.1   | 192.168.2.63  |           62 |
| 40   | INVITADOS  | 192.168.2.64  | /27  | 255.255.255.224 | 192.168.2.65  | 192.168.2.95  |           30 |
| 50   | SERVERS    | 192.168.2.96  | /29  | 255.255.255.248 | 192.168.2.97  | 192.168.2.103 |            6 |
| 99   | MGMT       | 192.168.2.104 | /29  | 255.255.255.248 | 192.168.2.105 | 192.168.2.111 |            6 |
| 30   | ADMINS     | 192.168.2.112 | /29  | 255.255.255.248 | 192.168.2.113 | 192.168.2.119 |            6 |

---

## 🧮 VLSM Design

VLSM was used to assign IP ranges according to the estimated number of hosts required by each department.

The main network is:

```text
192.168.0.0/22
```

The VLANs were ordered from largest to smallest host requirement.

### VLAN 10 — ALUMNOS

The students VLAN required around **270 hosts**.

```text
2^8 - 2 = 254 hosts ❌
2^9 - 2 = 510 hosts ✅
```

Therefore, VLAN 10 uses a `/23` subnet:

```text
192.168.0.0/23
```

### VLAN 20 — PROFESORES

The teachers VLAN required around **35 hosts**.

```text
2^5 - 2 = 30 hosts ❌
2^6 - 2 = 62 hosts ✅
```

Therefore, VLAN 20 uses a `/26` subnet:

```text
192.168.2.0/26
```

### Smaller VLANs

Smaller networks were assigned smaller subnets:

```text
INVITADOS → /27
SERVERS   → /29
MGMT      → /29
ADMINS    → /29
```

This avoids wasting IP addresses and keeps the design scalable.

---

## 🔀 Inter-VLAN Routing

Inter-VLAN routing is performed by the multilayer switch:

```text
MLS-CORE
```

Each VLAN has its own SVI configured as the default gateway.

Layer 3 routing is enabled with:

```bash
ip routing
```

Main VLAN gateways:

| VLAN                 | Gateway       |
| -------------------- | ------------- |
| VLAN 10 — ALUMNOS    | 192.168.0.1   |
| VLAN 20 — PROFESORES | 192.168.2.1   |
| VLAN 40 — INVITADOS  | 192.168.2.65  |
| VLAN 50 — SERVERS    | 192.168.2.97  |
| VLAN 99 — MGMT       | 192.168.2.105 |
| VLAN 30 — ADMINS     | 192.168.2.113 |

---

## 📡 DHCP Service

DHCP is centralized on the internal server:

```text
SERVER-WEB → 192.168.2.98
```

DHCP is used for:

* VLAN 10 — ALUMNOS
* VLAN 20 — PROFESORES
* VLAN 40 — INVITADOS
* VLAN 30 — ADMINS

Static addressing is used for:

* VLAN 50 — SERVERS
* VLAN 99 — MGMT

DHCP relay is configured on `MLS-CORE` using:

```bash
ip helper-address 192.168.2.98
```

### DHCP Pools

| VLAN | Pool Name  | Start IP      | Subnet Mask     | Default Gateway |
| ---- | ---------- | ------------- | --------------- | --------------- |
| 10   | ALUMNOS    | 192.168.0.20  | 255.255.254.0   | 192.168.0.1     |
| 20   | PROFESORES | 192.168.2.10  | 255.255.255.192 | 192.168.2.1     |
| 40   | INVITADOS  | 192.168.2.70  | 255.255.255.224 | 192.168.2.65    |
| 30   | ADMINS     | 192.168.2.114 | 255.255.255.248 | 192.168.2.113   |

---

## 🌍 ISP Simulation

An external ISP router and an Internet server were added to simulate Internet access.

### ISP Link

The link between `R-INTERNET` and `R-ISP` uses:

```text
203.0.113.0/30
```

| Device       | Interface | IP Address  |
| ------------ | --------- | ----------- |
| `R-ISP`      | G0/0      | 203.0.113.1 |
| `R-INTERNET` | G0/1      | 203.0.113.2 |

### Internet Server Network

The external Internet server network uses:

```text
8.8.8.0/24
```

| Device            | Interface | IP Address |
| ----------------- | --------- | ---------- |
| `R-ISP`           | G0/1      | 8.8.8.1    |
| `INTERNET-SERVER` | Fa0       | 8.8.8.8    |

The `INTERNET-SERVER` uses the following gateway:

```text
8.8.8.1
```

---

## 🛣️ Routing to External Network

Static routing is used between `MLS-CORE`, `R-INTERNET` and `R-ISP`.

### On MLS-CORE

Default route toward `R-INTERNET`:

```bash
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

### On R-INTERNET

Route back to the internal school network:

```bash
ip route 192.168.0.0 255.255.252.0 10.0.0.2
```

Default route toward `R-ISP`:

```bash
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

### On R-ISP

Route toward the internal school network:

```bash
ip route 192.168.0.0 255.255.252.0 203.0.113.2
```

Route toward the transit network between `MLS-CORE` and `R-INTERNET`:

```bash
ip route 10.0.0.0 255.255.255.252 203.0.113.2
```

Route toward the NAT public pool:

```bash
ip route 203.0.113.8 255.255.255.248 203.0.113.2
```

---

## 🔁 NAT/PAT per VLAN

NAT/PAT is configured on `R-INTERNET`.

Each VLAN uses a different public IP address when accessing the external network. This allows the administrator to identify which VLAN generated the traffic.

| VLAN | Name       | Private Network  | Public NAT IP |
| ---- | ---------- | ---------------- | ------------- |
| 10   | ALUMNOS    | 192.168.0.0/23   | 203.0.113.10  |
| 20   | PROFESORES | 192.168.2.0/26   | 203.0.113.11  |
| 40   | INVITADOS  | 192.168.2.64/27  | 203.0.113.12  |
| 30   | ADMINS     | 192.168.2.112/29 | 203.0.113.13  |
| 50   | SERVERS    | 192.168.2.96/29  | 203.0.113.14  |

### NAT Inside and Outside Interfaces

```bash
interface gigabitEthernet 0/0
 description Link to MLS-CORE
 ip nat inside

interface gigabitEthernet 0/1
 description Link to R-ISP
 ip nat outside
```

### NAT ACLs

```bash
ip access-list standard NAT_ALUMNOS
 permit 192.168.0.0 0.0.1.255

ip access-list standard NAT_PROFESORES
 permit 192.168.2.0 0.0.0.63

ip access-list standard NAT_INVITADOS
 permit 192.168.2.64 0.0.0.31

ip access-list standard NAT_ADMINS
 permit 192.168.2.112 0.0.0.7

ip access-list standard NAT_SERVERS
 permit 192.168.2.96 0.0.0.7
```

### NAT Pools

```bash
ip nat pool NAT_POOL_ALUMNOS 203.0.113.10 203.0.113.10 netmask 255.255.255.248
ip nat pool NAT_POOL_PROFESORES 203.0.113.11 203.0.113.11 netmask 255.255.255.248
ip nat pool NAT_POOL_INVITADOS 203.0.113.12 203.0.113.12 netmask 255.255.255.248
ip nat pool NAT_POOL_ADMINS 203.0.113.13 203.0.113.13 netmask 255.255.255.248
ip nat pool NAT_POOL_SERVERS 203.0.113.14 203.0.113.14 netmask 255.255.255.248
```

### NAT Overload

```bash
ip nat inside source list NAT_ALUMNOS pool NAT_POOL_ALUMNOS overload
ip nat inside source list NAT_PROFESORES pool NAT_POOL_PROFESORES overload
ip nat inside source list NAT_INVITADOS pool NAT_POOL_INVITADOS overload
ip nat inside source list NAT_ADMINS pool NAT_POOL_ADMINS overload
ip nat inside source list NAT_SERVERS pool NAT_POOL_SERVERS overload
```

---

## 🔐 Security Policies

The network applies ACLs to control traffic between VLANs.

| Source         | Destination             | Result                  |
| -------------- | ----------------------- | ----------------------- |
| ALUMNOS        | SERVERS                 | Blocked                 |
| INVITADOS      | Internal networks       | Blocked                 |
| PROFESORES     | SERVERS                 | Allowed                 |
| ADMINS         | SERVERS                 | Allowed                 |
| ADMINS         | Network devices via SSH | Allowed                 |
| Other VLANs    | Network devices via SSH | Blocked                 |
| Internal VLANs | INTERNET-SERVER         | Allowed through NAT/PAT |

---

## 🚫 ACL Configuration

### Students ACL

Students are not allowed to access the server VLAN.

```bash
ip access-list extended ACL_ALUMNOS
 deny ip 192.168.0.0 0.0.1.255 192.168.2.96 0.0.0.7
 permit ip any any
```

Applied inbound on VLAN 10:

```bash
interface vlan 10
 ip access-group ACL_ALUMNOS in
```

---

### Guests ACL

Guests are not allowed to access internal school networks.

```bash
ip access-list extended ACL_INVITADOS
 deny ip 192.168.2.64 0.0.0.31 192.168.0.0 0.0.3.255
 permit ip any any
```

Applied inbound on VLAN 40:

```bash
interface vlan 40
 ip access-group ACL_INVITADOS in
```

---

## 🔑 SSH Management

SSH access is restricted to the administration VLAN.

Allowed admin network:

```text
192.168.2.112/29
```

Managed devices:

* `MLS-CORE`
* `R-INTERNET`

Standard ACL used for SSH management:

```bash
ip access-list standard SSH_ADMINS_ONLY
 permit 192.168.2.112 0.0.0.7
 deny any
```

Applied to VTY lines:

```bash
line vty 0 15
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

Example SSH commands from an admin PC:

```bash
ssh -l admin 192.168.2.105
ssh -l admin 10.0.0.1
```

---

## 🧪 Testing & Validation

The following tests were performed:

| Test                             | Expected Result            | Status |
| -------------------------------- | -------------------------- | ------ |
| Student PC → Student Gateway     | Successful                 | ✅      |
| Teacher PC → Teacher Gateway     | Successful                 | ✅      |
| Admin PC → Admin Gateway         | Successful                 | ✅      |
| Guest PC → Guest Gateway         | Successful                 | ✅      |
| Teacher PC → SERVER-WEB          | Successful                 | ✅      |
| Admin PC → SERVER-WEB            | Successful                 | ✅      |
| Student PC → SERVER-WEB          | Blocked                    | ✅      |
| Guest PC → SERVER-WEB            | Blocked                    | ✅      |
| Guest PC → Student Network       | Blocked                    | ✅      |
| Admin PC → MLS-CORE via SSH      | Successful                 | ✅      |
| Admin PC → R-INTERNET via SSH    | Successful                 | ✅      |
| Non-admin VLANs → SSH Management | Blocked                    | ✅      |
| DHCP address assignment          | Successful                 | ✅      |
| Internal VLANs → INTERNET-SERVER | Successful through NAT/PAT | ✅      |
| NAT translation per VLAN         | Successful                 | ✅      |

---

## 🔍 NAT Verification

NAT translations can be verified on `R-INTERNET` using:

```bash
show ip nat translations
```

Expected behavior:

| Internal Source   | Expected Public IP |
| ----------------- | ------------------ |
| Student VLAN host | 203.0.113.10       |
| Teacher VLAN host | 203.0.113.11       |
| Guest VLAN host   | 203.0.113.12       |
| Admin VLAN host   | 203.0.113.13       |
| Server VLAN host  | 203.0.113.14       |

Additional verification command:

```bash
show ip nat statistics
```

---

## 📁 Repository Structure

```text
enterprise-school-network/
│
├── README.md
│
├── topology/
│   ├── topology.png
│   └── enterprise-school-network.pkt
│
├── configs/
│   ├── README.md
│   ├── MLS-CORE.txt
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
    └── nat-tests.md
```

---

## 📚 Documentation

### Design Documentation

* [Addressing Plan](docs/addressing-plan.md)
* [VLSM Calculation](docs/vlsm-calculation.md)
* [VLAN Design](docs/vlan-design.md)
* [Network Services](docs/network-services.md)

### Security Documentation

* [ACL Security Policy](security/acl-policy.md)
* [SSH Management](security/ssh-management.md)

### Testing Documentation

* [Connectivity Tests](tests/connectivity-tests.md)
* [DHCP Tests](tests/dhcp-tests.md)
* [ACL Tests](tests/acl-tests.md)
* [SSH Tests](tests/ssh-tests.md)
* [NAT Tests](tests/nat-tests.md)

### Device Configurations

* [Configuration Folder](configs/README.md)
* [MLS-CORE Configuration](configs/MLS-CORE.txt)
* [R-INTERNET Configuration](configs/R-INTERNET.txt)
* [R-ISP Configuration](configs/R-ISP.txt)
* [SW-ALUMNOS Configuration](configs/SW-ALUMNOS.txt)
* [SW-PROFESORES Configuration](configs/SW-PROFESORES.txt)
* [SW-INVITADOS Configuration](configs/SW-INVITADOS.txt)
* [SW-SERVERS Configuration](configs/SW-SERVERS.txt)
* [SW-ADMINS Configuration](configs/SW-ADMINS.txt)

---

## 🛠️ Technologies Used

* Cisco Packet Tracer
* Cisco IOS
* VLANs
* Trunking
* VLSM
* Inter-VLAN Routing
* DHCP
* DHCP Relay
* ACLs
* SSH
* NAT/PAT
* Static Routing
* ISP Simulation
* Layer 2 Switching
* Layer 3 Switching

---

## 🚀 Future Improvements

* Add DNS records for internal services
* Add a web service for the school intranet
* Add STP documentation
* Add EtherChannel between core and access switches
* Add firewall simulation
* Add monitoring using SNMP or Syslog
* Add redundancy between core and edge layers

---

## 👨‍💻 Author

**Samuel Saiz Retama**
ASIR Student | Networking & Systems

---

## 📜 License

This project is intended for educational purposes and portfolio demonstration.

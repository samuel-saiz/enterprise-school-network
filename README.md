# 🏫 Enterprise School Network

## 📌 Project Overview

This project simulates a **real-world enterprise network** for an educational institution using **Cisco Packet Tracer**.

The network is designed with segmentation, scalability and security in mind. It includes **VLANs**, **VLSM subnetting**, **inter-VLAN routing**, **centralized DHCP**, **ACL-based traffic filtering** and **SSH management restricted to the administration network**.

The design follows a hierarchical network model:

* **Core Layer** → Multilayer Switch
* **Access Layer** → Layer 2 Switches
* **Edge Layer** → Router for external network access

---

## 🎯 Objectives

* Design a realistic school network topology
* Segment the network using VLANs
* Apply VLSM subnetting based on real host requirements
* Configure inter-VLAN routing on a multilayer switch
* Deploy centralized DHCP from the server VLAN
* Apply ACLs to restrict access between departments
* Allow SSH management only from the admin network
* Validate the network using connectivity and security tests

---

## 🧩 Network Architecture

```text
                    🌐 External Network
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

| Device Role     | Device Name     | Model                        |
| --------------- | --------------- | ---------------------------- |
| Router          | `R-INTERNET`    | Cisco 2911                   |
| Core Switch     | `MLS-CORE`      | Cisco 3560 Multilayer Switch |
| Students Switch | `SW-ALUMNOS`    | Cisco 2960                   |
| Teachers Switch | `SW-PROFESORES` | Cisco 2960                   |
| Guests Switch   | `SW-INVITADOS`  | Cisco 2960                   |
| Servers Switch  | `SW-SERVERS`    | Cisco 2960                   |
| Admin Switch    | `SW-ADMINS`     | Cisco 2960                   |
| Server          | `SERVER-WEB`    | Server-PT                    |
| Clients         | PCs             | PC-PT                        |

---

## 🌐 Main Network

The main network used for this project is:

```text
192.168.0.0/22
```

This provides the following range:

```text
192.168.0.0 - 192.168.3.255
```

This range was selected to allow enough space for all VLANs and future growth.

---

## 📊 VLAN & Addressing Plan

| VLAN | Name       | Network          | Mask            | Gateway       | Broadcast     | Usable Hosts |
| ---- | ---------- | ---------------- | --------------- | ------------- | ------------- | ------------ |
| 10   | ALUMNOS    | 192.168.0.0/23   | 255.255.254.0   | 192.168.0.1   | 192.168.1.255 | 510          |
| 20   | PROFESORES | 192.168.2.0/26   | 255.255.255.192 | 192.168.2.1   | 192.168.2.63  | 62           |
| 40   | INVITADOS  | 192.168.2.64/27  | 255.255.255.224 | 192.168.2.65  | 192.168.2.95  | 30           |
| 50   | SERVERS    | 192.168.2.96/29  | 255.255.255.248 | 192.168.2.97  | 192.168.2.103 | 6            |
| 99   | MGMT       | 192.168.2.104/29 | 255.255.255.248 | 192.168.2.105 | 192.168.2.111 | 6            |
| 30   | ADMINS     | 192.168.2.112/29 | 255.255.255.248 | 192.168.2.113 | 192.168.2.119 | 6            |

---

## 🧮 VLSM Design

VLSM was applied according to the estimated number of hosts required by each department.

### Students VLAN

The students network required around **270 hosts**.

```text
2^8 - 2 = 254 hosts ❌
2^9 - 2 = 510 hosts ✅
```

Therefore, VLAN 10 uses:

```text
192.168.0.0/23
```

### Teachers VLAN

The teachers network required around **35 hosts**.

```text
2^5 - 2 = 30 hosts ❌
2^6 - 2 = 62 hosts ✅
```

Therefore, VLAN 20 uses:

```text
192.168.2.0/26
```

### Guests, Servers, Management and Admins

Smaller departments use smaller subnets to avoid wasting IP addresses:

```text
Guests  → /27
Servers → /29
MGMT    → /29
Admins  → /29
```

---

## 🔀 Inter-VLAN Routing

Inter-VLAN routing is performed by the multilayer switch `MLS-CORE`.

Each VLAN has its own SVI configured as the default gateway:

```text
VLAN 10 → 192.168.0.1
VLAN 20 → 192.168.2.1
VLAN 40 → 192.168.2.65
VLAN 50 → 192.168.2.97
VLAN 99 → 192.168.2.105
VLAN 30 → 192.168.2.113
```

The multilayer switch has IP routing enabled:

```bash
ip routing
```

---

## 📡 DHCP Service

DHCP is centralized on the server:

```text
SERVER-WEB → 192.168.2.98
```

The server provides DHCP addresses for:

* VLAN 10 — ALUMNOS
* VLAN 20 — PROFESORES
* VLAN 40 — INVITADOS
* VLAN 30 — ADMINS

The following VLANs use static addressing:

* VLAN 50 — SERVERS
* VLAN 99 — MGMT

DHCP relay is configured on the multilayer switch using `ip helper-address`:

```bash
interface vlan 10
 ip helper-address 192.168.2.98

interface vlan 20
 ip helper-address 192.168.2.98

interface vlan 30
 ip helper-address 192.168.2.98

interface vlan 40
 ip helper-address 192.168.2.98
```

---

## 🔐 Security Policies

The network applies ACLs to control traffic between VLANs.

### Security Rules

| Source      | Destination             | Result  |
| ----------- | ----------------------- | ------- |
| ALUMNOS     | SERVERS                 | Blocked |
| INVITADOS   | Internal Networks       | Blocked |
| PROFESORES  | SERVERS                 | Allowed |
| ADMINS      | SERVERS                 | Allowed |
| ADMINS      | Network Devices via SSH | Allowed |
| Other VLANs | Network Devices via SSH | Blocked |

---

## 🚫 ACL Policy

### Students ACL

Students are not allowed to access the server network:

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

Guests are not allowed to access internal networks:

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

Standard ACL used for SSH access control:

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

This allows only admin devices to manage:

* `MLS-CORE`
* `R-INTERNET`

---

## 🧪 Testing & Validation

The following tests were performed to verify the network behavior.

### Connectivity Tests

| Test                         | Expected Result | Status |
| ---------------------------- | --------------- | ------ |
| Student PC → Student Gateway | Successful      | ✅      |
| Teacher PC → Teacher Gateway | Successful      | ✅      |
| Admin PC → Admin Gateway     | Successful      | ✅      |
| Guest PC → Guest Gateway     | Successful      | ✅      |
| Teacher PC → SERVER-WEB      | Successful      | ✅      |
| Admin PC → SERVER-WEB        | Successful      | ✅      |
| Student PC → SERVER-WEB      | Blocked         | ✅      |
| Guest PC → SERVER-WEB        | Blocked         | ✅      |
| Guest PC → Students Network  | Blocked         | ✅      |

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
│   ├── MLS-CORE.txt
│   ├── R-INTERNET.txt
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
    └── acl-tests.md
```

---

## 📂 Folder Description

### `topology/`

Contains the Packet Tracer topology file and an image of the final network design.

### `configs/`

Contains the running configuration of each network device.

Recommended command:

```bash
show running-config
```

### `docs/`

Contains documentation about the addressing plan, VLSM calculation, VLAN design and network services.

### `security/`

Contains ACL documentation and SSH management restrictions.

### `tests/`

Contains evidence of network validation, such as ping tests, DHCP tests, ACL tests and screenshots.

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
* Layer 2 Switching
* Layer 3 Switching

---

## 🚀 Future Improvements

* Add NAT for simulated internet access
* Add DNS records for internal services
* Add a web service for the school intranet
* Add STP documentation
* Add EtherChannel between core and access switches
* Add firewall simulation
* Add monitoring using SNMP or Syslog

---

## 👨‍💻 Author

**Samuel Saiz Retama**
ASIR Student | Networking & Systems

---

## 📜 License

This project is intended for educational purposes and portfolio demonstration.

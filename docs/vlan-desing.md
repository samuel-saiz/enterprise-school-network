# 🧩 VLAN Design

This document explains the VLAN design used in the **Enterprise School Network** project.

The network is segmented into different VLANs to separate departments, improve security and make the network easier to manage.

---

## 🎯 Objective

The main objective of the VLAN design is to separate network traffic according to the type of user or device.

This provides:

* Better network organization
* Improved security
* Easier troubleshooting
* Scalable network growth
* Controlled access between departments

---

## 📊 VLAN Summary

| VLAN | Name       | Purpose                   |
| ---- | ---------- | ------------------------- |
| 10   | ALUMNOS    | Student devices           |
| 20   | PROFESORES | Teacher devices           |
| 40   | INVITADOS  | Guest devices             |
| 50   | SERVERS    | Internal servers          |
| 99   | MGMT       | Network device management |
| 30   | ADMINS     | Administration devices    |

---

## 🏫 VLAN 10 — ALUMNOS

The `ALUMNOS` VLAN is used for student devices.

This VLAN has the largest subnet because it represents the highest number of expected devices in the school network.

```text
VLAN ID: 10
Name: ALUMNOS
Network: 192.168.0.0/23
Gateway: 192.168.0.1
```

### Design Reason

Students require more IP addresses because the school can have several computer classrooms and many workstations.

In this project, only a small number of PCs are simulated in Packet Tracer, but the subnet is designed to support future growth.

---

## 👨‍🏫 VLAN 20 — PROFESORES

The `PROFESORES` VLAN is used for teacher devices.

```text
VLAN ID: 20
Name: PROFESORES
Network: 192.168.2.0/26
Gateway: 192.168.2.1
```

### Design Reason

Teachers need access to internal resources such as the server VLAN, but they should remain separated from students and guests.

---

## 🌐 VLAN 40 — INVITADOS

The `INVITADOS` VLAN is used for guest users.

```text
VLAN ID: 40
Name: INVITADOS
Network: 192.168.2.64/27
Gateway: 192.168.2.65
```

### Design Reason

Guest users should have restricted access.

They are not allowed to access internal networks such as:

* Students VLAN
* Teachers VLAN
* Admin VLAN
* Servers VLAN
* Management VLAN

This restriction is enforced using ACLs.

---

## 🖥️ VLAN 50 — SERVERS

The `SERVERS` VLAN is used for internal servers.

```text
VLAN ID: 50
Name: SERVERS
Network: 192.168.2.96/29
Gateway: 192.168.2.97
```

### Devices

```text
SERVER-WEB → 192.168.2.98
```

### Design Reason

Servers are separated into their own VLAN to improve security and make access control easier.

Only authorized VLANs can access the server network.

---

## 🔧 VLAN 99 — MGMT

The `MGMT` VLAN is used for network device management.

```text
VLAN ID: 99
Name: MGMT
Network: 192.168.2.104/29
Gateway: 192.168.2.105
```

### Design Reason

Management traffic should be separated from normal user traffic.

This VLAN is used to manage network infrastructure such as:

* Multilayer switch
* Access switches
* Router
* Future access points or firewalls

---

## 🧑‍💼 VLAN 30 — ADMINS

The `ADMINS` VLAN is used for administration devices.

```text
VLAN ID: 30
Name: ADMINS
Network: 192.168.2.112/29
Gateway: 192.168.2.113
```

### Design Reason

Admin devices require higher privileges than other users.

This VLAN is allowed to:

* Access the server VLAN
* Manage network devices using SSH
* Reach the router and core switch

SSH access is restricted so only this VLAN can manage the network devices.

---

## 🔀 Inter-VLAN Routing

Inter-VLAN routing is performed by the multilayer switch:

```text
MLS-CORE
```

Each VLAN has an SVI configured as its default gateway.

Example:

```bash
interface vlan 10
 ip address 192.168.0.1 255.255.254.0
 no shutdown
```

Layer 3 routing is enabled with:

```bash
ip routing
```

---

## 🔌 Access Switch Design

Each access switch represents one department or network group.

| Switch          | VLAN    |
| --------------- | ------- |
| `SW-ALUMNOS`    | VLAN 10 |
| `SW-PROFESORES` | VLAN 20 |
| `SW-INVITADOS`  | VLAN 40 |
| `SW-SERVERS`    | VLAN 50 |
| `SW-ADMINS`     | VLAN 30 |

---

## 🔗 Trunk Links

The links between `MLS-CORE` and the access switches are configured as trunk links.

Each trunk only allows the required VLAN and the management VLAN.

Example:

```bash
interface fa0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,99
```

This improves control and avoids unnecessary VLAN traffic on trunks.

---

## 🖥️ Access Ports

End devices are connected to access ports.

Example for student devices:

```bash
interface range fa0/2 - 24
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
```

---

## ✅ Design Summary

This VLAN design provides:

* Logical department separation
* Efficient network management
* Better security
* Controlled access between VLANs
* Clear scalability for future expansion

The final topology follows a hierarchical structure:

```text
R-INTERNET
    |
MLS-CORE
    |
Access Switches
    |
End Devices
```

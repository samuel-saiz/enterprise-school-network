# 🌐 Addressing Plan

This document contains the IP addressing plan used in the **Enterprise School Network** project.

The network was designed using **VLSM** to assign IP ranges according to the estimated number of hosts required by each department.

---

## 📌 Main Network

```text
Main Network: 192.168.0.0/22
Subnet Mask: 255.255.252.0
Total Addresses: 1024
Usable Hosts: 1022
Range: 192.168.0.0 - 192.168.3.255
```

The `/22` network provides enough space for all VLANs and allows future growth.

---

## 📊 VLAN Addressing Table

| VLAN | Name       | Network       | CIDR | Subnet Mask     | Gateway       | Broadcast     | Usable Hosts |
| ---- | ---------- | ------------- | ---- | --------------- | ------------- | ------------- | ------------ |
| 10   | ALUMNOS    | 192.168.0.0   | /23  | 255.255.254.0   | 192.168.0.1   | 192.168.1.255 | 510          |
| 20   | PROFESORES | 192.168.2.0   | /26  | 255.255.255.192 | 192.168.2.1   | 192.168.2.63  | 62           |
| 40   | INVITADOS  | 192.168.2.64  | /27  | 255.255.255.224 | 192.168.2.65  | 192.168.2.95  | 30           |
| 50   | SERVERS    | 192.168.2.96  | /29  | 255.255.255.248 | 192.168.2.97  | 192.168.2.103 | 6            |
| 99   | MGMT       | 192.168.2.104 | /29  | 255.255.255.248 | 192.168.2.105 | 192.168.2.111 | 6            |
| 30   | ADMINS     | 192.168.2.112 | /29  | 255.255.255.248 | 192.168.2.113 | 192.168.2.119 | 6            |

---

## 🧠 VLAN Purpose

### VLAN 10 — ALUMNOS

This VLAN is used for student devices.

It has the largest subnet because the school can have many student workstations in classrooms and computer labs.

```text
Network: 192.168.0.0/23
Gateway: 192.168.0.1
Usable Range: 192.168.0.1 - 192.168.1.254
```

---

### VLAN 20 — PROFESORES

This VLAN is used for teacher devices.

```text
Network: 192.168.2.0/26
Gateway: 192.168.2.1
Usable Range: 192.168.2.1 - 192.168.2.62
```

---

### VLAN 40 — INVITADOS

This VLAN is used for guest users.

Guest traffic is restricted using ACLs to prevent access to internal school networks.

```text
Network: 192.168.2.64/27
Gateway: 192.168.2.65
Usable Range: 192.168.2.65 - 192.168.2.94
```

---

### VLAN 50 — SERVERS

This VLAN is used for internal servers.

The main server in this project is:

```text
SERVER-WEB: 192.168.2.98
Gateway: 192.168.2.97
```

---

### VLAN 99 — MGMT

This VLAN is used for network device management.

It is used to manage switches and network infrastructure.

```text
Network: 192.168.2.104/29
Gateway: 192.168.2.105
```

---

### VLAN 30 — ADMINS

This VLAN is used for administration devices.

Admin devices have access to internal services and are allowed to manage network devices through SSH.

```text
Network: 192.168.2.112/29
Gateway: 192.168.2.113
Usable Range: 192.168.2.113 - 192.168.2.118
```

---

## ✅ Design Notes

* The students VLAN uses a larger subnet due to the higher number of expected devices.
* Servers and management VLANs use smaller subnets because they require fewer IP addresses.
* The addressing plan avoids overlapping networks.
* All VLANs are inside the main network `192.168.0.0/22`.
* Gateways are configured as SVIs on the multilayer switch `MLS-CORE`.

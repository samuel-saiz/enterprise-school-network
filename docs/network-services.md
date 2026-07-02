# 🧰 Network Services

This document describes the network services implemented in the **Enterprise School Network** project.

The project includes centralized DHCP, DHCP relay, inter-VLAN routing, SSH management and basic connectivity services.

---

## 📌 Services Summary

| Service            | Status      | Description                                       |
| ------------------ | ----------- | ------------------------------------------------- |
| DHCP               | Implemented | Centralized DHCP service from the server VLAN     |
| DHCP Relay         | Implemented | `ip helper-address` configured on VLAN interfaces |
| Inter-VLAN Routing | Implemented | Routing between VLANs performed by `MLS-CORE`     |
| SSH                | Implemented | Remote management restricted to the admin VLAN    |
| ACL Filtering      | Implemented | Traffic restrictions between VLANs                |
| DNS                | Optional    | Can be added for internal name resolution         |
| Web Server         | Optional    | Can be used for an internal school intranet       |

---

## 🖥️ Server VLAN

The server is located in the `SERVERS` VLAN.

```text
VLAN: 50
Name: SERVERS
Network: 192.168.2.96/29
Gateway: 192.168.2.97
```

Main server:

```text
Device: SERVER-WEB
IP Address: 192.168.2.98
Subnet Mask: 255.255.255.248
Default Gateway: 192.168.2.97
DNS Server: 192.168.2.98
```

---

## 📡 DHCP Service

DHCP is centralized on `SERVER-WEB`.

The server provides automatic IP addressing for the following VLANs:

* VLAN 10 — ALUMNOS
* VLAN 20 — PROFESORES
* VLAN 40 — INVITADOS
* VLAN 30 — ADMINS

The following VLANs use static addressing:

* VLAN 50 — SERVERS
* VLAN 99 — MGMT

---

## 📋 DHCP Pools

### VLAN 10 — ALUMNOS

```text
Pool Name: ALUMNOS
Default Gateway: 192.168.0.1
DNS Server: 192.168.2.98
Start IP Address: 192.168.0.20
Subnet Mask: 255.255.254.0
Maximum Number of Users: 300
```

---

### VLAN 20 — PROFESORES

```text
Pool Name: PROFESORES
Default Gateway: 192.168.2.1
DNS Server: 192.168.2.98
Start IP Address: 192.168.2.10
Subnet Mask: 255.255.255.192
Maximum Number of Users: 40
```

---

### VLAN 40 — INVITADOS

```text
Pool Name: INVITADOS
Default Gateway: 192.168.2.65
DNS Server: 192.168.2.98
Start IP Address: 192.168.2.70
Subnet Mask: 255.255.255.224
Maximum Number of Users: 20
```

---

### VLAN 30 — ADMINS

```text
Pool Name: ADMINS
Default Gateway: 192.168.2.113
DNS Server: 192.168.2.98
Start IP Address: 192.168.2.114
Subnet Mask: 255.255.255.248
Maximum Number of Users: 4
```

---

## 🔁 DHCP Relay

Because the DHCP server is located in VLAN 50, DHCP requests from other VLANs must be forwarded by the multilayer switch.

This is done using `ip helper-address` on the VLAN interfaces.

Configured on `MLS-CORE`:

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

No DHCP relay is needed on VLAN 50 because the DHCP server is directly connected to that VLAN.

---

## 🔀 Inter-VLAN Routing

Inter-VLAN routing is performed by the multilayer switch:

```text
MLS-CORE
```

Each VLAN has a Layer 3 SVI configured as its gateway.

| VLAN                 | Gateway       |
| -------------------- | ------------- |
| VLAN 10 — ALUMNOS    | 192.168.0.1   |
| VLAN 20 — PROFESORES | 192.168.2.1   |
| VLAN 40 — INVITADOS  | 192.168.2.65  |
| VLAN 50 — SERVERS    | 192.168.2.97  |
| VLAN 99 — MGMT       | 192.168.2.105 |
| VLAN 30 — ADMINS     | 192.168.2.113 |

Layer 3 routing is enabled with:

```bash
ip routing
```

---

## 🛣️ Router Connection

The core switch is connected to the router using a point-to-point network.

```text
Network: 10.0.0.0/30
```

| Device       | IP Address |
| ------------ | ---------- |
| `R-INTERNET` | 10.0.0.1   |
| `MLS-CORE`   | 10.0.0.2   |

Static route on `MLS-CORE`:

```bash
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

Static route on `R-INTERNET`:

```bash
ip route 192.168.0.0 255.255.252.0 10.0.0.2
```

---

## 🔑 SSH Management

SSH is used for remote administration of network devices.

SSH access is restricted to the admin VLAN:

```text
VLAN 30 ADMINS
Network: 192.168.2.112/29
```

Only admin devices can access:

* `MLS-CORE`
* `R-INTERNET`

Example SSH access from an admin PC:

```bash
ssh -l admin 192.168.2.105
ssh -l admin 10.0.0.1
```

---

## 🔐 ACL Filtering

ACLs are used to enforce security policies.

Main restrictions:

| Source     | Destination             | Result  |
| ---------- | ----------------------- | ------- |
| ALUMNOS    | SERVERS                 | Blocked |
| INVITADOS  | Internal networks       | Blocked |
| PROFESORES | SERVERS                 | Allowed |
| ADMINS     | SERVERS                 | Allowed |
| ADMINS     | Network devices via SSH | Allowed |

---

## 🧪 Service Validation

The following services were tested successfully:

| Test                     | Expected Result                         | Status |
| ------------------------ | --------------------------------------- | ------ |
| DHCP address assignment  | Clients receive automatic IP            | ✅      |
| DHCP relay               | Clients from different VLANs receive IP | ✅      |
| Inter-VLAN routing       | Allowed VLANs communicate               | ✅      |
| ACL filtering            | Restricted traffic is blocked           | ✅      |
| SSH from admin VLAN      | Successful                              | ✅      |
| SSH from non-admin VLANs | Blocked                                 | ✅      |

---

## 🚀 Future Improvements

Possible future improvements:

* Add internal DNS records
* Add a web-based intranet service
* Add NAT for simulated internet access
* Add Syslog logging
* Add SNMP monitoring
* Add NTP time synchronization
* Add redundancy with EtherChannel or STP documentation

# 🧪 Connectivity Tests

This document contains the connectivity tests performed in the **Enterprise School Network** project.

The purpose of these tests is to verify that VLAN gateways, inter-VLAN routing and allowed communications are working correctly.

---

## 🎯 Objective

The objective is to validate that:

* Each PC can reach its default gateway.
* Inter-VLAN routing works through `MLS-CORE`.
* Authorized VLANs can reach the server.
* Restricted VLANs are blocked by ACLs.
* The admin VLAN can reach the router.

---

## 🌐 Gateway Tests

Each client must be able to reach its own default gateway.

| Source Device | VLAN    | Destination Gateway | Expected Result | Status |
| ------------- | ------- | ------------------- | --------------- | ------ |
| `PC-ALUMNO`   | VLAN 10 | 192.168.0.1         | Successful      | ✅      |
| `PC-PROFE`    | VLAN 20 | 192.168.2.1         | Successful      | ✅      |
| `PC-INVITADO` | VLAN 40 | 192.168.2.65        | Successful      | ✅      |
| `PC-ADMIN`    | VLAN 30 | 192.168.2.113       | Successful      | ✅      |
| `SERVER-WEB`  | VLAN 50 | 192.168.2.97        | Successful      | ✅      |

---

## 🧪 Gateway Test Commands

### Student PC

```bash
ping 192.168.0.1
```

Expected result:

```text
Reply from 192.168.0.1
```

---

### Teacher PC

```bash
ping 192.168.2.1
```

Expected result:

```text
Reply from 192.168.2.1
```

---

### Guest PC

```bash
ping 192.168.2.65
```

Expected result:

```text
Reply from 192.168.2.65
```

---

### Admin PC

```bash
ping 192.168.2.113
```

Expected result:

```text
Reply from 192.168.2.113
```

---

### Server

```bash
ping 192.168.2.97
```

Expected result:

```text
Reply from 192.168.2.97
```

---

## 🔀 Inter-VLAN Routing Tests

Inter-VLAN routing is performed by the multilayer switch:

```text
MLS-CORE
```

The following tests verify that allowed VLANs can communicate through the core switch.

| Source     | Destination               | Expected Result | Status |
| ---------- | ------------------------- | --------------- | ------ |
| `PC-PROFE` | `SERVER-WEB` 192.168.2.98 | Successful      | ✅      |
| `PC-ADMIN` | `SERVER-WEB` 192.168.2.98 | Successful      | ✅      |
| `PC-ADMIN` | `R-INTERNET` 10.0.0.1     | Successful      | ✅      |

---

## 🧪 Inter-VLAN Test Commands

### Teacher to Server

From `PC-PROFE`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

---

### Admin to Server

From `PC-ADMIN`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

---

### Admin to Router

From `PC-ADMIN`:

```bash
ping 10.0.0.1
```

Expected result:

```text
Reply from 10.0.0.1
```

---

## 🚫 Restricted Connectivity Tests

These tests verify that ACLs are working correctly.

| Source        | Destination               | Expected Result | Status |
| ------------- | ------------------------- | --------------- | ------ |
| `PC-ALUMNO`   | `SERVER-WEB` 192.168.2.98 | Blocked         | ✅      |
| `PC-INVITADO` | `SERVER-WEB` 192.168.2.98 | Blocked         | ✅      |
| `PC-INVITADO` | Student network           | Blocked         | ✅      |

---

## 🧪 Restricted Test Commands

### Student to Server

From `PC-ALUMNO`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

---

### Guest to Server

From `PC-INVITADO`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

---

### Guest to Student Network

From `PC-INVITADO`:

```bash
ping 192.168.0.20
```

Expected result:

```text
Request timed out / Lost packets
```

---

## ✅ Core Verification Commands

On `MLS-CORE`:

```bash
show ip interface brief
show ip route
show interfaces trunk
```

Expected result:

* VLAN interfaces should be `up/up`.
* Connected routes should appear in the routing table.
* Trunk links should be active.

---

## 📌 Notes

The first ping may fail because of ARP resolution. This is normal.

A test is considered successful if the following packets receive replies.

Blocked traffic is considered successful when packets are lost or timed out.

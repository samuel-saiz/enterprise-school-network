# 🔐 ACL Tests

This document contains the ACL validation tests performed in the **Enterprise School Network** project.

The purpose of these tests is to verify that the configured ACLs correctly block unauthorized traffic while allowing legitimate communication.

---

## 🎯 Objective

The objective is to validate that:

* Students cannot access the server VLAN.
* Guests cannot access internal school networks.
* Teachers can access the server VLAN.
* Admins can access the server VLAN.
* Gateways remain reachable from each VLAN.
* ACLs do not break valid network communication.

---

## 🔐 Security Rules Tested

| Source VLAN | Destination       | Expected Result |
| ----------- | ----------------- | --------------- |
| ALUMNOS     | SERVERS           | Blocked         |
| INVITADOS   | SERVERS           | Blocked         |
| INVITADOS   | Internal networks | Blocked         |
| PROFESORES  | SERVERS           | Allowed         |
| ADMINS      | SERVERS           | Allowed         |
| ALUMNOS     | Own gateway       | Allowed         |
| INVITADOS   | Own gateway       | Allowed         |

---

## 🌐 Relevant Networks

| VLAN | Name       | Network          | Gateway       |
| ---- | ---------- | ---------------- | ------------- |
| 10   | ALUMNOS    | 192.168.0.0/23   | 192.168.0.1   |
| 20   | PROFESORES | 192.168.2.0/26   | 192.168.2.1   |
| 40   | INVITADOS  | 192.168.2.64/27  | 192.168.2.65  |
| 50   | SERVERS    | 192.168.2.96/29  | 192.168.2.97  |
| 30   | ADMINS     | 192.168.2.112/29 | 192.168.2.113 |

---

## 🚫 ACL_ALUMNOS

Students are not allowed to access the server VLAN.

### ACL Configuration

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

## 🚫 ACL_INVITADOS

Guests are not allowed to access internal school networks.

### ACL Configuration

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

## 🧪 Test 1 — Student to Server

From `PC-ALUMNO`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

Status:

```text
Blocked ✅
```

---

## 🧪 Test 2 — Guest to Server

From `PC-INVITADO`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

Status:

```text
Blocked ✅
```

---

## 🧪 Test 3 — Guest to Student Network

From `PC-INVITADO`:

```bash
ping 192.168.0.20
```

Expected result:

```text
Request timed out / Lost packets
```

Status:

```text
Blocked ✅
```

---

## 🧪 Test 4 — Teacher to Server

From `PC-PROFE`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

Status:

```text
Allowed ✅
```

---

## 🧪 Test 5 — Admin to Server

From `PC-ADMIN`:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

Status:

```text
Allowed ✅
```

---

## 🧪 Test 6 — Student to Gateway

From `PC-ALUMNO`:

```bash
ping 192.168.0.1
```

Expected result:

```text
Reply from 192.168.0.1
```

Status:

```text
Allowed ✅
```

---

## 🧪 Test 7 — Guest to Gateway

From `PC-INVITADO`:

```bash
ping 192.168.2.65
```

Expected result:

```text
Reply from 192.168.2.65
```

Status:

```text
Allowed ✅
```

---

## 📊 ACL Test Summary

| Test                    | Expected Result | Status |
| ----------------------- | --------------- | ------ |
| Student → Server        | Blocked         | ✅      |
| Guest → Server          | Blocked         | ✅      |
| Guest → Student network | Blocked         | ✅      |
| Teacher → Server        | Allowed         | ✅      |
| Admin → Server          | Allowed         | ✅      |
| Student → Gateway       | Allowed         | ✅      |
| Guest → Gateway         | Allowed         | ✅      |

---

## ✅ Verification Commands

On `MLS-CORE`:

```bash
show access-lists
show running-config interface vlan 10
show running-config interface vlan 40
```

Expected result:

* `ACL_ALUMNOS` should be applied inbound on `Vlan10`.
* `ACL_INVITADOS` should be applied inbound on `Vlan40`.
* ACL counters should increase when blocked traffic is tested.

---

## 📌 Notes

ACLs are applied inbound on the source VLAN interfaces.

This prevents restricted traffic from being routed further into the network.

The final `permit ip any any` line is required to allow all other non-restricted traffic.

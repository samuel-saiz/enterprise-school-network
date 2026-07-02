# 🔐 ACL Security Policy

This document explains the ACL security policy implemented in the **Enterprise School Network** project.

ACLs are used to control traffic between VLANs and enforce security rules between different departments.

---

## 🎯 Objective

The objective of the ACL configuration is to restrict unauthorized access between internal networks.

The main rules are:

* Students cannot access the server VLAN.
* Guests cannot access internal school networks.
* Teachers can access the server VLAN.
* Admins have access to the server VLAN.
* Admins are allowed to manage network devices through SSH.

---

## 🌐 Network Summary

| VLAN | Name       | Network          | Wildcard  |
| ---- | ---------- | ---------------- | --------- |
| 10   | ALUMNOS    | 192.168.0.0/23   | 0.0.1.255 |
| 20   | PROFESORES | 192.168.2.0/26   | 0.0.0.63  |
| 40   | INVITADOS  | 192.168.2.64/27  | 0.0.0.31  |
| 50   | SERVERS    | 192.168.2.96/29  | 0.0.0.7   |
| 99   | MGMT       | 192.168.2.104/29 | 0.0.0.7   |
| 30   | ADMINS     | 192.168.2.112/29 | 0.0.0.7   |

---

## 🚫 ACL for Students

Students are not allowed to access the server VLAN.

### ACL Configuration

```bash
ip access-list extended ACL_ALUMNOS
 deny ip 192.168.0.0 0.0.1.255 192.168.2.96 0.0.0.7
 permit ip any any
```

### Applied Interface

The ACL is applied inbound on VLAN 10.

```bash
interface vlan 10
 ip access-group ACL_ALUMNOS in
```

### Explanation

```text
Source network:      192.168.0.0/23
Source wildcard:     0.0.1.255

Destination network: 192.168.2.96/29
Destination wildcard: 0.0.0.7
```

This blocks traffic from the students VLAN to the servers VLAN.

The final line allows the rest of the traffic:

```bash
permit ip any any
```

Without this line, all remaining traffic would be denied by the implicit deny at the end of the ACL.

---

## 🚫 ACL for Guests

Guests are not allowed to access internal school networks.

### ACL Configuration

```bash
ip access-list extended ACL_INVITADOS
 deny ip 192.168.2.64 0.0.0.31 192.168.0.0 0.0.3.255
 permit ip any any
```

### Applied Interface

The ACL is applied inbound on VLAN 40.

```bash
interface vlan 40
 ip access-group ACL_INVITADOS in
```

### Explanation

```text
Source network:      192.168.2.64/27
Source wildcard:     0.0.0.31

Destination network: 192.168.0.0/22
Destination wildcard: 0.0.3.255
```

This blocks guest users from accessing the internal school network.

The final line allows traffic not matching the deny statement.

---

## ✅ Allowed Traffic

The following traffic is allowed:

| Source     | Destination             | Result  |
| ---------- | ----------------------- | ------- |
| PROFESORES | SERVERS                 | Allowed |
| ADMINS     | SERVERS                 | Allowed |
| ALUMNOS    | Own gateway             | Allowed |
| INVITADOS  | Own gateway             | Allowed |
| ADMINS     | Network devices via SSH | Allowed |

---

## ❌ Blocked Traffic

The following traffic is blocked:

| Source    | Destination       | Result  |
| --------- | ----------------- | ------- |
| ALUMNOS   | SERVERS           | Blocked |
| INVITADOS | SERVERS           | Blocked |
| INVITADOS | ALUMNOS           | Blocked |
| INVITADOS | Internal networks | Blocked |

---

## 🧪 Validation Tests

### Student to Server

From a student PC:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

---

### Guest to Server

From a guest PC:

```bash
ping 192.168.2.98
```

Expected result:

```text
Request timed out / Lost packets
```

---

### Teacher to Server

From a teacher PC:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

---

### Admin to Server

From an admin PC:

```bash
ping 192.168.2.98
```

Expected result:

```text
Reply from 192.168.2.98
```

---

## ✅ Verification Commands

On `MLS-CORE`:

```bash
show access-lists
show running-config interface vlan 10
show running-config interface vlan 40
```

The ACL counters should increase when blocked traffic is tested.

---

## 📌 Notes

ACLs are applied close to the source VLANs.

This helps stop unauthorized traffic as soon as it enters the routing device.

In this project:

* `ACL_ALUMNOS` is applied on `interface vlan 10`
* `ACL_INVITADOS` is applied on `interface vlan 40`

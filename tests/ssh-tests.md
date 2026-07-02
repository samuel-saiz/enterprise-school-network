# 🔑 SSH Management Tests

This document contains the SSH management validation tests performed in the **Enterprise School Network** project.

The purpose of these tests is to verify that only devices from the administration VLAN can remotely manage network devices using SSH.

---

## 🎯 Objective

The objective is to validate that:

* Admin devices can access `MLS-CORE` through SSH.
* Admin devices can access `R-INTERNET` through SSH.
* Students cannot access network devices through SSH.
* Teachers cannot access network devices through SSH.
* Guests cannot access network devices through SSH.
* SSH is used instead of Telnet.

---

## 🔐 SSH Security Policy

Only the administration VLAN is allowed to use SSH for network device management.

```text
Allowed Network: 192.168.2.112/29
Wildcard: 0.0.0.7
```

---

## 🌐 Management Targets

| Device       | Management IP | Access |
| ------------ | ------------- | ------ |
| `MLS-CORE`   | 192.168.2.105 | SSH    |
| `R-INTERNET` | 10.0.0.1      | SSH    |

---

## 🔒 SSH Access ACL

The following standard ACL is used to restrict SSH access:

```bash
ip access-list standard SSH_ADMINS_ONLY
 permit 192.168.2.112 0.0.0.7
 deny any
```

Applied to the VTY lines:

```bash
line vty 0 15
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

On the router:

```bash
line vty 0 4
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

---

## 🧪 Test 1 — Admin PC to MLS-CORE

From `PC-ADMIN`:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection successful
```

Status:

```text
Allowed ✅
```

---

## 🧪 Test 2 — Admin PC to R-INTERNET

From `PC-ADMIN`:

```bash
ssh -l admin 10.0.0.1
```

Expected result:

```text
Connection successful
```

Status:

```text
Allowed ✅
```

---

## 🧪 Test 3 — Student PC to MLS-CORE

From `PC-ALUMNO`:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection refused / timed out
```

Status:

```text
Blocked ✅
```

---

## 🧪 Test 4 — Teacher PC to MLS-CORE

From `PC-PROFE`:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection refused / timed out
```

Status:

```text
Blocked ✅
```

---

## 🧪 Test 5 — Guest PC to MLS-CORE

From `PC-INVITADO`:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection refused / timed out
```

Status:

```text
Blocked ✅
```

---

## 📊 SSH Test Summary

| Source        | Destination  | Expected Result | Status |
| ------------- | ------------ | --------------- | ------ |
| `PC-ADMIN`    | `MLS-CORE`   | Allowed         | ✅      |
| `PC-ADMIN`    | `R-INTERNET` | Allowed         | ✅      |
| `PC-ALUMNO`   | `MLS-CORE`   | Blocked         | ✅      |
| `PC-PROFE`    | `MLS-CORE`   | Blocked         | ✅      |
| `PC-INVITADO` | `MLS-CORE`   | Blocked         | ✅      |

---

## ✅ Verification Commands

On `MLS-CORE` and `R-INTERNET`:

```bash
show ip ssh
show access-lists SSH_ADMINS_ONLY
show running-config | section line vty
```

Expected result:

* SSH version 2 should be enabled.
* `SSH_ADMINS_ONLY` should only permit the admin VLAN.
* VTY lines should only allow SSH.
* Telnet should not be allowed.

---

## 📌 Notes

SSH is preferred over Telnet because SSH encrypts the remote management session.

Telnet should not be used because it sends traffic in plain text.

Real passwords should not be published in GitHub. Use placeholders in documentation:

```text
<SECURE_PASSWORD>
<ENABLE_SECRET>
```

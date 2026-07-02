# 🔑 SSH Management

This document explains the SSH management configuration implemented in the **Enterprise School Network** project.

SSH is used to securely manage network devices remotely. Access is restricted so that only devices from the administration VLAN can connect.

---

## 🎯 Objective

The goal of this configuration is to allow only the admin network to manage critical network devices.

Managed devices:

* `MLS-CORE`
* `R-INTERNET`

Allowed source network:

```text
VLAN 30 ADMINS
Network: 192.168.2.112/29
Wildcard: 0.0.0.7
```

---

## 🔐 Security Policy

| Source     | Destination | Protocol | Result  |
| ---------- | ----------- | -------- | ------- |
| ADMINS     | MLS-CORE    | SSH      | Allowed |
| ADMINS     | R-INTERNET  | SSH      | Allowed |
| ALUMNOS    | MLS-CORE    | SSH      | Blocked |
| PROFESORES | MLS-CORE    | SSH      | Blocked |
| INVITADOS  | MLS-CORE    | SSH      | Blocked |

---

## 🧠 Why SSH?

SSH is used instead of Telnet because SSH provides encrypted remote access.

Telnet sends information in plain text, including usernames and passwords, while SSH encrypts the session.

---

## 🌐 Management IPs

| Device       | Management IP |
| ------------ | ------------- |
| `MLS-CORE`   | 192.168.2.105 |
| `R-INTERNET` | 10.0.0.1      |

The admin PC can reach both devices because routing between the admin VLAN and the router/core network is configured correctly.

---

## ⚙️ SSH Base Configuration

The following configuration is applied on the network devices.

```bash
ip domain-name school.local

username admin privilege 15 secret <SECURE_PASSWORD>
enable secret <ENABLE_SECRET>

crypto key generate rsa
ip ssh version 2
```

Recommended RSA key size for this Packet Tracer lab:

```text
1024
```

---

## 🔒 SSH Access Control List

A standard ACL is used to allow SSH only from the admin VLAN.

```bash
ip access-list standard SSH_ADMINS_ONLY
 permit 192.168.2.112 0.0.0.7
 deny any
```

---

## 🖧 VTY Line Configuration

The ACL is applied to the VTY lines.

### On `MLS-CORE`

```bash
line vty 0 15
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

### On `R-INTERNET`

```bash
line vty 0 4
 login local
 transport input ssh
 access-class SSH_ADMINS_ONLY in
```

---

## 🧪 SSH Tests

### Test 1 — Admin PC to MLS-CORE

From an admin PC:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection successful
```

---

### Test 2 — Admin PC to R-INTERNET

From an admin PC:

```bash
ssh -l admin 10.0.0.1
```

Expected result:

```text
Connection successful
```

---

### Test 3 — Student PC to MLS-CORE

From a student PC:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection refused / timed out
```

---

### Test 4 — Guest PC to MLS-CORE

From a guest PC:

```bash
ssh -l admin 192.168.2.105
```

Expected result:

```text
Connection refused / timed out
```

---

## ✅ Verification Commands

On `MLS-CORE` and `R-INTERNET`:

```bash
show ip ssh
show access-lists SSH_ADMINS_ONLY
show running-config | section line vty
```

---

## 📌 Notes

SSH access is restricted using `access-class` on the VTY lines.

This prevents non-admin VLANs from remotely managing network infrastructure.

For security reasons, real passwords should not be published in GitHub. Use placeholders such as:

```text
<SECURE_PASSWORD>
<ENABLE_SECRET>
```

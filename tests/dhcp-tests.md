# 📡 DHCP Tests

This document contains the DHCP validation tests performed in the **Enterprise School Network** project.

The purpose of these tests is to verify that clients receive automatic IP configuration from the centralized DHCP server.

---

## 🎯 Objective

The objective is to validate that:

* DHCP is working correctly.
* Clients receive an IP address from the correct VLAN pool.
* Clients receive the correct subnet mask.
* Clients receive the correct default gateway.
* DHCP relay is working through `MLS-CORE`.

---

## 🖥️ DHCP Server

The DHCP service is configured on:

```text
Device: SERVER-WEB
IP Address: 192.168.2.98
VLAN: 50 SERVERS
Gateway: 192.168.2.97
```

The DHCP server is located in the server VLAN, so DHCP relay is required for other VLANs.

---

## 🔁 DHCP Relay

DHCP relay is configured on the VLAN interfaces of `MLS-CORE`.

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

This allows DHCP requests from different VLANs to reach the DHCP server.

---

## 📋 DHCP Pools

| VLAN | Pool Name  | Start IP      | Subnet Mask     | Default Gateway |
| ---- | ---------- | ------------- | --------------- | --------------- |
| 10   | ALUMNOS    | 192.168.0.20  | 255.255.254.0   | 192.168.0.1     |
| 20   | PROFESORES | 192.168.2.10  | 255.255.255.192 | 192.168.2.1     |
| 40   | INVITADOS  | 192.168.2.70  | 255.255.255.224 | 192.168.2.65    |
| 30   | ADMINS     | 192.168.2.114 | 255.255.255.248 | 192.168.2.113   |

---

## 🧪 Test Procedure

On each client PC:

```text
Desktop → IP Configuration → DHCP
```

Then open Command Prompt and run:

```bash
ipconfig
```

---

## ✅ Expected DHCP Results

### VLAN 10 — ALUMNOS

Expected result:

```text
IP Address: 192.168.0.20 or higher
Subnet Mask: 255.255.254.0
Default Gateway: 192.168.0.1
DNS Server: 192.168.2.98
```

Status:

```text
DHCP working ✅
```

---

### VLAN 20 — PROFESORES

Expected result:

```text
IP Address: 192.168.2.10 or higher
Subnet Mask: 255.255.255.192
Default Gateway: 192.168.2.1
DNS Server: 192.168.2.98
```

Status:

```text
DHCP working ✅
```

---

### VLAN 40 — INVITADOS

Expected result:

```text
IP Address: 192.168.2.70 or higher
Subnet Mask: 255.255.255.224
Default Gateway: 192.168.2.65
DNS Server: 192.168.2.98
```

Status:

```text
DHCP working ✅
```

---

### VLAN 30 — ADMINS

Expected result:

```text
IP Address: 192.168.2.114 or higher
Subnet Mask: 255.255.255.248
Default Gateway: 192.168.2.113
DNS Server: 192.168.2.98
```

Status:

```text
DHCP working ✅
```

---

## 📊 DHCP Test Summary

| Device        | VLAN    | Expected Result                  | Status |
| ------------- | ------- | -------------------------------- | ------ |
| `PC-ALUMNO`   | VLAN 10 | Receives IP from ALUMNOS pool    | ✅      |
| `PC-PROFE`    | VLAN 20 | Receives IP from PROFESORES pool | ✅      |
| `PC-INVITADO` | VLAN 40 | Receives IP from INVITADOS pool  | ✅      |
| `PC-ADMIN`    | VLAN 30 | Receives IP from ADMINS pool     | ✅      |

---

## 🧪 Gateway Tests After DHCP

After receiving IP configuration, each client should be able to ping its default gateway.

| Source Device | Gateway       | Expected Result | Status |
| ------------- | ------------- | --------------- | ------ |
| `PC-ALUMNO`   | 192.168.0.1   | Successful      | ✅      |
| `PC-PROFE`    | 192.168.2.1   | Successful      | ✅      |
| `PC-INVITADO` | 192.168.2.65  | Successful      | ✅      |
| `PC-ADMIN`    | 192.168.2.113 | Successful      | ✅      |

---

## 🧪 Test Commands

From each PC:

```bash
ipconfig
ping <default_gateway>
```

Examples:

```bash
ping 192.168.0.1
ping 192.168.2.1
ping 192.168.2.65
ping 192.168.2.113
```

---

## ✅ Verification on MLS-CORE

On `MLS-CORE`, run:

```bash
show running-config | include helper
show ip interface brief
```

Expected result:

```text
ip helper-address 192.168.2.98
ip helper-address 192.168.2.98
ip helper-address 192.168.2.98
ip helper-address 192.168.2.98
```

---

## 📌 Notes

The server VLAN and management VLAN use static addressing.

DHCP is only used for end-user VLANs:

* ALUMNOS
* PROFESORES
* INVITADOS
* ADMINS

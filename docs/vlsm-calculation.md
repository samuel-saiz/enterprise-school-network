# 🧮 VLSM Calculation

This document explains the VLSM calculation process used in the **Enterprise School Network** project.

The goal was to divide the main network into smaller subnets according to the number of hosts required by each VLAN.

---

## 📌 Main Network

```text
Main Network: 192.168.0.0/22
Subnet Mask: 255.255.252.0
Total Addresses: 1024
Usable Hosts: 1022
Range: 192.168.0.0 - 192.168.3.255
```

The main network was selected to provide enough address space for all departments and future growth.

---

## 🧠 VLSM Method

VLSM was applied by ordering the VLANs from the largest host requirement to the smallest.

The formula used to calculate the number of available hosts is:

```text
2^n - 2
```

Where:

```text
n = number of host bits
```

The `-2` represents:

```text
1 network address
1 broadcast address
```

---

## 📊 Host Requirements

| VLAN | Name       | Estimated Hosts |
| ---- | ---------- | --------------: |
| 10   | ALUMNOS    |             270 |
| 20   | PROFESORES |              35 |
| 40   | INVITADOS  |              20 |
| 50   | SERVERS    |               5 |
| 99   | MGMT       |               6 |
| 30   | ADMINS     |               4 |

---

## 1️⃣ VLAN 10 — ALUMNOS

The students VLAN requires around **270 hosts**.

Calculation:

```text
2^8 - 2 = 254 hosts ❌
2^9 - 2 = 510 hosts ✅
```

Therefore, 9 host bits are required.

```text
32 - 9 = /23
```

Assigned subnet:

```text
Network: 192.168.0.0/23
Mask: 255.255.254.0
Gateway: 192.168.0.1
Usable Range: 192.168.0.1 - 192.168.1.254
Broadcast: 192.168.1.255
Usable Hosts: 510
```

---

## 2️⃣ VLAN 20 — PROFESORES

The teachers VLAN requires around **35 hosts**.

Calculation:

```text
2^5 - 2 = 30 hosts ❌
2^6 - 2 = 62 hosts ✅
```

Therefore, 6 host bits are required.

```text
32 - 6 = /26
```

Assigned subnet:

```text
Network: 192.168.2.0/26
Mask: 255.255.255.192
Gateway: 192.168.2.1
Usable Range: 192.168.2.1 - 192.168.2.62
Broadcast: 192.168.2.63
Usable Hosts: 62
```

---

## 3️⃣ VLAN 40 — INVITADOS

The guests VLAN requires around **20 hosts**.

Calculation:

```text
2^4 - 2 = 14 hosts ❌
2^5 - 2 = 30 hosts ✅
```

Therefore, 5 host bits are required.

```text
32 - 5 = /27
```

Assigned subnet:

```text
Network: 192.168.2.64/27
Mask: 255.255.255.224
Gateway: 192.168.2.65
Usable Range: 192.168.2.65 - 192.168.2.94
Broadcast: 192.168.2.95
Usable Hosts: 30
```

---

## 4️⃣ VLAN 99 — MGMT

The management VLAN requires around **6 hosts**.

Calculation:

```text
2^2 - 2 = 2 hosts ❌
2^3 - 2 = 6 hosts ✅
```

Therefore, 3 host bits are required.

```text
32 - 3 = /29
```

Assigned subnet:

```text
Network: 192.168.2.104/29
Mask: 255.255.255.248
Gateway: 192.168.2.105
Usable Range: 192.168.2.105 - 192.168.2.110
Broadcast: 192.168.2.111
Usable Hosts: 6
```

---

## 5️⃣ VLAN 50 — SERVERS

The servers VLAN requires around **5 hosts**.

Calculation:

```text
2^2 - 2 = 2 hosts ❌
2^3 - 2 = 6 hosts ✅
```

Therefore, 3 host bits are required.

```text
32 - 3 = /29
```

Assigned subnet:

```text
Network: 192.168.2.96/29
Mask: 255.255.255.248
Gateway: 192.168.2.97
Usable Range: 192.168.2.97 - 192.168.2.102
Broadcast: 192.168.2.103
Usable Hosts: 6
```

---

## 6️⃣ VLAN 30 — ADMINS

The administration VLAN requires around **4 hosts**.

Calculation:

```text
2^2 - 2 = 2 hosts ❌
2^3 - 2 = 6 hosts ✅
```

Therefore, 3 host bits are required.

```text
32 - 3 = /29
```

Assigned subnet:

```text
Network: 192.168.2.112/29
Mask: 255.255.255.248
Gateway: 192.168.2.113
Usable Range: 192.168.2.113 - 192.168.2.118
Broadcast: 192.168.2.119
Usable Hosts: 6
```

---

## 📊 Final VLSM Summary

| VLAN | Name       | Network       | CIDR | Usable Hosts |
| ---- | ---------- | ------------- | ---- | -----------: |
| 10   | ALUMNOS    | 192.168.0.0   | /23  |          510 |
| 20   | PROFESORES | 192.168.2.0   | /26  |           62 |
| 40   | INVITADOS  | 192.168.2.64  | /27  |           30 |
| 50   | SERVERS    | 192.168.2.96  | /29  |            6 |
| 99   | MGMT       | 192.168.2.104 | /29  |            6 |
| 30   | ADMINS     | 192.168.2.112 | /29  |            6 |

---

## ✅ Conclusion

The VLSM design provides efficient IP allocation while leaving unused address space available for future expansion.

All subnets are contained inside the main network:

```text
192.168.0.0/22
```

No subnet overlaps with another subnet.

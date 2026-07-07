# 🔁 NAT/PAT Tests

This document contains the NAT/PAT validation tests performed in the **Enterprise School Network** project.

The purpose of these tests is to verify that each internal VLAN can reach the simulated Internet server using a different public NAT IP address.

---

## 🎯 Objective

The objective is to validate that:

* Internal VLANs can reach the external `INTERNET-SERVER`.
* NAT/PAT is working on `R-INTERNET`.
* Each VLAN is translated to its own public IP address.
* NAT translations can be verified using Cisco IOS commands.
* Internal ACL policies still work while Internet access is available.

---

## 🌐 External Network

The external network is simulated using:

```text
INTERNET-SERVER
      |
    R-ISP
      |
 R-INTERNET
```

---

## 🖥️ Internet Server

The simulated external server uses the following configuration:

```text
Device: INTERNET-SERVER
IP Address: 8.8.8.8
Subnet Mask: 255.255.255.0
Default Gateway: 8.8.8.1
```

---

## 🌍 ISP Link

The link between `R-INTERNET` and `R-ISP` uses:

```text
Network: 203.0.113.0/30
```

| Device       | Interface | IP Address  |
| ------------ | --------- | ----------- |
| `R-ISP`      | G0/0      | 203.0.113.1 |
| `R-INTERNET` | G0/1      | 203.0.113.2 |

---

## 🔁 NAT/PAT Design

NAT/PAT is configured on `R-INTERNET`.

Each VLAN uses a different public IP address when accessing the external network.

| VLAN | Name       | Private Network  | Public NAT IP |
| ---- | ---------- | ---------------- | ------------- |
| 10   | ALUMNOS    | 192.168.0.0/23   | 203.0.113.10  |
| 20   | PROFESORES | 192.168.2.0/26   | 203.0.113.11  |
| 40   | INVITADOS  | 192.168.2.64/27  | 203.0.113.12  |
| 30   | ADMINS     | 192.168.2.112/29 | 203.0.113.13  |
| 50   | SERVERS    | 192.168.2.96/29  | 203.0.113.14  |

---

## ⚙️ NAT Inside and Outside Interfaces

On `R-INTERNET`:

```bash
interface gigabitEthernet 0/0
 description Link to MLS-CORE
 ip nat inside
```

```bash
interface gigabitEthernet 0/1
 description Link to R-ISP
 ip nat outside
```

---

## 🧪 Test Procedure

Before testing, NAT translations can be cleared on `R-INTERNET`:

```bash
clear ip nat translation *
```

Then generate traffic from each VLAN by pinging the external server:

```bash
ping 8.8.8.8
```

After each ping, verify translations on `R-INTERNET`:

```bash
show ip nat translations
```

---

## ✅ Test 1 — ALUMNOS VLAN to INTERNET-SERVER

From a student PC:

```bash
ping 8.8.8.8
```

Expected result:

```text
Reply from 8.8.8.8
```

Expected NAT translation:

| Inside Local  | Inside Global |
| ------------- | ------------- |
| Student PC IP | 203.0.113.10  |

Example:

```text
192.168.0.20 → 203.0.113.10
```

Status:

```text
Successful ✅
```

---

## ✅ Test 2 — PROFESORES VLAN to INTERNET-SERVER

From a teacher PC:

```bash
ping 8.8.8.8
```

Expected result:

```text
Reply from 8.8.8.8
```

Expected NAT translation:

| Inside Local  | Inside Global |
| ------------- | ------------- |
| Teacher PC IP | 203.0.113.11  |

Example:

```text
192.168.2.10 → 203.0.113.11
```

Status:

```text
Successful ✅
```

---

## ✅ Test 3 — INVITADOS VLAN to INTERNET-SERVER

From a guest PC:

```bash
ping 8.8.8.8
```

Expected result:

```text
Reply from 8.8.8.8
```

Expected NAT translation:

| Inside Local | Inside Global |
| ------------ | ------------- |
| Guest PC IP  | 203.0.113.12  |

Example:

```text
192.168.2.70 → 203.0.113.12
```

Status:

```text
Successful ✅
```

---

## ✅ Test 4 — ADMINS VLAN to INTERNET-SERVER

From an admin PC:

```bash
ping 8.8.8.8
```

Expected result:

```text
Reply from 8.8.8.8
```

Expected NAT translation:

| Inside Local | Inside Global |
| ------------ | ------------- |
| Admin PC IP  | 203.0.113.13  |

Example:

```text
192.168.2.114 → 203.0.113.13
```

Status:

```text
Successful ✅
```

---

## ✅ Test 5 — SERVERS VLAN to INTERNET-SERVER

From `SERVER-WEB`:

```bash
ping 8.8.8.8
```

Expected result:

```text
Reply from 8.8.8.8
```

Expected NAT translation:

| Inside Local  | Inside Global |
| ------------- | ------------- |
| SERVER-WEB IP | 203.0.113.14  |

Example:

```text
192.168.2.98 → 203.0.113.14
```

Status:

```text
Successful ✅
```

---

## 📊 NAT Test Summary

| Source VLAN | Test Destination | Expected Public NAT IP | Status |
| ----------- | ---------------- | ---------------------- | ------ |
| ALUMNOS     | 8.8.8.8          | 203.0.113.10           | ✅      |
| PROFESORES  | 8.8.8.8          | 203.0.113.11           | ✅      |
| INVITADOS   | 8.8.8.8          | 203.0.113.12           | ✅      |
| ADMINS      | 8.8.8.8          | 203.0.113.13           | ✅      |
| SERVERS     | 8.8.8.8          | 203.0.113.14           | ✅      |

---

## 🔎 Verification Commands

On `R-INTERNET`:

```bash
show ip nat translations
show ip nat statistics
show access-lists
show ip route
```

Expected output from `show ip nat translations`:

```text
Inside local addresses should be translated to their assigned public NAT IPs.
```

Example:

```text
192.168.0.20   → 203.0.113.10
192.168.2.10   → 203.0.113.11
192.168.2.70   → 203.0.113.12
192.168.2.114  → 203.0.113.13
192.168.2.98   → 203.0.113.14
```

---

## 📌 Notes

NAT/PAT is performed only on `R-INTERNET`.

The internal VLAN structure remains unchanged.

This improvement allows the project to simulate real Internet access while keeping each department identifiable through a different public NAT address.

Internal ACLs still apply:

* Students cannot access the internal server VLAN.
* Guests cannot access internal networks.
* All allowed VLANs can reach the simulated Internet server through NAT/PAT.

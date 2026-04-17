# 🏫 Enterprise School Network

## 📌 Project Overview
This project simulates a **real-world enterprise network** for an educational institution.  
It focuses on **network segmentation, security, and scalability**, using technologies commonly applied in professional environments.

The design follows a **hierarchical network model**:
- Core layer → Multilayer switch (Layer 3)
- Access layer → Layer 2 switches
- Edge → Router (Internet access)

---

## 🎯 Objectives
- Implement **VLAN segmentation** based on departments  
- Apply **VLSM subnetting** for efficient IP allocation  
- Configure **inter-VLAN routing** using a multilayer switch  
- Enforce **network security using ACLs**  
- Provide **basic network services** (DHCP, connectivity)  
- Simulate a **realistic enterprise environment**

---

## 🧩 Network Architecture
            🌐 Internet
                 |
              Router
                 |
        ┌────────────────┐
        │ Multilayer SW  │
        └────────────────┘
          |   |   |   |   |
        L2   L2  L2  L2  L2
         |    |   |   |   |
        PCs  PCs PCs PCs PCs
---

## 🌐 VLAN & Addressing Plan

| VLAN | Name        | Network            | Description              |
|------|------------|--------------------|--------------------------|
| 10   | STUDENTS   | 192.168.0.0/24     | High-density network     |
| 20   | TEACHERS   | 192.168.1.0/27     | Faculty devices          |
| 30   | ADMIN      | 192.168.1.32/28    | Administrative staff     |
| 40   | GUESTS     | 192.168.1.64/26    | Restricted access users  |
| 50   | SERVERS    | 192.168.1.128/28   | Internal services        |
| 99   | MGMT       | 192.168.1.144/28   | Network management       |

---

## 🔐 Security Policies (ACLs)

- ❌ Students cannot access internal servers  
- ❌ Guests cannot access internal networks  
- ✅ Teachers can access servers  
- ✅ Admin has full access  

These policies are enforced using **Access Control Lists (ACLs)** applied at the VLAN interfaces.

---

## 📁 Repository Structure

### 📂 `configs/`
Contains all device configurations:
- Multilayer switch configuration  
- Access switch configurations  
- Router configuration  

👉 Purpose: Provide full reproducibility of the network setup.

---

### 📂 `services/`
Documentation of implemented services:
- DHCP configuration  
- (Optional) DNS / Web server setup  

👉 Purpose: Explain how network services are deployed and integrated.

---

### 📂 `security/`
Security-related configurations:
- ACL definitions  
- Traffic filtering policies  

👉 Purpose: Demonstrate network protection and segmentation strategies.

---

### 📂 `tests/`
Validation and verification of the network:
- Connectivity tests (ping, VLAN communication)  
- Access restriction tests  
- Screenshots or outputs  

👉 Purpose: Prove that the network behaves as expected.

---

### 📂 `docs/` *(optional but recommended)*
Additional documentation:
- Addressing plan  
- Design decisions  
- Future improvements  

👉 Purpose: Show reasoning behind the architecture.

---

## 🛠️ Technologies Used

- Cisco Packet Tracer / GNS3  
- VLANs (802.1Q)  
- Inter-VLAN Routing  
- ACL (Access Control Lists)  
- DHCP  
- IPv4 Subnetting (VLSM)

---

## 🧪 Testing & Validation

The following tests were performed:

- ✅ Inter-VLAN communication (allowed networks)  
- ❌ Blocked traffic (students → servers)  
- ❌ Guest isolation  
- ✅ DHCP address assignment  
- ✅ Default gateway reachability  

---

## 🚀 Future Improvements

- NAT configuration for internet access  
- Implementation of a firewall  
- Network monitoring (SNMP, logs)  
- Redundancy (STP, EtherChannel)  
- Integration with Active Directory  

---

## 👨‍💻 Author

**Samuel Saiz Retama**  
ASIR Student | Networking & Systems  

---

## 📜 License
This project is intended for **educational purposes** and portfolio demonstration.
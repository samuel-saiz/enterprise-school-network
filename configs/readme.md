# ⚙️ Device Configurations

This folder contains the running configurations of the network devices used in the **Enterprise School Network** project.

The purpose of this folder is to provide technical evidence of the configuration applied in Cisco Packet Tracer.

---

## 📁 Expected Files

```text
configs/
├── MLS-CORE.txt
├── R-INTERNET.txt
├── SW-ALUMNOS.txt
├── SW-PROFESORES.txt
├── SW-INVITADOS.txt
├── SW-SERVERS.txt
└── SW-ADMINS.txt
```

---

## 🧠 Device Roles

| Device          | Role                                       |
| --------------- | ------------------------------------------ |
| `MLS-CORE`      | Multilayer switch used as the network core |
| `R-INTERNET`    | Router used for external network access    |
| `SW-ALUMNOS`    | Access switch for the students VLAN        |
| `SW-PROFESORES` | Access switch for the teachers VLAN        |
| `SW-INVITADOS`  | Access switch for the guests VLAN          |
| `SW-SERVERS`    | Access switch for the servers VLAN         |
| `SW-ADMINS`     | Access switch for the administration VLAN  |

---

## 🔧 How to Export the Configuration

On each Cisco device, run the following command:

```bash
show running-config
```

Then copy the output and save it in the corresponding file.

Example:

```text
MLS-CORE      → configs/MLS-CORE.txt
R-INTERNET    → configs/R-INTERNET.txt
SW-ALUMNOS    → configs/SW-ALUMNOS.txt
```

---

## 📌 Main Configuration Elements

The configuration files should include evidence of:

* Hostname configuration
* VLAN creation
* Trunk links
* Access ports
* SVI gateways
* Inter-VLAN routing
* DHCP relay
* ACL configuration
* SSH access control
* Static routing
* Saved configuration

---

## 🧩 MLS-CORE Configuration Includes

The `MLS-CORE` configuration should include:

* VLANs
* SVI interfaces
* `ip routing`
* Trunk ports to access switches
* ACLs applied to VLAN interfaces
* DHCP relay using `ip helper-address`
* SSH management configuration
* Static default route to `R-INTERNET`

Useful verification commands:

```bash
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show access-lists
show running-config
```

---

## 🌐 R-INTERNET Configuration Includes

The `R-INTERNET` configuration should include:

* Interface toward `MLS-CORE`
* Static route back to the internal network
* SSH configuration
* VTY access restriction

Useful verification commands:

```bash
show ip interface brief
show ip route
show ip ssh
show running-config
```

---

## 🔌 Access Switch Configuration Includes

Each access switch should include:

* VLAN assigned to the department
* VLAN 99 for management
* Trunk port toward `MLS-CORE`
* Access ports assigned to the correct VLAN
* Management IP address
* Default gateway

Useful verification commands:

```bash
show vlan brief
show interfaces trunk
show ip interface brief
show running-config
```

---

## 🔐 Security Warning

Do not publish real passwords in GitHub.

Replace sensitive values with placeholders:

```text
<SECURE_PASSWORD>
<ENABLE_SECRET>
<SSH_PASSWORD>
```

Example:

```bash
username admin privilege 15 secret <SECURE_PASSWORD>
enable secret <ENABLE_SECRET>
```

---

## ✅ Configuration Checklist

* [ ] `MLS-CORE.txt` added
* [ ] `R-INTERNET.txt` added
* [ ] `SW-ALUMNOS.txt` added
* [ ] `SW-PROFESORES.txt` added
* [ ] `SW-INVITADOS.txt` added
* [ ] `SW-SERVERS.txt` added
* [ ] `SW-ADMINS.txt` added
* [ ] Passwords replaced with placeholders
* [ ] Configurations verified
* [ ] Files uploaded to GitHub

---

## 📌 Notes

These configuration files help demonstrate that the network was fully implemented, not only designed.

They also allow other users to reproduce the project in Cisco Packet Tracer.

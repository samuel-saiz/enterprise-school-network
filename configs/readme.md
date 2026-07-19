# ⚙️ Device Configurations

This directory contains the documented Cisco IOS configurations used in each version of the **Enterprise School Network** project.

The configuration files are organized by project version so that the original architecture and the High Availability implementation can be reviewed independently.

---

## Directory Structure

```text
configs/
│
├── V1/
│   ├── MLS-CORE.txt
│   ├── R-INTERNET.txt
│   ├── R-ISP.txt
│   ├── SW-ALUMNOS.txt
│   ├── SW-PROFESORES.txt
│   ├── SW-INVITADOS.txt
│   ├── SW-SERVERS.txt
│   ├── SW-ADMINS.txt
│   └── INTERNET-SERVER.txt
│
├── V2/
│   ├── MLS-CORE-1.txt
│   ├── MLS-CORE-2.txt
│   ├── R-INTERNET.txt
│   ├── R-ISP.txt
│   ├── SW-ALUMNOS.txt
│   ├── SW-PROFESORES.txt
│   ├── SW-INVITADOS.txt
│   ├── SW-SERVERS.txt
│   ├── SW-ADMINS.txt
│   └── INTERNET-SERVER.txt
│
└── README.md
```

---

# Version 1 Configurations

Version 1 uses a single multilayer core switch to provide the default gateways and inter-VLAN routing for the entire campus network.

| Device            | Role                   | Main functions                                              |
| ----------------- | ---------------------- | ----------------------------------------------------------- |
| `MLS-CORE`        | Multilayer core switch | VLAN gateways, inter-VLAN routing, DHCP relay, ACLs and SSH |
| `R-INTERNET`      | Internet edge router   | Static routing, NAT/PAT and external connectivity           |
| `R-ISP`           | Simulated ISP router   | Connection to the external Internet network                 |
| `SW-ALUMNOS`      | Access switch          | Connectivity for VLAN 10                                    |
| `SW-PROFESORES`   | Access switch          | Connectivity for VLAN 20                                    |
| `SW-ADMINS`       | Access switch          | Connectivity for VLAN 30                                    |
| `SW-INVITADOS`    | Access switch          | Connectivity for VLAN 40                                    |
| `SW-SERVERS`      | Access switch          | Connectivity for VLAN 50                                    |
| `INTERNET-SERVER` | External server        | Internet and NAT/PAT validation                             |

Version 1 configuration files are available in:

```text
configs/V1/
```

---

# Version 2 Configurations

Version 2 introduces a redundant core architecture while retaining the original VLAN design and centralized services.

The main configuration changes affect the two multilayer core switches and the Internet edge router.

| Device            | Status in V2 | Main functions                                                                 |
| ----------------- | ------------ | ------------------------------------------------------------------------------ |
| `MLS-CORE-1`      | Updated      | HSRP, Rapid PVST, inter-VLAN routing and preferred gateway for selected VLANs  |
| `MLS-CORE-2`      | New          | Secondary core, HSRP redundancy, Rapid PVST and alternate routed Internet path |
| `R-INTERNET`      | Updated      | Dual core connectivity and floating static routes                              |
| `R-ISP`           | Retained     | Simulated ISP and public NAT block routing                                     |
| Access switches   | Retained     | VLAN access connectivity and redundant core uplinks                            |
| `INTERNET-SERVER` | Retained     | External connectivity and NAT/PAT validation                                   |

Version 2 configuration files are available in:

```text
configs/V2/
```

---

## Version 2 Core Role Distribution

HSRP and Rapid PVST responsibilities are distributed between both core switches.

| VLAN | Name       | Preferred HSRP active core | Preferred STP root |
| ---: | ---------- | -------------------------- | ------------------ |
|   10 | ALUMNOS    | `MLS-CORE-1`               | `MLS-CORE-1`       |
|   20 | PROFESORES | `MLS-CORE-2`               | `MLS-CORE-2`       |
|   30 | ADMINS     | `MLS-CORE-1`               | `MLS-CORE-1`       |
|   40 | INVITADOS  | `MLS-CORE-2`               | `MLS-CORE-2`       |
|   50 | SERVERS    | `MLS-CORE-1`               | `MLS-CORE-1`       |
|   99 | MANAGEMENT | `MLS-CORE-2`               | `MLS-CORE-2`       |

This distribution prevents a single core switch from processing all active gateway and Layer 2 forwarding roles.

---

## Version 2 Routed Links

`R-INTERNET` connects independently to both multilayer core switches.

| Connection              | Network          |    R-INTERNET |   Core switch |
| ----------------------- | ---------------- | ------------: | ------------: |
| R-INTERNET ↔ MLS-CORE-1 | `10.0.0.0/30`    |    `10.0.0.1` |    `10.0.0.2` |
| R-INTERNET ↔ MLS-CORE-2 | `10.0.0.4/30`    |    `10.0.0.5` |    `10.0.0.6` |
| R-INTERNET ↔ R-ISP      | `203.0.113.0/30` | `203.0.113.2` | `203.0.113.1` |

Floating static routes provide an alternative path when one of the core connections becomes unavailable.

---

## Configuration File Format

The files in this directory are structured implementation documents rather than unmodified `show running-config` outputs.

Each file normally includes:

* Device role and model.
* Basic system configuration.
* Interface configuration.
* VLAN and trunk configuration.
* Routing configuration.
* HSRP or Rapid PVST configuration where applicable.
* NAT/PAT and ACL configuration.
* SSH management controls.
* Verification commands.
* Relevant technical notes.

Automatically generated IOS lines and hardware-specific information are omitted when they do not contribute to understanding the implementation.

---

## Security

Real credentials and encrypted password hashes must not be published in the repository.

Configuration examples use placeholders such as:

```text
<SECURE_PASSWORD>
<ENABLE_SECRET>
```

The following information should also be removed before publishing raw configurations:

* Password hashes.
* Real usernames when sensitive.
* Device serial numbers.
* License identifiers.
* Unnecessary hardware-generated MAC addresses.

---

## Reused Configurations

The access-layer switches and `INTERNET-SERVER` were not functionally redesigned in Version 2.

Their configuration files can be copied into `configs/V2/` so that the Version 2 directory remains a complete and independent configuration snapshot.

References to the original single `MLS-CORE` should be updated where necessary to describe the redundant links toward:

```text
MLS-CORE-1
MLS-CORE-2
```

The IOS commands should only be changed when the actual Packet Tracer device configuration was modified.

---

## Verification

The documented configurations can be checked using commands such as:

```bash
show running-config
show ip interface brief
show vlan brief
show interfaces trunk
show ip route
show standby brief
show spanning-tree root
show access-lists
show ip nat translations
show ip nat statistics
```

Not every command is supported by every device.

---

## Notes

The files in this directory document the implemented Packet Tracer topology and are intended for:

* Technical review.
* Configuration reference.
* Portfolio presentation.
* Troubleshooting.
* Comparison between Version 1 and Version 2.

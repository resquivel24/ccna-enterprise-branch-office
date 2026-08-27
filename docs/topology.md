# CCNA Enterprise Branch Office Lab — Network Topology

This document describes the physical and logical topology of the **CCNA Enterprise Branch Office Lab** built in GNS3.

The lab models a small enterprise branch network using a hierarchical design with redundant distribution switches, multiple access switches, departmental VLANs, centralized server networks, management connectivity, Internet access, and IPv4/IPv6 dual-stack operation.

---

## 1. Topology Overview

The network is organized into the following functional areas:

- Internet / WAN edge
- Enterprise edge router
- Redundant distribution layer
- Access layer
- Departmental user VLANs
- Management VLAN
- Server networks
- IPv4 and IPv6 routing
- First-hop gateway redundancy
- Network management services

The primary infrastructure consists of:

| Device | Role |
|---|---|
| R1 | Enterprise edge router |
| DSW1 | Layer 3 distribution switch |
| DSW2 | Layer 3 distribution switch |
| SW1 | Engineering access switch |
| SW2 | Human Resources access switch |
| SW3 | Sales access switch |
| SW4 | Management access switch |
| SW5 | Server access switch |
| PC1-PC2 | Engineering endpoints |
| PC3-PC4 | Human Resources endpoints |
| PC5-PC6 | Sales endpoints |
| PC7 | Management / NMS workstation |
| DHCP_DNS_SRV | DHCP and DNS server |
| Web_FT_SRV | Web and FTP server |

---

## 2. Logical Topology

At a high level, the topology follows this structure:

```text
                           Internet
                              |
                              |
                             R1
                         /    |    \
                        /     |     \
                     DSW1-----+-----DSW2
                      |\             /|
                      | \           / |
                      |  \         /  |
                      |   \       /   |
                     SW1  SW2   SW3  SW4
                      |    |     |     |
                   VLAN10 VLAN20 VLAN30 VLAN99
                    PC1-2  PC3-4 PC5-6  PC7

                             |
                            SW5
                           /   \
                          /     \
                 DHCP/DNS       Web/FTP
                 Server          Server
                10.0.2.0/24    10.0.3.0/24
```

DSW1 and DSW2 provide redundant Layer 3 services for the principal enterprise VLANs.

The access switches are connected redundantly to the distribution layer so that loss of a distribution path does not necessarily isolate the access network.

---

## 3. Enterprise Edge — R1

R1 provides connectivity between the enterprise network and external networks.

Its major functions in the lab include:

- Internet connectivity
- IPv4 routing
- IPv6 routing
- NAT/PAT
- Static NAT
- OSPF for supported IPv4 routed infrastructure
- IPv6 static routing
- DHCP relay where required
- Router-on-a-stick connectivity toward server networks
- Network management services
- SSH remote administration
- NTP
- SNMP
- Syslog-related management functionality

### R1 Primary Connections

| R1 Interface | Connection | IPv4 / IPv6 Purpose |
|---|---|---|
| Gi1 | DSW1 | Routed distribution link |
| Gi2 | DSW2 | Routed distribution link |
| Gi3 | SW5 | Server VLAN trunk |
| Gi4 | Internet | External / NAT outside |
| Loopback0 | Logical interface | Management / router identification |

R1 uses the loopback IPv4 address:

```text
1.1.1.1
```

for management and infrastructure purposes within the lab.

---

## 4. Distribution Layer

The distribution layer consists of:

```text
DSW1
DSW2
```

Both devices operate as multilayer switches and provide Layer 3 gateway services for the enterprise VLANs.

Their responsibilities include:

- Inter-VLAN routing
- IPv4 HSRP
- IPv6 HSRPv2
- IPv4 routing toward R1
- IPv6 routing toward R1
- VLAN SVI termination
- Redundant access-layer connectivity
- Spanning Tree participation
- EtherChannel connectivity between distribution switches
- Network management

---

## 5. R1-to-Distribution Routed Links

### R1 ↔ DSW1

IPv4 network:

```text
10.0.1.128/30
```

Addresses:

```text
R1:   10.0.1.129
DSW1: 10.0.1.130
```

IPv6 network:

```text
2001:DB8:1001::/64
```

Addresses:

```text
R1:   2001:DB8:1001::1/64
DSW1: 2001:DB8:1001::2/64
```

### R1 ↔ DSW2

IPv4 network:

```text
10.0.1.132/30
```

Addresses:

```text
R1:   10.0.1.133
DSW2: 10.0.1.134
```

IPv6 network:

```text
2001:DB8:1001:1::/64
```

Addresses:

```text
R1:   2001:DB8:1001:1::1/64
DSW2: 2001:DB8:1001:1::2/64
```

---

## 6. Distribution Interconnection

DSW1 and DSW2 are interconnected to provide redundancy and Layer 2/Layer 3 communication between the distribution switches.

The topology includes an EtherChannel between the two distribution switches.

The IPv4 routed transit network associated with the distribution interconnection is:

```text
10.0.1.136/30
```

Addresses:

```text
DSW1: 10.0.1.137
DSW2: 10.0.1.138
```

EtherChannel is used to combine physical links into a logical Port-Channel, providing additional resiliency and demonstrating link aggregation concepts used in enterprise switching.

---

## 7. Access Layer

The access layer contains four primary access switches:

| Switch | Primary Function |
|---|---|
| SW1 | Engineering access |
| SW2 | Human Resources access |
| SW3 | Sales access |
| SW4 | Management access |

The access switches connect endpoint devices to the enterprise network and provide Layer 2 connectivity toward DSW1 and DSW2.

Redundant uplinks are used between the access and distribution layers.

This allows the lab to demonstrate technologies including:

- VLANs
- 802.1Q trunking
- Spanning Tree Protocol
- Root bridge selection
- Redundant Layer 2 paths
- EtherChannel where implemented
- Access-port configuration
- Management VLAN operation

---

## 8. VLAN 10 — Engineering

VLAN 10 provides connectivity for the Engineering department.

IPv4 network:

```text
10.0.1.0/27
```

IPv6 network:

```text
2001:DB8:10::/64
```

Connected hosts:

```text
PC1
PC2
```

Access switch:

```text
SW1
```

IPv4 gateway redundancy:

```text
DSW1: 10.0.1.1
DSW2: 10.0.1.2
HSRP: 10.0.1.3
```

IPv6 gateway addressing:

```text
DSW1: 2001:DB8:10::1/64
DSW2: 2001:DB8:10::2/64
HSRP: 2001:DB8:10::3/64
```

IPv6 SLAAC was tested with PC1 on this VLAN.

---

## 9. VLAN 20 — Human Resources

VLAN 20 provides connectivity for the Human Resources department.

IPv4 network:

```text
10.0.1.32/27
```

IPv6 network:

```text
2001:DB8:20::/64
```

Connected hosts:

```text
PC3
PC4
```

Access switch:

```text
SW2
```

IPv4 gateway redundancy:

```text
DSW1: 10.0.1.33
DSW2: 10.0.1.34
HSRP: 10.0.1.35
```

IPv6 gateway addressing:

```text
DSW1: 2001:DB8:20::1/64
DSW2: 2001:DB8:20::2/64
HSRP: 2001:DB8:20::3/64
```

---

## 10. VLAN 30 — Sales

VLAN 30 provides connectivity for the Sales department.

IPv4 network:

```text
10.0.1.64/27
```

IPv6 network:

```text
2001:DB8:30::/64
```

Connected hosts:

```text
PC5
PC6
```

Access switch:

```text
SW3
```

IPv4 gateway redundancy:

```text
DSW1: 10.0.1.65
DSW2: 10.0.1.66
HSRP: 10.0.1.67
```

IPv6 gateway addressing:

```text
DSW1: 2001:DB8:30::1/64
DSW2: 2001:DB8:30::2/64
HSRP: 2001:DB8:30::3/64
```

---

## 11. VLAN 99 — Management

VLAN 99 is used for network management.

IPv4 network:

```text
10.0.1.96/27
```

IPv6 network:

```text
2001:DB8:99::/64
```

IPv4 gateway redundancy:

```text
DSW1: 10.0.1.97
DSW2: 10.0.1.98
HSRP: 10.0.1.99
```

IPv6 gateway addressing:

```text
DSW1: 2001:DB8:99::1/64
DSW2: 2001:DB8:99::2/64
HSRP: 2001:DB8:99::3/64
```

PC7 is connected through SW4 and is used as a management/NMS workstation.

Management services tested in the lab include technologies such as:

- SSH
- SNMP
- SNMP traps
- Syslog-related monitoring
- NTP verification
- General network troubleshooting and verification

---

## 12. Infrastructure Management Addresses

The infrastructure management plan currently uses the following IPv4 addresses:

| Device | Management IPv4 Address |
|---|---|
| R1 | 1.1.1.1 |
| DSW1 | 10.0.1.97 |
| DSW2 | 10.0.1.98 |
| SW1 | 10.0.1.103 |
| SW2 | 10.0.1.104 |
| SW3 | 10.0.1.101 |
| SW4 | 10.0.1.100 |
| SW5 | 10.0.1.142 |
| PC7 / NMS | 10.0.1.105 |

---

## 13. Server Access Layer — SW5

SW5 connects the enterprise edge router to the server networks.

The R1-to-SW5 connection carries multiple VLANs using 802.1Q trunking.

The server-side networks are:

```text
10.0.2.0/24
10.0.3.0/24
```

R1 provides Layer 3 gateway functionality for these server networks using subinterfaces.

---

## 14. Server Room 1 — DHCP and DNS

Server Room 1 uses:

```text
10.0.2.0/24
```

The server provides:

- DHCP
- DNS

Server address:

```text
10.0.2.2
```

The server is connected to SW5.

DHCP relay functionality is used where clients and the DHCP server reside on different IPv4 subnets.

DNS functionality is used by lab clients for hostname resolution.

---

## 15. Server Room 2 — Web and FTP

Server Room 2 uses:

```text
10.0.3.0/24
```

The server provides:

- Web services
- FTP services

Server address:

```text
10.0.3.2
```

The server is connected to SW5.

This network allows application-layer connectivity, ACL, NAT, and service-access concepts to be tested without placing the services directly inside the user VLANs.

---

## 16. IPv4 HSRP Design

IPv4 HSRP provides redundant default gateways for the primary enterprise VLANs.

The design intentionally distributes the Active role between DSW1 and DSW2.

| VLAN | HSRP Group | Preferred Active | Preferred Standby | Virtual IPv4 |
|---|---:|---|---|---|
| 10 | 1 | DSW1 | DSW2 | 10.0.1.3 |
| 20 | 2 | DSW2 | DSW1 | 10.0.1.35 |
| 30 | 3 | DSW1 | DSW2 | 10.0.1.67 |
| 99 | 4 | DSW2 | DSW1 | 10.0.1.99 |

The preferred Active switch uses an increased HSRP priority and preemption.

This distributes gateway responsibility across both distribution switches instead of making a single switch Active for every VLAN.

---

## 17. IPv6 HSRPv2 Design

IPv6 HSRPv2 was also implemented for the primary VLANs.

| VLAN | IPv6 HSRP Group | Preferred Active | Preferred Standby | Virtual IPv6 |
|---|---:|---|---|---|
| 10 | 2 | DSW2 | DSW1 | 2001:DB8:10::3/64 |
| 20 | 5 | DSW1 | DSW2 | 2001:DB8:20::3/64 |
| 30 | 2 | DSW2 | DSW1 | 2001:DB8:30::3/64 |
| 99 | 2 | DSW1 | DSW2 | 2001:DB8:99::3/64 |

The IPv6 Active distribution switch is intentionally reversed relative to the IPv4 Active distribution switch.

This provides additional practice with first-hop redundancy and traffic-distribution design.

---

## 18. IPv6 Routing

IPv6 is implemented as part of a dual-stack design.

R1 reaches the enterprise VLAN prefixes using static IPv6 routes.

The route distribution is:

```text
VLAN 10 → DSW1
VLAN 20 → DSW2
VLAN 30 → DSW1
VLAN 99 → DSW2
```

R1 routes:

```cisco
ipv6 route 2001:DB8:10::/64 2001:DB8:1001::2
ipv6 route 2001:DB8:20::/64 2001:DB8:1001:1::2
ipv6 route 2001:DB8:30::/64 2001:DB8:1001::2
ipv6 route 2001:DB8:99::/64 2001:DB8:1001:1::2
```

Distribution-switch default routes:

```text
DSW1 → 2001:DB8:1001::1
DSW2 → 2001:DB8:1001:1::1
```

IPv6 routing was successfully verified between R1 and the enterprise VLAN SVI addresses.

---

## 19. IPv6 Neighbor Discovery and SLAAC

IPv6 hosts use Neighbor Discovery Protocol functionality for local IPv6 operation.

The lab has been used to examine:

- Router Solicitation (RS)
- Router Advertisement (RA)
- Neighbor Solicitation (NS)
- Neighbor Advertisement (NA)
- SLAAC
- Link-local addresses
- IPv6 neighbor tables
- Default-router learning

PC1 in VLAN 10 was specifically used to verify SLAAC and Neighbor Discovery behavior.

---

## 20. Internet Connectivity and NAT

R1 connects to the external network through Gi4.

The external connection provides Internet access to the lab.

R1 performs IPv4 NAT/PAT so that internal private IPv4 networks can reach external networks.

The lab has also been used to configure and verify static NAT for internal services.

The external connection is intentionally kept logically separate from the enterprise addressing used internally.

---

## 21. SSH Management

Remote device administration is performed using SSHv2.

The topology documentation intentionally does **not** contain authentication credentials.

The SSH design includes:

- SSH version 2
- Local authentication
- RSA keys
- Management through the designated management infrastructure
- Restricted remote administration using VTY configuration

Credentials, passwords, secrets, and private keys are excluded from the GitHub portfolio.

---

## 22. Network Management Services

The lab includes practical implementation and verification of several network management technologies.

These include:

- NTP
- DNS
- DHCP
- SNMP
- SNMP traps
- Syslog
- SSH
- File-transfer services
- ICMP-based testing
- IPv4 and IPv6 route verification

PC7 has been used as a management/NMS workstation for several of these exercises.

---

## 23. Security Features

Security functionality is being added progressively as the lab develops.

Implemented or planned CCNA-level security features include:

- SSHv2
- Local authentication
- ACLs
- NAT boundary controls
- Port security
- Layer 2 security controls
- Secure device management
- AAA concepts and potential centralized AAA integration

Only verified implementations will be documented as completed features in the portfolio.

---

## 24. Redundancy Strategy

The lab intentionally contains redundant paths at several layers.

### Distribution Redundancy

```text
DSW1 ↔ DSW2
```

### Access Redundancy

Access switches have redundant connectivity toward the distribution layer.

### Default Gateway Redundancy

```text
IPv4 → HSRP
IPv6 → HSRPv2
```

### Link Redundancy

EtherChannel is used where implemented to combine multiple physical links into a logical connection.

This architecture allows failure scenarios to be introduced intentionally for troubleshooting and verification exercises.

---

## 25. CCNA Technologies Represented

The topology is designed to provide hands-on practice with technologies covered by the Cisco CCNA 200-301 curriculum.

Current lab coverage includes:

- Ethernet switching
- MAC address learning
- VLANs
- Access ports
- 802.1Q trunks
- Native VLANs
- Spanning Tree Protocol
- EtherChannel
- Layer 3 switching
- Inter-VLAN routing
- Static routing
- OSPF
- IPv4 subnetting
- IPv6 addressing
- IPv6 Neighbor Discovery
- SLAAC
- IPv6 static routing
- HSRP
- IPv6 HSRPv2
- DHCP
- DHCP relay
- DNS
- NAT/PAT
- Static NAT
- ACLs
- NTP
- SNMP
- Syslog
- SSH
- FTP
- Network troubleshooting and verification

The topology continues to evolve as additional CCNA topics are reviewed and implemented.

---

## 26. Portfolio Design Principle

The GitHub repository contains documentation and evidence of the lab rather than the complete GNS3 runtime environment.

Large virtual appliance files and platform images are intentionally excluded.

The repository is intended to contain:

```text
configs/        Sanitized network-device configurations
diagrams/       Network topology diagrams
docs/           Technical design documentation
verification/   Verification and troubleshooting evidence
```

This approach keeps the repository lightweight while preserving the information necessary to understand, reproduce, review, and discuss the network design.

---

## 27. Documentation Security

Before configuration files or topology images are published, sensitive information must be removed.

The portfolio must not contain:

- passwords;
- enable secrets;
- authentication secrets;
- private SSH keys;
- real production credentials;
- SNMP community strings that should remain private;
- API tokens;
- personal access tokens.

Lab documentation may describe the authentication technology being used without exposing the corresponding credentials.

---

## 28. Current Topology Status

The topology currently provides a functioning foundation for continued CCNA study and portfolio development.

Major completed areas include:

| Area | Status |
|---|---|
| IPv4 addressing | Implemented and verified |
| VLAN segmentation | Implemented |
| Inter-VLAN routing | Implemented |
| Distribution redundancy | Implemented |
| IPv4 HSRP | Implemented and verified |
| IPv6 addressing | Implemented and verified |
| SLAAC / Neighbor Discovery | Tested and verified |
| IPv6 HSRPv2 control plane | Implemented and tested |
| IPv6 static routing | Implemented and verified |
| DHCP / DHCP relay | Implemented and tested |
| DNS | Implemented and tested |
| NAT/PAT | Implemented and tested |
| Static NAT | Implemented and tested |
| ACLs | Implemented and tested |
| NTP | Implemented and tested |
| SNMP / traps | Implemented and tested |
| SSH management | Implemented |
| Server networks | Implemented |
| Security hardening | In progress |

---

## 29. Related Documentation

Detailed addressing information is maintained separately in:

```text
docs/addressing-plan.md
```

Future repository documentation will include configuration references, verification evidence, security implementation notes, and troubleshooting records as the lab continues to develop.

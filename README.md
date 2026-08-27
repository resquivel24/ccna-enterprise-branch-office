# CCNA Enterprise Branch Office Lab

Enterprise-style network lab built in **GNS3** to practice, integrate, verify, and document technologies from the **Cisco CCNA 200-301** curriculum.

The project models a branch-office environment with redundant distribution switching, multiple departmental VLANs, centralized network services, IPv4/IPv6 dual-stack operation, remote management, monitoring, and security controls.

The lab is developed incrementally. Each major implementation or troubleshooting milestone is documented and version-controlled using Git and GitHub.

---

## Network Topology

![CCNA Enterprise Branch Office Topology](diagrams/enterprise-branch-office-topology.png)

The topology uses a hierarchical enterprise design consisting of:

- Enterprise edge router
- Redundant Layer 3 distribution switches
- Multiple access switches
- Engineering, Human Resources, Sales, and Management VLANs
- Centralized DHCP/DNS services
- Web and FTP services
- Dedicated management workstation
- Internet connectivity
- IPv4 and IPv6 infrastructure

---

## Project Objectives

The purpose of this project is to move beyond isolated command exercises and build a functioning network in which multiple CCNA technologies operate together.

The lab is used to practice:

- Network design
- Cisco IOS configuration
- Configuration verification
- Troubleshooting methodology
- Routing and switching
- High availability
- Network services
- Device management
- Network monitoring
- IPv6
- Security
- Technical documentation

---

## Current Architecture

### Enterprise Edge

**R1** provides:

- IPv4 and IPv6 routing
- Internet connectivity
- NAT/PAT
- Static NAT
- OSPFv2 connectivity
- IPv6 static routing
- Router-on-a-stick connectivity toward server VLANs
- Network management services

### Distribution Layer

**DSW1** and **DSW2** provide:

- Layer 3 switching
- Inter-VLAN routing
- IPv4 HSRP
- IPv6 HSRPv2
- VLAN SVI gateways
- Redundant distribution connectivity
- Routing toward R1
- EtherChannel
- Spanning Tree participation

### Access Layer

The access layer consists of:

- SW1 — Engineering
- SW2 — Human Resources
- SW3 — Sales
- SW4 — Management
- SW5 — Server access

---

## VLAN Design

| VLAN | Purpose | IPv4 Network | IPv6 Network |
|---:|---|---|---|
| 10 | Engineering | `10.0.1.0/27` | `2001:DB8:10::/64` |
| 20 | Human Resources | `10.0.1.32/27` | `2001:DB8:20::/64` |
| 30 | Sales | `10.0.1.64/27` | `2001:DB8:30::/64` |
| 99 | Management | `10.0.1.96/27` | `2001:DB8:99::/64` |
| 200 | Server Network | `10.0.2.0/24` | — |
| 300 | Web / FTP Server Network | `10.0.3.0/24` | — |

Detailed addressing information is available in:

[`docs/addressing-plan.md`](docs/addressing-plan.md)

---

## Technologies Implemented

### Layer 2

- VLANs
- Access ports
- 802.1Q trunking
- Native VLAN
- Spanning Tree Protocol
- EtherChannel
- Layer 2 redundancy

### Layer 3

- Inter-VLAN routing
- Routed switch interfaces
- IPv4 static routing
- OSPFv2
- IPv4 HSRP
- IPv6 addressing
- IPv6 static routing
- IPv6 HSRPv2
- Dual-stack IPv4/IPv6 operation

### IPv6

- Global unicast addressing
- Link-local addressing
- Neighbor Discovery Protocol
- Router Solicitation
- Router Advertisement
- SLAAC
- IPv6 routing tables
- IPv6 static routes
- IPv6 first-hop redundancy testing

### Network Services

- DHCP
- DHCP relay
- DNS
- NTP
- NAT/PAT
- Static NAT
- FTP
- Web services

### Management and Monitoring

- SSHv2
- Local authentication
- SNMP
- SNMP traps
- Syslog
- NTP synchronization
- Centralized management workstation
- Configuration backup through FTP

### Security

- Standard and extended ACLs
- Restricted SSH access
- Local administrative authentication
- NAT boundary controls

Additional CCNA security features are being added as the security portion of the curriculum is reviewed.

---

## High Availability

IPv4 HSRP provides redundant default gateways across DSW1 and DSW2.

The Active role is distributed between the two switches:

| VLAN | Preferred IPv4 Active |
|---:|---|
| 10 | DSW1 |
| 20 | DSW2 |
| 30 | DSW1 |
| 99 | DSW2 |

IPv6 HSRPv2 was configured using the opposite preferred distribution switch to provide additional first-hop redundancy practice.

Detailed topology information is available in:

[`docs/topology.md`](docs/topology.md)

---

## IPv6 Lab Notes

The network was extended from IPv4-only operation to dual-stack IPv4/IPv6.

IPv6 connectivity was successfully verified across:

- Routed R1-to-distribution links
- VLAN 10
- VLAN 20
- VLAN 30
- VLAN 99
- Inter-VLAN routing
- R1-to-VLAN connectivity

SLAAC and Neighbor Discovery were verified using a Linux endpoint.

IPv6 HSRPv2 control-plane functionality was also verified, including:

- Active/Standby election
- Priority
- Preemption
- Virtual IPv6 addressing
- Virtual MAC addressing
- Failover

A forwarding limitation involving the HSRPv2 virtual IPv6 gateway was observed in the IOSvL2/GNS3 environment. The behavior and troubleshooting process are documented rather than hidden, because identifying and isolating platform limitations is part of the purpose of the lab.

---

## Troubleshooting Approach

The lab is intentionally used to reproduce and diagnose faults.

Troubleshooting activities have included:

- Missing static routes
- Incorrect interface addressing
- OSPF route propagation
- VLAN and trunk verification
- HSRP election behavior
- DHCP and DNS connectivity
- NAT and ACL troubleshooting
- SNMP community mismatches
- Syslog connectivity
- SSH RSA-key problems
- Legacy SSH algorithm negotiation
- IPv6 SLAAC and Router Advertisement verification
- IPv6 HSRPv2 forwarding investigation

The troubleshooting process follows a layered approach rather than changing configurations randomly.

Verification evidence will be added progressively under:

```text
verification/
```

---

## Repository Structure

```text
ccna-enterprise-branch-office/
│
├── README.md
│
├── configs/
│   └── Sanitized Cisco device configurations
│
├── diagrams/
│   └── enterprise-branch-office-topology.png
│
├── docs/
│   ├── addressing-plan.md
│   └── topology.md
│
└── verification/
    └── Configuration and troubleshooting evidence
```

Large GNS3 appliance files, virtual disks, IOS images, and runtime data are intentionally excluded from the repository.

---

## Documentation

### Addressing Plan

Detailed IPv4 and IPv6 addressing:

[`docs/addressing-plan.md`](docs/addressing-plan.md)

### Network Topology

Detailed architecture and device-role documentation:

[`docs/topology.md`](docs/topology.md)

---

## Project Development

The lab is version-controlled using Git.

Major additions are committed individually so that the repository history reflects how the environment evolves.

Examples of project milestones include:

```text
Initial repository structure
        ↓
IPv4 / IPv6 addressing documentation
        ↓
Topology documentation
        ↓
Topology diagram
        ↓
Device configurations
        ↓
Verification evidence
        ↓
Layer 2 security
        ↓
Additional CCNA features
```

This allows future changes to be documented and pushed to GitHub without duplicating the full GNS3 runtime environment.

---

## Security and Repository Hygiene

The public portfolio version of the project will not include:

- Passwords
- Enable secrets
- Private SSH keys
- Authentication tokens
- GitHub personal access tokens
- Sensitive SNMP community strings
- Production credentials
- Cisco IOS image files

Configuration files will be reviewed before the repository is made public.

---

## Current Project Status

| Area | Status |
|---|---|
| Enterprise topology | Implemented |
| IPv4 addressing | Verified |
| IPv6 addressing | Verified |
| VLANs and trunking | Implemented |
| Inter-VLAN routing | Verified |
| OSPFv2 | Implemented |
| IPv4 HSRP | Verified |
| IPv6 HSRPv2 control plane | Verified |
| IPv6 static routing | Verified |
| DHCP / DNS | Verified |
| NAT / PAT | Verified |
| ACLs | Implemented |
| NTP | Verified |
| SNMP / traps | Verified |
| Syslog | Verified |
| SSH | Verified |
| FTP configuration backup | Verified |
| Security hardening | In progress |
| Portfolio documentation | In progress |

---

## Next Development Areas

Planned additions include further CCNA security implementation and verification, such as:

- Port Security
- DHCP Snooping
- Dynamic ARP Inspection
- Additional device-hardening controls
- Centralized AAA lab extension where appropriate

Only features that are actually implemented and verified will be marked as complete.

---

## About This Project

This project was created as a practical companion to CCNA study.

Its purpose is not only to demonstrate Cisco IOS commands, but to show how switching, routing, redundancy, IPv6, services, security, monitoring, and troubleshooting operate together inside a functioning enterprise-style network.

# Network Verification Evidence

This directory contains operational verification evidence for the \***\*CCNA Enterprise Branch Office Lab\*\***.

The purpose of this section is to demonstrate that the technologies documented in the device configurations were not only configured, but also tested and validated in the GNS3 environment.

Verification outputs are captured directly from the lab devices and sanitized where necessary before publication.

---

## 1. Layer 2 Switching

Verification includes:

- VLAN operation
- 802.1Q trunking
- Native VLAN configuration
- Rapid PVST+ operation
- Root bridge placement
- EtherChannel using LACP
- Access-port operation

Relevant verification commands include:

    show vlan brief
    show interfaces trunk
    show spanning-tree
    show etherchannel summary

Current evidence:

    spanning-tree-verification.txt
    etherchannel-verification.txt

---

## 2. First-Hop Redundancy

HSRP version 2 provides redundant default gateways for the enterprise user and management VLANs across both IPv4 and IPv6.

The final HSRP design uses a predictable dual-stack numbering convention:

- IPv4 HSRP group = VLAN ID
- IPv6 HSRP group = VLAN ID + 100

Gateway placement is aligned with spanning-tree root placement:

- VLAN 10: DSW1 Active / STP Root
- VLAN 20: DSW2 Active / STP Root
- VLAN 30: DSW1 Active / STP Root
- VLAN 99: DSW2 Active / STP Root

Verification includes:

- HSRPv2 operation
- Active and standby gateway roles
- IPv4 virtual gateways
- IPv6 virtual gateways
- HSRP priorities
- Preemption
- IPv6 virtual link-local gateways
- Dual-stack gateway consistency
- HSRP and STP alignment
- IPv6 forwarding through the HSRP virtual gateway

Relevant verification commands include:

    show standby brief
    show standby

Current evidence:

    hsrp-verification.txt

---

## 3. IPv4 Routing

OSPF is used for dynamic IPv4 routing between the enterprise routing and distribution layers.

Verification includes:

- OSPF neighbor relationships
- OSPF-learned routes
- Connected networks
- Default routing
- End-to-end IPv4 reachability

Relevant verification commands include:

    show ip ospf neighbor
    show ip route
    show ip route ospf
    ping
    traceroute

Current evidence:

    ospf-verification.txt

---

## 4. IPv6 Connectivity

IPv6 connectivity is implemented using global unicast addressing, HSRPv2 for IPv6 gateway redundancy, Neighbor Discovery, SLAAC, Router Advertisements, and static IPv6 routing between R1 and the distribution switches.

Verification includes:

- IPv6 interface addressing
- IPv6 Neighbor Discovery
- IPv6 routing tables
- Static IPv6 routing
- SLAAC
- Router Advertisements
- HSRPv2 IPv6 virtual gateways
- Client learning of HSRP virtual link-local default gateways
- IPv6 inter-VLAN routing
- End-to-end IPv6 connectivity

Relevant verification commands include:

    show ipv6 interface brief
    show ipv6 neighbors
    show ipv6 route
    show standby brief
    ping ipv6

Linux client verification includes:

    ip -6 addr
    ip -6 route
    ping -6

Current evidence:

    ipv6-verification.txt

---

## 5. DHCP

Centralized DHCP services provide addressing to enterprise client VLANs.

The distribution switches use DHCP relay to forward client requests toward the DHCP server.

Verification will demonstrate:

- DHCP relay operation
- Client address assignment
- Default gateway assignment
- DNS server assignment
- DHCP-provided network parameters

Relevant verification commands and client evidence include:

    show running-config interface vlan <VLAN-ID>
    ipconfig /all

Planned evidence:

    dhcp-dns-verification.md

---

## 6. DNS

Internal DNS provides name resolution for lab resources.

Verification will demonstrate:

- DNS server reachability
- Forward name resolution
- Client DNS configuration
- Internal hostname resolution

Example verification commands include:

    nslookup <hostname>
    ping <hostname>

Planned evidence:

    dhcp-dns-verification.md

---

## 7. NAT/PAT and Internet Connectivity

R1 provides IPv4 NAT/PAT between internal enterprise networks and the external network.

Verification will demonstrate:

- NAT configuration
- Dynamic PAT translations
- NAT statistics
- Internet reachability from internal clients

Relevant verification commands include:

    show ip nat translations
    show ip nat statistics
    ping <external-destination>

Planned evidence:

    nat-verification.txt

---

## 8. Network Management

The lab includes multiple management and monitoring technologies.

Verification will include:

- SSH remote administration
- SNMPv2c monitoring
- SNMP traps
- NTP synchronization
- Management VLAN connectivity
- Management access control
- Syslog operation

Relevant verification commands include:

    show ssh
    show privilege
    show aaa method-lists
    show snmp
    show ntp associations
    show ntp status
    show access-lists
    show logging

Centralized AAA was validated first on SW4 and then deployed to R1, DSW1, DSW2, and SW1-SW5. Verification confirmed TACACS+ administrative login, privilege 15 access, command authorization for a restricted account, local fallback behavior, local-only console recovery, and EXEC accounting.

Sensitive authentication information, TACACS+ shared secrets, account passwords, and SNMP community strings are excluded from all public verification evidence.

Current evidence:

    aaa-verification.txt

Planned evidence:

    network-management-verification.md

---

## 9. File Transfer and Server Services

The lab includes server-side services used for CCNA operational practice.

Verification may include:

- FTP connectivity
- Configuration file transfer
- DNS services
- DHCP services
- Web services
- Connectivity between enterprise clients and server networks

Authentication credentials are excluded from all public verification evidence.

---

## 10. End-to-End Connectivity

Final connectivity testing will demonstrate communication across the enterprise topology.

Testing will include appropriate combinations of:

- Engineering VLAN 10
- Human Resources VLAN 20
- Sales VLAN 30
- Management VLAN 99
- Server VLAN 200
- Server VLAN 300
- R1
- DSW1
- DSW2
- External connectivity

Both IPv4 and IPv6 connectivity will be demonstrated where those protocols are implemented.

Verification may use:

    ping
    traceroute
    show ip route
    show ipv6 route

Planned evidence:

    end-to-end-connectivity.md

---

## Verification Evidence

The verification directory currently contains:

    verification/
    ├── README.md
    ├── hsrp-verification.txt
    ├── ospf-verification.txt
    ├── etherchannel-verification.txt
    ├── spanning-tree-verification.txt
    ├── ipv6-verification.txt
    └── aaa-verification.txt

Additional evidence planned as the lab is expanded and validated:

    dhcp-dns-verification.md
    nat-verification.txt
    network-management-verification.md
    end-to-end-connectivity.md

The exact contents may evolve as additional CCNA technologies are implemented and tested.

---

## Verification Methodology

The evidence in this directory follows a simple process:

1. Configure the technology in the live GNS3 environment.
2. Verify its operational state using appropriate Cisco IOS commands.
3. Test connectivity or behavior where applicable.
4. Troubleshoot any unexpected results.
5. Correct the design or configuration when necessary.
6. Repeat testing until the intended behavior is confirmed.
7. Capture the final working operational output.
8. Remove credentials or other sensitive information before publication.
9. Commit the verified evidence to the Git repository.

This approach ensures that the portfolio represents technologies actually implemented, tested, and troubleshot in the lab rather than configuration examples created only for documentation.

---

## Portfolio Purpose

The verification evidence is intended to demonstrate practical understanding of:

- Enterprise switching
- VLAN segmentation
- 802.1Q trunking
- Spanning Tree Protocol
- EtherChannel and LACP
- Layer 3 switching
- First-hop redundancy
- OSPF
- Static routing
- IPv4
- IPv6
- SLAAC
- Neighbor Discovery
- Router Advertisements
- DHCP and DHCP relay
- DNS
- NAT/PAT
- SSH
- Centralized AAA
- TACACS+ authentication
- TACACS+ authorization
- TACACS+ EXEC accounting
- Local AAA fallback and console recovery
- SNMP
- Syslog
- NTP
- Network services
- Network monitoring
- Troubleshooting
- Operational validation

This is an ongoing CCNA learning project. New technologies and security controls are added to the portfolio only after they have been implemented and validated in the working lab.

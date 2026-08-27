# Addressing Plan

This document records the IPv4 and IPv6 addressing used in the **CCNA Enterprise Branch Office Lab**.

The lab uses a dual-stack design where IPv4 and IPv6 operate in parallel across the routed core and user VLANs.

---

## 1. IPv4 VLAN Addressing

| VLAN | Department / Purpose | IPv4 Subnet | DSW1 SVI | DSW2 SVI | HSRP Virtual IP |
|---|---|---|---|---|---|
| 10 | Engineering | 10.0.1.0/27 | 10.0.1.1 | 10.0.1.2 | 10.0.1.3 |
| 20 | Human Resources | 10.0.1.32/27 | 10.0.1.33 | 10.0.1.34 | 10.0.1.35 |
| 30 | Sales | 10.0.1.64/27 | 10.0.1.65 | 10.0.1.66 | 10.0.1.67 |
| 99 | Management | 10.0.1.96/27 | 10.0.1.97 | 10.0.1.98 | 10.0.1.99 |
| 200 | Server Network | 10.0.2.0/24 | — | — | R1 Gi3.200 = 10.0.2.1 |
| 300 | Web / FTP Server Network | 10.0.3.0/24 | — | — | R1 Gi3.300 = 10.0.3.1 |

---

## 2. IPv4 Routed Transit Networks

| Link | Network | Device A | Address | Device B | Address |
|---|---|---|---|---|---|
| R1 ↔ DSW1 | 10.0.1.128/30 | R1 Gi1 | 10.0.1.129 | DSW1 Gi1/0 | 10.0.1.130 |
| R1 ↔ DSW2 | 10.0.1.132/30 | R1 Gi2 | 10.0.1.133 | DSW2 Gi1/0 | 10.0.1.134 |
| DSW1 ↔ DSW2 | 10.0.1.136/30 | DSW1 | 10.0.1.137 | DSW2 | 10.0.1.138 |
| R1 ↔ SW5 Management | 10.0.1.140/30 | R1 Gi3.99 | 10.0.1.141 | SW5 Vlan99 | 10.0.1.142 |

---

## 3. IPv4 Management Addresses

| Device | Management Address |
|---|---|
| R1 | 1.1.1.1 |
| DSW1 | 10.0.1.97 |
| DSW2 | 10.0.1.98 |
| SW1 | 10.0.1.103 |
| SW2 | 10.0.1.104 |
| SW3 | 10.0.1.101 |
| SW4 | 10.0.1.100 |
| SW5 | 10.0.1.142 |
| PC7 Management / NMS | 10.0.1.105 |

---

## 4. IPv4 Server Addresses

| Server | Function | Address |
|---|---|---|
| DHCP / DNS Server | DHCP and DNS services | 10.0.2.2 |
| Web / FTP Server | Web and FTP services | 10.0.3.2 |

---

## 5. IPv6 Routed Transit Networks

| Link | IPv6 Prefix | Device A | IPv6 Address | Device B | IPv6 Address |
|---|---|---|---|---|---|
| R1 ↔ DSW1 | 2001:DB8:1001::/64 | R1 Gi1 | 2001:DB8:1001::1/64 | DSW1 Gi1/0 | 2001:DB8:1001::2/64 |
| R1 ↔ DSW2 | 2001:DB8:1001:1::/64 | R1 Gi2 | 2001:DB8:1001:1::1/64 | DSW2 Gi1/0 | 2001:DB8:1001:1::2/64 |

---

## 6. IPv6 VLAN Addressing

| VLAN | IPv6 Prefix | DSW1 SVI | DSW2 SVI | HSRPv2 Global VIP |
|---|---|---|---|---|
| 10 | 2001:DB8:10::/64 | 2001:DB8:10::1/64 | 2001:DB8:10::2/64 | 2001:DB8:10::3/64 |
| 20 | 2001:DB8:20::/64 | 2001:DB8:20::1/64 | 2001:DB8:20::2/64 | 2001:DB8:20::3/64 |
| 30 | 2001:DB8:30::/64 | 2001:DB8:30::1/64 | 2001:DB8:30::2/64 | 2001:DB8:30::3/64 |
| 99 | 2001:DB8:99::/64 | 2001:DB8:99::1/64 | 2001:DB8:99::2/64 | 2001:DB8:99::3/64 |

---

## 7. IPv6 HSRPv2 Design

IPv6 HSRP was configured with an intentional design where the preferred IPv6 Active distribution switch is the opposite of the IPv4 Active distribution switch.

| VLAN | IPv4 Active | IPv6 HSRP Group | IPv6 Active | IPv6 Standby |
|---|---|---:|---|---|
| 10 | DSW1 | 2 | DSW2 | DSW1 |
| 20 | DSW2 | 5 | DSW1 | DSW2 |
| 30 | DSW1 | 2 | DSW2 | DSW1 |
| 99 | DSW2 | 2 | DSW1 | DSW2 |

### HSRPv2 Virtual Link-Local Addresses

| VLAN | Group | Virtual Link-Local |
|---|---:|---|
| 10 | 2 | FE80::5:73FF:FEA0:2 |
| 20 | 5 | FE80::5:73FF:FEA0:5 |
| 30 | 2 | FE80::5:73FF:FEA0:2 |
| 99 | 2 | FE80::5:73FF:FEA0:2 |

---

## 8. IPv6 Host Addressing

Hosts use SLAAC where supported.

### PC1 Example

PC1 belongs to VLAN 10 and receives an IPv6 address dynamically from the prefix:

```text
2001:DB8:10::/64

One dynamically generated address observed during lab verification was:

```text
2001:DB8:10:0:98BE:2446:F1C3:122B/64
```

The host also automatically generates a link-local IPv6 address.

Router Advertisement (RA) messages provide the IPv6 prefix information and allow the host to learn its IPv6 default router.

---

## 9. IPv6 Static Routing

Because the IOSvL2 image used by the distribution switches does not provide OSPFv3 support, IPv6 static routing is used between the distribution layer and R1.

### DSW1 Default IPv6 Route

```cisco
ipv6 route ::/0 2001:DB8:1001::1
```

### DSW2 Default IPv6 Route

```cisco
ipv6 route ::/0 2001:DB8:1001:1::1
```

### R1 IPv6 Routes

```cisco
ipv6 route 2001:DB8:10::/64 2001:DB8:1001::2
ipv6 route 2001:DB8:20::/64 2001:DB8:1001:1::2
ipv6 route 2001:DB8:30::/64 2001:DB8:1001::2
ipv6 route 2001:DB8:99::/64 2001:DB8:1001:1::2
```

The static routing design distributes the VLAN routes across both distribution switches:

```text
VLAN 10 and VLAN 30 → DSW1
VLAN 20 and VLAN 99 → DSW2
```

End-to-end IPv6 connectivity between R1 and all four IPv6 VLAN networks was successfully verified.

---

## 10. IPv6 HSRPv2 Lab Observation

IPv6 HSRPv2 control-plane operation was successfully verified in the GNS3 lab.

The following functions were observed:

- Active and Standby election
- HSRP priority selection
- Preemption
- Virtual global IPv6 addressing
- Virtual link-local addressing
- HSRPv2 virtual MAC addressing
- Active/Standby failover between DSW1 and DSW2
- Neighbor Discovery of the HSRP virtual MAC address

During testing, an IPv6 forwarding limitation was observed when an end host attempted to use the HSRPv2 virtual link-local address as its default router.

The host successfully:

- obtained an IPv6 address using SLAAC;
- learned its IPv6 prefix through Router Advertisement;
- learned the HSRP virtual link-local default router;
- resolved the HSRP virtual IPv6 address to the correct virtual MAC address;
- reached the physical IPv6 SVI addresses of DSW1 and DSW2.

However, routed IPv6 traffic through the HSRPv2 virtual link-local gateway did not forward successfully.

Inter-VLAN and upstream IPv6 connectivity was successfully verified by temporarily using the physical link-local address of the active distribution switch as the host's IPv6 next hop.

This behavior is documented as an observed limitation of the virtual lab environment rather than a requirement of the production network design.

---

## 11. Addressing Design Notes

- The lab operates as an IPv4/IPv6 dual-stack network.
- Existing IPv4 addressing remained operational when IPv6 was introduced.
- VLAN IDs are reflected in IPv6 prefixes where practical for easier identification and troubleshooting.
- The IPv6 documentation prefix `2001:DB8::/32` is used throughout the lab.
- `/64` prefixes are used for IPv6 LAN and routed segments.
- `/27` IPv4 subnets are used for the primary user and management VLANs.
- `/30` IPv4 subnets are used for point-to-point routed infrastructure links.
- IPv4 HSRP provides redundant default gateways for the principal VLANs.
- IPv6 HSRPv2 was configured and its control-plane failover behavior was verified.
- IPv6 HSRP groups use separate group numbers where necessary to prevent IPv4/IPv6 address-family conflicts.
- IPv6 static routes provide connectivity between R1 and the distribution-layer VLANs.
- IPv4 dynamic routing and IPv6 static routing coexist as part of the dual-stack lab design.

---

## 12. Verification Status

| Feature | Status |
|---|---|
| IPv4 VLAN addressing | Verified |
| IPv4 HSRP | Verified |
| IPv4 routed transit links | Verified |
| IPv6 VLAN addressing | Verified |
| IPv6 routed transit links | Verified |
| SLAAC | Verified |
| Router Advertisement | Verified |
| Neighbor Discovery | Verified |
| IPv6 HSRPv2 control plane | Verified |
| IPv6 HSRPv2 failover | Verified |
| IPv6 static routing | Verified |
| IPv6 inter-VLAN routing using physical DSW next hop | Verified |
| IPv6 reachability between R1 and VLAN SVIs | Verified |

# Addressing Plan

This document records the IPv4 and IPv6 addressing used in the **CCNA Enterprise Branch Office Lab**.

The lab uses a dual-stack design where IPv4 and IPv6 operate in parallel across the routed core and user VLANs.

---

## 1. IPv4 VLAN Addressing

| VLAN | Department / Purpose     | IPv4 Subnet  | DSW1 SVI  | DSW2 SVI  | HSRP Virtual IP       |
| ---- | ------------------------ | ------------ | --------- | --------- | --------------------- |
| 10   | Engineering              | 10.0.1.0/27  | 10.0.1.1  | 10.0.1.2  | 10.0.1.3              |
| 20   | Human Resources          | 10.0.1.32/27 | 10.0.1.33 | 10.0.1.34 | 10.0.1.35             |
| 30   | Sales                    | 10.0.1.64/27 | 10.0.1.65 | 10.0.1.66 | 10.0.1.67             |
| 99   | Management               | 10.0.1.96/27 | 10.0.1.97 | 10.0.1.98 | 10.0.1.99             |
| 200  | Server Network           | 10.0.2.0/24  | —         | —         | R1 Gi3.200 = 10.0.2.1 |
| 300  | Web / FTP Server Network | 10.0.3.0/24  | —         | —         | R1 Gi3.300 = 10.0.3.1 |

---

## 2. IPv4 Routed Transit Networks

| Link                | Network       | Device A  | Address    | Device B   | Address    |
| ------------------- | ------------- | --------- | ---------- | ---------- | ---------- |
| R1 ↔ DSW1           | 10.0.1.128/30 | R1 Gi1    | 10.0.1.129 | DSW1 Gi1/0 | 10.0.1.130 |
| R1 ↔ DSW2           | 10.0.1.132/30 | R1 Gi2    | 10.0.1.133 | DSW2 Gi1/0 | 10.0.1.134 |
| DSW1 ↔ DSW2         | 10.0.1.136/30 | DSW1      | 10.0.1.137 | DSW2       | 10.0.1.138 |
| R1 ↔ SW5 Management | 10.0.1.140/30 | R1 Gi3.99 | 10.0.1.141 | SW5 Vlan99 | 10.0.1.142 |

---

## 3. IPv4 Management Addresses

| Device               | Management Address |
| -------------------- | ------------------ |
| R1                   | 1.1.1.1            |
| DSW1                 | 10.0.1.97          |
| DSW2                 | 10.0.1.98          |
| SW1                  | 10.0.1.103         |
| SW2                  | 10.0.1.104         |
| SW3                  | 10.0.1.101         |
| SW4                  | 10.0.1.100         |
| SW5                  | 10.0.1.142         |
| PC7 Management / NMS | 10.0.1.105         |
| AUT-SRV-AAA          | 10.0.1.106         |

---

## 4. IPv4 Server Addresses

| Server            | Function                           | Address    |
| ----------------- | ---------------------------------- | ---------- |
| DHCP / DNS Server | DHCP and DNS services              | 10.0.2.2   |
| Web / FTP Server  | Web and FTP services               | 10.0.3.2   |
| AUT-SRV-AAA       | Centralized TACACS+ / AAA services | 10.0.1.106 |

The AAA server is located in Management VLAN 99 and is connected to SW4 Gi0/3.

Its network configuration is:

```text
IPv4 Address: 10.0.1.106/27
Default Gateway: 10.0.1.99
DNS Server: 10.0.2.2
Primary AAA Protocol: TACACS+
TACACS+ Transport: TCP/49
```

---

## 5. IPv6 Routed Transit Networks

| Link      | IPv6 Prefix          | Device A | IPv6 Address          | Device B   | IPv6 Address          |
| --------- | -------------------- | -------- | --------------------- | ---------- | --------------------- |
| R1 ↔ DSW1 | 2001:DB8:1001::/64   | R1 Gi1   | 2001:DB8:1001::1/64   | DSW1 Gi1/0 | 2001:DB8:1001::2/64   |
| R1 ↔ DSW2 | 2001:DB8:1001:1::/64 | R1 Gi2   | 2001:DB8:1001:1::1/64 | DSW2 Gi1/0 | 2001:DB8:1001:1::2/64 |

---

## 6. IPv6 VLAN Addressing

| VLAN | IPv6 Prefix      | DSW1 SVI          | DSW2 SVI          | HSRPv2 Global VIP |
| ---- | ---------------- | ----------------- | ----------------- | ----------------- |
| 10   | 2001:DB8:10::/64 | 2001:DB8:10::1/64 | 2001:DB8:10::2/64 | 2001:DB8:10::3/64 |
| 20   | 2001:DB8:20::/64 | 2001:DB8:20::1/64 | 2001:DB8:20::2/64 | 2001:DB8:20::3/64 |
| 30   | 2001:DB8:30::/64 | 2001:DB8:30::1/64 | 2001:DB8:30::2/64 | 2001:DB8:30::3/64 |
| 99   | 2001:DB8:99::/64 | 2001:DB8:99::1/64 | 2001:DB8:99::2/64 | 2001:DB8:99::3/64 |

---

## 7. IPv6 HSRPv2 Design

The final IPv6 HSRPv2 design aligns the preferred IPv6 Active distribution switch with the preferred IPv4 Active gateway and the Spanning Tree root for each VLAN.

IPv4 and IPv6 use separate HSRP group numbers because the IOSvL2 image does not allow the same HSRP group number to be reused for both address families on the same SVI.

The group-number convention is:

```text
IPv4 HSRP group = VLAN ID
IPv6 HSRP group = VLAN ID + 100
```

| VLAN | IPv4 Group | IPv6 Group | Preferred Active | Standby | STP Root |
| ---- | ---------: | ---------: | ---------------- | ------- | -------- |
| 10   |         10 |        110 | DSW1             | DSW2    | DSW1     |
| 20   |         20 |        120 | DSW2             | DSW1    | DSW2     |
| 30   |         30 |        130 | DSW1             | DSW2    | DSW1     |
| 99   |         99 |        199 | DSW2             | DSW1    | DSW2     |

### HSRPv2 Virtual Link-Local Addresses

| VLAN | Group | Virtual Link-Local   |
| ---- | ----: | -------------------- |
| 10   |   110 | FE80::5:73FF:FEA0:6E |
| 20   |   120 | FE80::5:73FF:FEA0:78 |
| 30   |   130 | FE80::5:73FF:FEA0:82 |
| 99   |   199 | FE80::5:73FF:FEA0:C7 |

---

## 8. IPv6 Host Addressing

Hosts use SLAAC where supported.

### PC1 Example

PC1 belongs to VLAN 10 and receives an IPv6 address dynamically from the prefix:

```text
2001:DB8:10::/64
```

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

## 10. IPv6 HSRPv2 Lab Verification

IPv6 HSRPv2 control-plane and forwarding operation were successfully verified in the GNS3 lab.

The following functions were observed:

- Active and Standby election
- HSRP priority selection
- Preemption where configured
- Virtual global IPv6 addressing
- Virtual link-local addressing
- HSRPv2 virtual MAC addressing
- Active/Standby failover between DSW1 and DSW2
- Neighbor Discovery of the HSRP virtual MAC address
- IPv6 forwarding through the HSRPv2 virtual gateway
- IPv6 inter-VLAN routing through the redundant gateway design

An earlier forwarding issue was resolved after rebuilding the IPv6 HSRP configuration with unique address-family group numbers and aligning the active gateway design.

The final implementation successfully forwards IPv6 traffic through the HSRPv2 virtual default gateway.

---

## 11. Centralized AAA Design

The lab includes a centralized AAA appliance named `AUT-SRV-AAA`.

The server is located in Management VLAN 99 and uses:

```text
IPv4 Address: 10.0.1.106/27
Default Gateway: 10.0.1.99
DNS Server: 10.0.2.2
Connected Switch: SW4
Switch Port: Gi0/3
Primary AAA Protocol: TACACS+
TACACS+ Transport: TCP/49
```

Centralized TACACS+ administration has been deployed and verified on:

| Device | Management Address | TACACS+ Status |
| ------ | ------------------ | -------------- |
| R1     | 1.1.1.1            | Verified       |
| DSW1   | 10.0.1.97          | Verified       |
| DSW2   | 10.0.1.98          | Verified       |
| SW1    | 10.0.1.103         | Verified       |
| SW2    | 10.0.1.104         | Verified       |
| SW3    | 10.0.1.101         | Verified       |
| SW4    | 10.0.1.100         | Verified       |
| SW5    | 10.0.1.142         | Verified       |

The AAA design includes:

- Centralized TACACS+ authentication for remote SSH administration
- Local authentication fallback when the TACACS+ server is unavailable
- A separate local-only console authentication method for recovery
- TACACS+ EXEC authorization
- TACACS+ privilege-level 15 command authorization
- TACACS+ EXEC session accounting
- Existing management ACL protection on VTY access
- SSH-only remote management

A TACACS+ read-only account was tested successfully.

The account was permitted to execute operational `show` commands but was denied access to configuration mode with `Command authorization failed.`

EXEC accounting was also verified on the AAA server with successful shell START and STOP records.

Sensitive TACACS+ shared secrets and user passwords are intentionally excluded from repository documentation and sanitized configuration files.

---

## 12. Addressing Design Notes

- The lab operates as an IPv4/IPv6 dual-stack network.
- Existing IPv4 addressing remained operational when IPv6 was introduced.
- VLAN IDs are reflected in IPv6 prefixes where practical for easier identification and troubleshooting.
- The IPv6 documentation prefix `2001:DB8::/32` is used throughout the lab.
- `/64` prefixes are used for IPv6 LAN and routed segments.
- `/27` IPv4 subnets are used for the primary user and management VLANs.
- `/30` IPv4 subnets are used for point-to-point routed infrastructure links.
- IPv4 HSRP provides redundant default gateways for the principal VLANs.
- IPv6 HSRPv2 provides redundant IPv6 default gateways.
- IPv4 HSRP group numbers match the VLAN ID.
- IPv6 HSRP group numbers use VLAN ID + 100.
- Preferred HSRP Active gateways are aligned with the preferred Spanning Tree root.
- IPv6 static routes provide connectivity between R1 and the distribution-layer VLANs.
- IPv4 dynamic routing and IPv6 static routing coexist as part of the dual-stack lab design.
- VLAN 99 provides the primary management network for infrastructure devices and services.
- PC7 at `10.0.1.105` serves as the management and NMS workstation.
- AUT-SRV-AAA at `10.0.1.106` provides centralized TACACS+ AAA services.
- TACACS+ has been deployed to R1, DSW1, DSW2, and SW1 through SW5.
- Local console authentication remains available as an administrative recovery path.
- Sensitive AAA credentials and shared secrets are excluded from public documentation.

---

## 13. Verification Status

| Feature                                               | Status   |
| ----------------------------------------------------- | -------- |
| IPv4 VLAN addressing                                  | Verified |
| IPv4 HSRP                                             | Verified |
| IPv4 routed transit links                             | Verified |
| IPv6 VLAN addressing                                  | Verified |
| IPv6 routed transit links                             | Verified |
| SLAAC                                                 | Verified |
| Router Advertisement                                  | Verified |
| Neighbor Discovery                                    | Verified |
| IPv6 HSRPv2 control plane                             | Verified |
| IPv6 HSRPv2 failover                                  | Verified |
| IPv6 HSRPv2 forwarding                                | Verified |
| IPv6 static routing                                   | Verified |
| IPv6 inter-VLAN routing through HSRP                  | Verified |
| IPv6 reachability between R1 and VLAN SVIs            | Verified |
| Management VLAN 99 connectivity                       | Verified |
| AAA server reachability                               | Verified |
| TACACS+ centralized authentication                    | Verified |
| TACACS+ EXEC authorization                            | Verified |
| TACACS+ command authorization                         | Verified |
| Local AAA fallback                                    | Verified |
| Local console recovery authentication                 | Verified |
| TACACS+ EXEC accounting                               | Verified |
| TACACS+ deployment across R1, DSW1, DSW2, and SW1-SW5 | Verified |

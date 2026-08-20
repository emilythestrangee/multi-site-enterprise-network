# Multi-Site Enterprise Network Design with OSPF, BGP & IPSec VPN

A fully simulated enterprise network built in **Cisco Packet Tracer**, connecting a Central Headquarters and a Remote Branch office across two Internet Service Providers, secured with a site-to-site IPSec VPN. Designed and implemented as part of the *Data Communications and Networking 2* course at University of Technology Bahrain.

## Overview

The network follows Cisco's three-tier hierarchical model (**Core, Distribution, Access**) and connects two geographically separated sites — HQ and Branch — over a fully encrypted IPSec VPN tunnel spanning two external ISPs. A dedicated DMZ hosts shared organizational services, and a simulated 4G segment provides wireless connectivity for executive devices.

## Network Architecture

**Core Layer** — Forms the backbone of the network. HQ-Router and VPN-HQ handle the HQ site; BR-Router and VPN-Branch handle the Branch site. Two ISP routers (ISP1, ISP2) form the public internet core connecting both sites. This layer runs OSPF for internal routing, BGP for inter-ISP routing, static default routes on the VPN routers, and IPSec VPN for secure cross-site traffic.

**Distribution Layer** — Two Layer 3 multilayer switches per site (HQ-Multilayer-SW1/SW2, BR-Multilayer-SW1/SW2) perform inter-VLAN routing via SVIs, aggregate access-layer uplinks over 802.1Q trunks, and connect to the core routers via routed Layer 3 interfaces. HSRP provides gateway redundancy between the two switches at each site, and a Layer 3 EtherChannel (LACP) gives a redundant, high-bandwidth logical uplink between them.

**Access Layer** — One Cisco 2960 Layer 2 switch per department at each site, connecting end devices (PCs, laptops, printers, APs, IoT devices). Access ports are assigned to department VLANs; uplinks to the multilayer switches are 802.1Q trunks. Each access switch has dual uplinks to both multilayer switches for redundancy, managed by STP.

**DMZ & Mobile Segment** — A dedicated DMZ switch connects all organizational servers on VLAN 70 (90.64.110.0/24), isolated from internal VLANs and reachable only through controlled routing and ACL-enforced policies on VPN-HQ. A simulated 4G segment connects to the HQ core through VPN-HQ.

## Key Features

- Classless subnetting (VLSM/CIDR) across 12 departmental VLANs, a DMZ segment, backbone links, and public ISP-facing links
- Inter-VLAN routing via SVIs on Layer 3 multilayer switches
- HSRP for first-hop gateway redundancy (priority 150/preempt on primary, default 100 on standby)
- Layer 3 EtherChannel (LACP active mode) for link aggregation and redundancy between multilayer switches
- OSPF (single-area, process 10) for dynamic routing within each site, with unique router-IDs and passive interfaces on the ISP-facing links
- BGP between two simulated ISPs (AS 65000 / AS 65001) for inter-autonomous-system routing
- Site-to-site IPSec VPN (ISAKMP + IPSec, pre-shared key authentication) securing all cross-site traffic
- NAT/PAT (overload) on VPN routers, with extended ACLs excluding cross-site VPN traffic from translation
- Centralized DHCP on HQ-Router and BR-Router with `ip helper-address` relay across all VLAN SVIs
- DMZ hosting DNS, HTTP, Email, NTP, Syslog, and IoT servers

## HQ Departments — 192.168.170.0/24 (subnetted into /27)

| VLAN | Department | Network | Host Range | Broadcast | Gateway (SVI) |
|---|---|---|---|---|---|
| 10 | SOC | 192.168.170.0/27 | .1–.30 | .31 | 192.168.170.1 |
| 20 | IT | 192.168.170.32/27 | .33–.62 | .63 | 192.168.170.33 |
| 30 | Engineering | 192.168.170.64/27 | .65–.94 | .95 | 192.168.170.65 |
| 40 | Operations | 192.168.170.96/27 | .97–.126 | .127 | 192.168.170.97 |
| 50 | Customer Service | 192.168.170.128/27 | .129–.158 | .159 | 192.168.170.129 |
| 60 | Accounting | 192.168.170.160/27 | .161–.190 | .191 | 192.168.170.161 |

## Branch Departments — 192.168.171.0/24 (subnetted into /28)

| VLAN | Department | Network | Host Range | Broadcast | Gateway (SVI) |
|---|---|---|---|---|---|
| 80 | NetOps | 192.168.171.0/28 | .1–.14 | .15 | 192.168.171.1 |
| 90 | R&D | 192.168.171.16/28 | .17–.30 | .31 | 192.168.171.18 |
| 100 | HR | 192.168.171.32/28 | .33–.46 | .47 | 192.168.171.33 |
| 110 | Marketing | 192.168.171.48/28 | .49–.62 | .63 | 192.168.171.50 |
| 120 | PMO | 192.168.171.64/28 | .65–.78 | .79 | 192.168.171.65 |
| 130 | Corporate Finance | 192.168.171.80/28 | .81–.94 | .95 | 192.168.171.81 |

## Backbone & DMZ Addressing

| Segment | Block | Mask |
|---|---|---|
| DMZ Servers (VLAN 70) | 90.64.110.0/24 | /24 |
| HQ Backbone Links | 172.16.0.0/16 | /30 |
| Branch Backbone Links | 172.16.1.0/16 | /30 |
| VPN-HQ ↔ ISP1 | 195.137.21.0/30 | /30 |
| VPN-Branch ↔ ISP2 | 195.137.22.0/30 | /30 |
| ISP1 ↔ ISP2 (Serial) | 12.12.12.0/30 | /30 |

## Verification & Testing

- End-to-end ping tests across VLANs and across sites (inter-VLAN, cross-site VPN, NAT/routing, backbone connectivity)
- Routing table inspection (OSPF adjacencies, default route propagation, VLAN subnet reachability)
- IPSec VPN session state verification (ISAKMP Phase 1 and IPSec Phase 2 security associations)
- DHCP binding verification via client address acquisition
- NAT translation table verification for internet-bound traffic

## Challenges & Lessons Learned

- `switchport trunk encapsulation dot1q` must be explicitly configured on multilayer switches **before** enabling trunk mode, since these are Layer 3-capable devices
- NAT ACL ordering matters: deny statements for cross-site traffic must precede the permit statement, otherwise cross-site traffic gets NATted before the VPN can process it
- OSPF default route propagation required `default-information originate` on the VPN routers to correctly advertise the default route into the OSPF domain

## Future Enhancements

- IPv6 dual-stack implementation to future-proof the addressing scheme
- Centralized AAA via RADIUS/TACACS+ extended to all device management access
- Firewall appliances to replace ACL-only security at the DMZ boundary
- Network automation tools to streamline configuration management and reduce human error

## How to Open

1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (free with a Cisco Networking Academy account).
2. Open `enterprise_network_icpc.pkt`.
3. Refer to `report/` for full design documentation, configuration explanations, and verification screenshots.

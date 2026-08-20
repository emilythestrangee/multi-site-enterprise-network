# Multi-Site Enterprise Network Design with OSPF, BGP & IPSec VPN

A fully simulated enterprise network built in **Cisco Packet Tracer**, connecting a Central Headquarters and a Remote Branch office across two Internet Service Providers, secured with a site-to-site IPSec VPN. Designed and implemented as part of the *Data Communications and Networking 2* course at University of Technology Bahrain.

![Network Topology](screenshots/topology-diagram.png)

## Overview

The network follows Cisco's three-tier hierarchical model (**Core, Distribution, Access**) and connects two geographically separated sites — HQ and Branch — over a fully encrypted IPSec VPN tunnel spanning two external ISPs. A dedicated DMZ hosts shared organizational services, and a simulated 4G segment provides wireless connectivity for executive devices.

## Key Features

- **Classless subnetting (VLSM/CIDR)** across 12 departmental VLANs, a DMZ segment, backbone links, and public ISP-facing links
- **Inter-VLAN routing** via SVIs on Layer 3 multilayer switches
- **HSRP** for first-hop gateway redundancy
- **Layer 3 EtherChannel** (LACP) for link aggregation and redundancy between multilayer switches
- **OSPF** (single-area) for dynamic routing within each site
- **BGP** between two simulated ISPs (AS 65000 / AS 65001) for inter-autonomous-system routing
- **Site-to-site IPSec VPN** (ISAKMP + IPSec) securing all cross-site traffic
- **NAT/PAT** with extended ACLs, excluding VPN traffic from translation
- **Centralized DHCP** with `ip helper-address` relay across all VLANs
- **DMZ** hosting DNS, HTTP, Email, NTP, Syslog, and IoT servers

## Addressing Summary

| Segment | Block | Mask |
|---|---|---|
| HQ Departments | 192.168.170.0/24 | /27 (30 hosts) |
| Branch Departments | 192.168.171.0/24 | /28 (14 hosts) |
| DMZ Servers | 90.64.110.0/24 | /24 |
| Internal Backbone Links | 172.16.0.0/16 | /30 |
| ISP-Facing Links | 195.137.21.0, 195.137.22.0 | /30 |
| ISP-to-ISP Backbone | 12.12.12.0/30 | /30 |

## Verification & Testing

- End-to-end ping tests across VLANs and across sites
- Routing table inspection (OSPF adjacencies, default route propagation)
- IPSec VPN session state verification (ISAKMP + IPSec SAs)
- DHCP binding verification
- NAT translation table verification
  

## How to Open

1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (free with a Cisco Networking Academy account).
2. Open `network-topology.pkt`.
3. Refer to `report/` for full design documentation, configuration explanations, and verification screenshots.

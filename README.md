# Company Network Design & Implementation (Admin • Marketing • Sales • HR • IT Support)

## Overview
Two‑part project completed for a medium‑sized company:
- **Part 1 – Design (individual):** Logical topology in Cisco Packet Tracer with VLSM, VLAN plan, addressing table and WAN /30s.
- **Part 2 – Implementation (group):** Full configuration in Packet Tracer with VLANs, trunking, DHCP, switch security, EtherChannel (**LACP + PAgP**), hybrid routing (**Static + EIGRP**), and **HSRP** gateway redundancy for IT Support, plus an edge **Web Server** reachable from all clients.

## Topology
See the exported diagrams in [`topology/`](topology):
- `topology-part1.png`
- `topology-part2.png`

> Edge router connects all department routers via /30 WAN links. Each department has a dedicated router and switch (IT Support has two routers for HSRP). Admin uses **PAgP** between switches; Marketing uses **LACP** between switches. Sales uses **Layer‑3 switch (MLS)** for inter‑VLAN routing; Admin uses **Router‑on‑a‑Stick**.

## Addressing (VLSM)
- Private space **10.1.0.0/16** with 20% growth considered per LAN.
- /30 subnets used for **all** WAN point‑to‑point links.
- Per‑LAN subnets and device IPs are in CSV under [`addressing/`](addressing).

Quick view of main LAN blocks (full table in CSV):
| Department/LAN | Hosts (req) | Total | Prefix | Network | First | Last | Broadcast |
|---|---:|---:|---:|---|---|---|---|
| Admin LAN1 | 1580 | 2048 | /21 | 10.1.0.0 | 10.1.0.1 | 10.1.7.254 | 10.1.7.255 |
| Marketing LAN1 | 303 | 510 | /23 | 10.1.8.0 | 10.1.8.1 | 10.1.9.254 | 10.1.9.255 |
| IT Support LAN1 | 164 | 256 | /24 | 10.1.10.0 | 10.1.10.1 | 10.1.10.254 | 10.1.10.255 |
| Admin LAN2 | 156 | 256 | /24 | 10.1.11.0 | 10.1.11.1 | 10.1.11.254 | 10.1.11.255 |
| IT Support LAN3 | 116 | 128 | /25 | 10.1.12.0 | 10.1.12.1 | 10.1.12.126 | 10.1.12.127 |
| IT Support LAN2 | 68 | 128 | /25 | 10.1.12.128 | 10.1.12.129 | 10.1.12.254 | 10.1.12.255 |
| Sales LAN1 | 51 | 64 | /26 | 10.1.13.0 | 10.1.13.1 | 10.1.13.62 | 10.1.13.63 |
| HR LAN1 | 26 | 32 | /27 | 10.1.13.64 | 10.1.13.65 | 10.1.13.94 | 10.1.13.95 |

Full addressing CSVs are included here:
- [`addressing/subnet-plan.csv`](addressing/subnet-plan.csv)
- [`addressing/device-ip-plan.csv`](addressing/device-ip-plan.csv)

## Key Design Choices
- **EtherChannel:** Marketing uses **LACP** (Channel‑Group 1), Admin uses **PAgP** (Channel‑Group 2).
- **Redundancy:** IT Support uses **HSRP** with virtual gateway IPs for VLANs 70/80/90; Primary vs Standby routers with preemption.
- **Inter‑VLAN routing:** Admin uses **Router‑on‑a‑Stick**; Sales uses **Layer‑3 switching** on MLS.
- **DHCP:** Selected departments use DHCP with excluded ranges for gateways and servers.
- **Security:** SSH for management, encrypted credentials, port‑security, DHCP snooping (rate‑limit 3), DAI on access ports, BPDU Guard + PortFast.

## Implementation Highlights
- **VLANs:** Per department VLAN IDs (Admin 10/20, Marketing 30/40, Sales 50, HR 60, IT 70/80/90) with management VLANs for remote access.
- **Trunks:** Native VLAN set to 99 (per design), trunks between access and distribution devices.
- **Routing:** Two departments with **static routes**; three with **EIGRP** (no auto‑summary, LAN interfaces passive).
- **Edge:** Default static route to the **Web Server**; website reachable from any PC.

## Validation
See [`validation/README.md`](validation/README.md) for the exact checklist and **show** commands.
Add screenshots of successful pings, `show standby brief`, `show etherchannel summary`, `show vlan brief`, and web browsing.

## Artifacts
- [`pkt/company-network-design.pkt`](pkt/company-network-design.pkt)  ← add your Packet Tracer file here
- [`topology/`](topology)  ← exported topology images
- [`addressing/subnet-plan.csv`](addressing/subnet-plan.csv), [`addressing/device-ip-plan.csv`](addressing/device-ip-plan.csv)
- [`configs/`](configs)  ← sanitized router/switch configs
- [`screenshots/`](screenshots)  ← pings, traceroutes, show commands, web test

## What I Learned
- Applying VLSM at scale and planning for growth
- Designing L2 & L3 redundancy (EtherChannel, HSRP)
- Operating a hybrid routing environment (Static + EIGRP)
- Hardening campus networks with port‑security, snooping, and DAI


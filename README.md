# Capston-project-Computer-Network

# 🏢 Data Center Network — Scalable VTP and EtherChannel

A Cisco Packet Tracer project demonstrating a scalable data center network using **VTP (VLAN Trunking Protocol)** and **EtherChannel (LACP)** for high availability and simplified VLAN management.

---

## 📋 Project Overview

| Property | Details |
|----------|---------|
| **Tool** | Cisco Packet Tracer |
| **Core Switch** | Cisco 3560 Multilayer Switch (VTP Server) |
| **Access Switches** | 2× Cisco 2960 (VTP Client) |
| **End Devices** | 4× PCs |
| **VLANs** | VLAN 10 (HR), VLAN 20 (Finance) |
| **VTP Domain** | DATACENTER |
| **EtherChannel Protocol** | LACP (802.3ad) |

---

## 🗺️ Network Topology

```
                    ┌─────────────────────┐
                    │   Core Switch        │
                    │   (3560 Multilayer)  │
                    │   VTP Server         │
                    │   VLAN 10, 20        │
                    └────────┬────────────┘
                   ══════════╪══════════════
              (EtherChannel) │ (EtherChannel)
            Po1 (Fa0/1-2)   │   Po2 (Fa0/5-6)
                   ══════════╪══════════════
            ┌───────────────┘     └───────────────┐
            │                                     │
   ┌────────┴────────┐                 ┌──────────┴──────┐
   │   Switch0 (SW0)  │                 │  Switch1 (SW1)  │
   │   Cisco 2960     │                 │  Cisco 2960     │
   │   VTP Client     │                 │  VTP Client     │
   └──────┬─────┬────┘                 └────┬──────┬─────┘
          │     │                            │      │
       Fa0/3  Fa0/4                       Fa0/3  Fa0/4
          │     │                            │      │
       ┌──┴─┐ ┌─┴──┐                     ┌──┴─┐ ┌──┴─┐
       │ PC0│ │ PC1│                     │ PC2│ │ PC3│
       │V10 │ │V20 │                     │V10 │ │V20 │
       └────┘ └────┘                     └────┘ └────┘
```

> **══** = EtherChannel (2 physical links bundled as 1 logical link)

---

## 🔧 Key Concepts Used

### 1. VTP (VLAN Trunking Protocol)
VTP allows automatic VLAN propagation across the network. Instead of manually creating VLANs on every switch, VLANs are created once on the **VTP Server** and automatically synced to all **VTP Clients**.

| Switch | VTP Mode | Role |
|--------|----------|------|
| Core Switch (3560) | Server | Creates & distributes VLANs |
| Switch0 (2960) | Client | Receives VLANs automatically |
| Switch1 (2960) | Client | Receives VLANs automatically |

### 2. EtherChannel (LACP)
EtherChannel bundles multiple physical links into one logical link, providing:
- **Increased bandwidth** — 2× 100Mbps = 200Mbps effective throughput
- **Redundancy** — if one cable fails, traffic flows through the other
- **Load balancing** — traffic is distributed across both links

| Port-Channel | Switch A Port | Switch B Port | Protocol |
|-------------|--------------|--------------|---------|
| Po1 | Core Fa0/1-2 | SW0 Fa0/1-2 | LACP Active |
| Po2 | Core Fa0/5-6 | SW1 Fa0/5-6 | LACP Active |

---

## 📡 VLAN Configuration

| VLAN ID | Name | Subnet | Assigned PCs |
|---------|------|--------|-------------|
| 10 | HR | 192.168.10.0/24 | PC0, PC2 |
| 20 | Finance | 192.168.20.0/24 | PC1, PC3 |

### PC IP Addresses

| Device | VLAN | IP Address | Subnet Mask |
|--------|------|------------|-------------|
| PC0 | 10 | 192.168.10.1 | 255.255.255.0 |
| PC1 | 20 | 192.168.20.1 | 255.255.255.0 |
| PC2 | 10 | 192.168.10.2 | 255.255.255.0 |
| PC3 | 20 | 192.168.20.2 | 255.255.255.0 |



interface fa0/3
 switchport mode access
 switchport access vlan 10

interface fa0/4
 switchport mode access
 switchport access vlan 20
```

### Expected Output — `show etherchannel summary`
```
Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------
1      Po1(SU)       LACP        Fa0/1(P) Fa0/2(P)
2      Po2(SU)       LACP        Fa0/5(P) Fa0/6(P)
```


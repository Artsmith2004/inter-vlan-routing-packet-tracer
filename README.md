# Inter-VLAN Routing using Cisco Packet Tracer

Designed and configured a multi-VLAN network in **Cisco Packet Tracer** with VLAN segmentation, IP addressing, trunk links, multiple switches and **Inter-VLAN Routing**, so that devices in different VLANs can communicate with each other.

## Tools & Concepts

| Item | Details |
|---|---|
| **Simulator** | Cisco Packet Tracer 9.0.0 |
| **Router** | Cisco CGR1240 (Layer 3 inter-VLAN routing) |
| **Switches** | 4 × Cisco Catalyst 2960-24TT |
| **End devices** | 14 × PC-PT |
| **Concepts** | VLANs, access ports, 802.1Q trunk links, IP subnetting, Inter-VLAN routing, ping testing |

## Network Topology

Four switches are connected to a central router through trunk links (dashed lines). Each VLAN has its own subnet, and the router forwards traffic between VLANs.

![Network topology](images/01-network-topology.png)

### Physical connections

| Link | Ports |
|---|---|
| Switch0 ↔ Router | Switch0 Fa0/1 ↔ Router Fa2/4 |
| Switch2 ↔ Router | Switch2 Fa0/1 ↔ Router Fa2/3 |
| Switch3 ↔ Router | Switch3 Fa0/1 ↔ Router Fa2/5 |
| Switch0 ↔ Switch1 | Switch0 Fa0/2 ↔ Switch1 Fa0/1 |

## VLAN & IP Addressing Plan

| VLAN ID | VLAN Name | Subnet | Switch | Hosts (IP addresses) |
|---|---|---|---|---|
| 10 | HR | 172.16.1.0/24 | Switch0 | PC0 – 172.16.1.4, PC1 – 172.16.1.5 |
| 20 | SALES | 172.16.2.0/24 | Switch0 | PC2 – 172.16.2.4, PC3 – 172.16.2.5 |
| 30 | DEV | 10.10.5.0/24 | Switch1 | PC13 – 10.10.5.4, PC12 – 10.10.5.5 |
| 40 | PYTHON | 192.168.1.0/24 | Switch2 | PC4 – 192.168.1.4, PC5 – 192.168.1.5 |
| 50 | REACT | 172.20.5.0/24 | Switch2 | PC6 – 172.20.5.4, PC7 – 172.20.5.5 |
| 60 | DB | 10.20.1.0/24 | Switch3 | PC11 – 10.20.1.4, PC10 – 10.20.1.5 |
| 70 | TEST | 172.16.3.0/24 | Switch3 | PC8 – 172.16.3.4, PC9 – 172.16.3.5 |

## Configuration

> The commands below are the standard approach for this design (router-on-a-stick). Replace the gateway addresses (`.1`) and port numbers with the ones you used.

### 1. Create VLANs on the switches

```
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name HR
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name SALES
Switch(config-vlan)# exit
```

Repeat for the VLANs used on each switch.

### 2. Configure access ports (PC-facing)

```
Switch(config)# interface fastEthernet 0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit
```

### 3. Configure trunk ports (switch-to-router and switch-to-switch)

```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

### 4. Configure the router for Inter-VLAN routing

One sub-interface per VLAN on the router link that carries it (example for VLAN 10 and 20):

```
Router(config)# interface fastEthernet 2/4
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface fastEthernet 2/4.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 172.16.1.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface fastEthernet 2/4.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 172.16.2.1 255.255.255.0
Router(config-subif)# exit
```

Repeat for the other VLANs on their respective router interfaces.

### 5. Configure the PCs

On each PC go to **Desktop → IP Configuration** and set the IP address, subnet mask and **default gateway** (the router sub-interface address of that VLAN).

## Verification

```
show vlan brief
show interfaces trunk
show ip interface brief      (on the router)
show ip route                (on the router)
```

Connectivity tests:

- Ping between PCs in the **same VLAN** (for example PC0 → PC1). This should succeed.
- Ping between PCs in **different VLANs** (for example PC0 → PC2, or PC4 → PC8). This should succeed through the router.

## Key Learnings

- Segmenting a network into multiple VLANs with separate subnets
- Configuring access and trunk ports across multiple switches
- How a router provides communication between VLANs (Inter-VLAN routing)
- Assigning IP addresses and default gateways
- Verifying connectivity with ping and show commands

## Files

- `ccna day 4.pkt`: Packet Tracer project file (add it to the repo so others can open the topology)

## Author

**Your Name**

# Lab 01: Peer-to-Peer Network

## Objective

Build and test a basic peer-to-peer network in Cisco Packet Tracer to understand how hosts can communicate without relying on a central server.

## Lab Environment

- Cisco Packet Tracer
- PCs
- Cisco Switch
- Wireless Access Point
- Ethernet crossover / straight-through cable
- Wireless connections

## What I Built

I tested peer-to-peer connectivity using three different network configurations.

### 1. Direct Connection

Connected two PCs directly using an Ethernet crossover cable and assigned both devices IP addresses on the same subnet.

Example:

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC1 | 192.168.10.1 | 255.255.255.0 |
| PC2 | 192.168.10.2 | 255.255.255.0 |

### 2. Switched Peer-to-Peer Network

Added a third PC and connected the hosts through a switch.

This demonstrated that peer-to-peer does not mean devices must be physically connected directly. A switch can provide network connectivity while the hosts still communicate as peers without a central server.

### 3. Wireless Peer-to-Peer Network

Connected wireless hosts through an access point and verified that they could communicate over the wireless LAN.

## Connectivity Testing

I used `ping` between hosts to verify IP connectivity.

## What I Learned

- Peer-to-peer networks allow devices to communicate and share resources without requiring a central server.
- Two Ethernet hosts can communicate through a direct connection.
- A switch allows additional wired hosts to participate in the same peer-to-peer network.
- An access point allows wireless devices to communicate on the LAN.
- Peer-to-peer describes the network architecture, not the physical connection method.
- Devices on the same IP subnet can communicate locally without a router.
- `ping` can be used to verify connectivity between network hosts.

## Skills Practiced

- Peer-to-peer networking
- IPv4 addressing
- Subnet masks
- Ethernet connectivity
- Crossover cabling
- Layer 2 switching
- Wireless access point connectivity
- ICMP and `ping`
- Basic network connectivity testing

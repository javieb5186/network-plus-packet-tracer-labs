# Lab 04: Layer 3 Spine-and-Leaf

## Objective

Build and compare common enterprise network architecture models in Cisco Packet Tracer:

- Three-tier hierarchical architecture
- Collapsed core architecture
- Spine-and-leaf architecture

The primary focus of the lab was building a functional Layer 3 spine-and-leaf network and testing redundant paths, dynamic routing, and network convergence.

## Lab Environment

- Cisco Packet Tracer
- Layer 2 switches
- Layer 3 / multilayer switches
- Router
- PCs
- IPv4 addressing
- /30 point-to-point subnets
- RIPv2
- ICMP testing

---

## Architecture Models

### Three-Tier Hierarchical Model

The traditional three-tier hierarchical model separates the network into three layers:

```text
             Core
              |
         Distribution
              |
            Access
              |
          End Devices
```

The layers have different responsibilities:

- **Access** — Connects end-user devices to the network.
- **Distribution** — Aggregates access switches and provides Layer 3 functionality.
- **Core** — Provides high-speed connectivity between major portions of the network.

---

### Collapsed Core

A collapsed-core architecture combines the core and distribution functions.

```text
        Core / Distribution
             /       \
            /         \
         Access      Access
           |            |
        Devices      Devices
```

This reduces the number of network devices and layers required compared with a traditional three-tier architecture.

It can be useful when a full three-tier design would introduce unnecessary complexity.

---

## Spine-and-Leaf Architecture

The main portion of this lab focused on building a functional Layer 3 spine-and-leaf network.

The topology consisted of:

- 2 Layer 3 spine switches
- 3 Layer 3 leaf switches
- 1 PC connected to each leaf
- 1 router
- Redundant leaf-to-spine connections

### Topology

Each leaf switch was connected to both spine switches.

This created multiple possible paths through the network.

![Spine-and-leaf topology](spine-leaf-topology.png)

---

## Initial Layer 2 Testing

I initially built the topology using Layer 2 leaf switches.

Because each leaf connected to multiple spine switches, the topology contained redundant Layer 2 paths.

Using:

```text
show spanning-tree
```

I discovered that Spanning Tree Protocol had placed one or more ports into a blocking state.

This demonstrated an important difference between Layer 2 and Layer 3 redundancy.

```text
Layer 2 redundant paths
        ↓
Potential switching loop
        ↓
STP detects the loop
        ↓
STP blocks a redundant path
```

This prevented Layer 2 loops but also meant that some of the physical links were not actively forwarding traffic.

---

## Converting to a Layer 3 Fabric

I replaced the Layer 2 leaf switches with multilayer switches so the leaf-to-spine connections could operate as routed Layer 3 links.

A Layer 3 switch can still operate as a normal Layer 2 switch, so the routed interfaces had to be explicitly configured.

Example:

```text
interface GigabitEthernet0/1
 no switchport
 ip address 10.0.0.1 255.255.255.252
 no shutdown
```

The command:

```text
no switchport
```

converts the physical switchport into a routed Layer 3 interface.

Routing was also enabled on the multilayer switches:

```text
ip routing
```

---

## Point-to-Point Addressing

Each routed leaf-to-spine connection was assigned its own subnet.

I used `/30` networks for the routed point-to-point connections.

Example addressing:

```text
Leaf1 ↔ Spine1 → 10.0.0.0/30
Leaf1 ↔ Spine2 → 10.0.0.4/30

Leaf2 ↔ Spine1 → 10.0.0.8/30
Leaf2 ↔ Spine2 → 10.0.0.12/30

Leaf3 ↔ Spine1 → 10.0.0.16/30
Leaf3 ↔ Spine2 → 10.0.0.20/30
```

Additional `/30` networks were used for the routed connections toward the router.

A `/30` uses:

```text
255.255.255.252
```

and provides two usable IPv4 addresses.

---

## Leaf LAN Networks

Each PC was placed on a different network behind its leaf switch.

The PC-facing routed interface on each leaf provided the default gateway for its connected PC.

This allowed the leaf switch to route traffic from the local PC into the spine-and-leaf fabric.

---

## Dynamic Routing

RIPv2 was used to dynamically exchange routing information between the Layer 3 switches and router.

The routed transit networks and local PC networks were advertised.

Example:

```text
router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 192.168.10.0
```

Each leaf advertised its own LAN network.

For example:

```text
Leaf1 → 192.168.10.0/24
Leaf2 → 192.168.20.0/24
Leaf3 → 192.168.30.0/24
```

This allowed the rest of the network to learn where each PC network was located.

---

## Routing Table Verification

I used:

```text
show ip route
```

to verify that the Layer 3 switches were learning remote networks.

The routing tables contained:

```text
C = Connected
L = Local
R = RIP
```

I also encountered stale RIP routes after changing some of the PC networks.

Clearing or allowing the routing information to reconverge corrected the routing tables.

This reinforced the importance of checking the routing table during troubleshooting instead of relying only on the physical topology.

---

## Redundancy Test

After establishing full connectivity, I tested the redundant spine-and-leaf paths.

With both spine paths operational, the routing table showed two next-hop addresses for a remote network.

For example:

```text
Destination: 192.168.10.0/24

Path 1 → Spine 1
Path 2 → Spine 2
```

I then shut down one of the leaf-to-spine links.

After the routing table updated, only one next-hop remained.

Connectivity continued through the remaining spine.

When the failed link was restored, the routing table updated again and both paths became available.

![Routing_table test](spine-leaf-routing-table.png)

---

## Layer 2 vs Layer 3 Redundancy

One of the most useful observations from this lab was seeing how redundant paths behaved differently at Layer 2 and Layer 3.

### Layer 2

```text
Multiple paths
      ↓
Potential switching loop
      ↓
STP
      ↓
Redundant path blocked
```

### Layer 3

```text
Multiple routed paths
      ↓
Routing table
      ↓
Multiple paths available
      ↓
Link failure
      ↓
Routing table updates
      ↓
Remaining path continues forwarding traffic
```

This demonstrated why routed Layer 3 connections are useful in a spine-and-leaf architecture.

---

## Connectivity Testing

I verified connectivity using `ping` between:

- PCs connected to different leaf switches
- PCs and the router
- Directly connected Layer 3 switch interfaces
- Remote networks across the spine-and-leaf fabric

I also tested connectivity while intentionally disabling one of the redundant links.

Communication continued after the routing tables updated.

---

## What I Learned

- Three-tier architecture separates access, distribution, and core functions.
- Collapsed core combines the distribution and core functions.
- Spine-and-leaf uses multiple spine switches to provide paths between leaf switches.
- Every leaf connects to every spine in a spine-and-leaf topology.
- Layer 3 switches can perform both switching and routing functions.
- A Layer 3 switchport does not automatically operate as a routed interface.
- `no switchport` converts a switchport into a routed Layer 3 interface.
- `ip routing` enables Layer 3 routing functionality on a multilayer switch.
- Routed physical interfaces can be assigned IP addresses similarly to router interfaces.
- Each routed point-to-point connection requires its own IP subnet.
- `/30` networks can be used for IPv4 point-to-point links.
- End devices require a default gateway to communicate with remote networks.
- Each routed LAN should have a unique network address.
- Routing protocols advertise remote network reachability.
- Routing tables can contain multiple paths to the same destination.
- Dynamic routing allows the network to adapt when a path becomes unavailable.
- Routing tables change when links fail and recover.
- Layer 2 redundant paths can cause loops and trigger STP.
- Layer 3 routed links avoid Layer 2 switching loops between those routed interfaces.
- `show spanning-tree` can identify ports blocked by STP.
- `show ip route` is essential for troubleshooting Layer 3 connectivity.
- Old routing information can temporarily cause traffic to follow an unexpected path after network changes.

---

## Skills Practiced

- Cisco Packet Tracer
- Three-tier hierarchical architecture
- Collapsed-core architecture
- Spine-and-leaf architecture
- Layer 2 switching
- Layer 3 switching
- Multilayer switch configuration
- Routed switchports
- `no switchport`
- `ip routing`
- IPv4 addressing
- `/30` subnetting
- Default gateway configuration
- RIPv2
- Dynamic routing
- Routing table analysis
- Equal-cost/redundant path observation
- Route convergence
- Spanning Tree Protocol
- `show spanning-tree`
- `show ip route`
- ICMP / ping testing
- Link failure simulation
- Network redundancy
- Network troubleshooting

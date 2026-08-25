# Lab 08: Enterprise Wireless & Wireless LAN Controller

## Objective

Build a basic enterprise wireless network in Cisco Packet Tracer using a Wireless LAN Controller (WLC) and multiple lightweight access points.

The lab focused on understanding how enterprise wireless networks use a WLC to centrally configure and manage multiple access points instead of configuring each AP individually.

The lab covered:

- Wireless LAN Controllers
- Lightweight access points
- Centralized wireless management
- DHCP for AP addressing
- WLANs
- SSIDs
- WPA2 security
- AES encryption
- Pre-shared keys
- AP Groups
- BSSIDs
- Basic wireless roaming concepts

---

## Lab Environment

- Cisco Packet Tracer
- Wireless LAN Controller
- Two lightweight access points
- Layer 2 switch
- Router
- Wireless laptop
- DHCP
- IPv4
- WPA2
- AES
- Pre-shared key authentication

---

# Network Topology

The WLC and lightweight APs were connected to the wired network through a Layer 2 switch.

An important concept demonstrated in this lab was that the WLC does **not** need to be the physical connection point for the APs.

Instead:

```text
Switch
→ Provides network connectivity

WLC
→ Provides centralized wireless management
```

The APs communicate with the WLC across the network.

---

# IP Addressing

The wireless infrastructure used:

```text
Network:
192.168.10.0/24
```

The router was configured as:

```text
Router:
192.168.10.1/24
```

The WLC used a static management address:

```text
WLC:
192.168.10.2/24

Default Gateway:
192.168.10.1
```

The lightweight APs received their network configuration through DHCP.

```text
AP1 → DHCP
AP2 → DHCP
```

The wireless client also used DHCP.

---

# DHCP Configuration

The router provided DHCP services for the network.

Infrastructure addresses were reserved:

```text
192.168.10.1 - 192.168.10.99
```

DHCP addresses were then available above that range.

Example router configuration:

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.99

ip dhcp pool WIRELESS
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
```

After powering on and resetting the lightweight APs, they successfully received:

```text
IP Address
Subnet Mask
Default Gateway
```

from the DHCP server.

This verified that the APs had Layer 3 connectivity before configuring the wireless network.

---

# Lightweight Access Points

The lab used lightweight APs rather than individually managed autonomous APs.

With autonomous APs, each AP would need to be configured separately:

```text
AP1
├── SSID
├── Security
└── Wireless settings

AP2
├── SSID
├── Security
└── Wireless settings
```

With lightweight APs:

```text
              WLC
               |
        Configure centrally
          _____|_____
         |           |
        AP1         AP2
```

The APs receive their wireless configuration through centralized management.

---

# AP Discovery and WLC Management

After receiving their network configuration, both APs appeared within the WLC.

The process was conceptually:

```text
AP powers on
      ↓
Obtains IP configuration
      ↓
Gets network connectivity
      ↓
Discovers WLC
      ↓
Joins WLC
      ↓
Becomes centrally managed
```

Both APs could then participate in WLANs configured through the controller.

---

# AP Groups

Both APs appeared within the WLC's AP Groups area.

AP Groups can be used to organize access points and control how WLAN configurations are associated with groups of APs.

Advanced AP Group configuration was not required for this lab.

The primary goal was simply to verify that multiple APs could be managed through the same WLC.

---

# Creating the WLAN

A WLAN was created centrally through the WLC.

```text
Profile Name:
Enterprise-WiFi

SSID:
Enterprise-WiFi

VLAN:
1
```

The lab remained on VLAN 1 because VLAN segmentation was outside the scope of this exercise.

The important concept was that the WLAN was configured **once on the WLC** rather than individually on every AP.

---

# WLAN vs SSID

A WLAN represents the wireless network configuration managed by the controller.

The SSID is the wireless network name advertised to clients.

For this lab:

```text
WLAN
      ↓
Enterprise-WiFi configuration
      ↓
SSID
      ↓
"Enterprise-WiFi"
```

Wireless users see the SSID when searching for available Wi-Fi networks.

---

# Centralized SSID Deployment

After creating `Enterprise-WiFi` on the WLC, both lightweight APs advertised the WLAN.

```text
                 WLC
                  |
           Enterprise-WiFi
             _____|_____
            |           |
           AP1         AP2
           )))         (((
            |           |
       Enterprise   Enterprise
          WiFi         WiFi
```

The APs did not require the WLAN to be independently configured on each device.

This demonstrated the primary advantage of using a Wireless LAN Controller:

> Configure the wireless network centrally and distribute the configuration across managed APs.

---

# Multiple APs With the Same SSID

The wireless laptop detected two wireless access points advertising:

```text
Enterprise-WiFi
```

This occurred because both APs were broadcasting the same centrally configured SSID.

Although the SSID was identical, each AP remained a separate physical wireless access point.

Conceptually:

```text
AP1
BSSID: AP1's wireless MAC
SSID: Enterprise-WiFi

AP2
BSSID: AP2's wireless MAC
SSID: Enterprise-WiFi
```

This distinction allows multiple APs to provide coverage for the same wireless network.

---

# SSID vs BSSID

The **SSID** identifies the wireless network.

Example:

```text
Enterprise-WiFi
```

The **BSSID** identifies a particular wireless AP/radio providing that network.

Therefore:

```text
             Enterprise-WiFi
                   SSID
                 /      \
                /        \
              AP1        AP2
            BSSID 1    BSSID 2
```

A client can therefore see multiple APs providing access to the same wireless network.

---

# Wireless Security

WPA2 security was configured centrally for the WLAN.

The basic configuration used:

```text
Security:
WPA2

Authentication:
Pre-Shared Key (PSK)

Encryption:
AES
```

The same security configuration was then used by the managed APs.

Conceptually:

```text
              WLC
               |
      Enterprise-WiFi
      WPA2 + AES + PSK
          _____|_____
         |           |
        AP1         AP2
```

This again demonstrated centralized configuration.

Instead of manually configuring the same security settings on each AP, the WLAN security policy was configured through the WLC.

---

# WPA2

WPA2 provides security for the wireless network.

For this lab, WPA2 used:

```text
AES
+
Pre-Shared Key
```

AES provides encryption for wireless traffic.

The pre-shared key provides the shared credential required for the client to join the WLAN.

---

# Wireless Client Connection

After configuring the WLAN and security settings, the wireless laptop discovered:

```text
Enterprise-WiFi
```

The laptop was configured with the matching WPA2 security settings and pre-shared key.

The client successfully connected to the WLAN.

This verified:

```text
WLC configuration
        ↓
AP receives WLAN configuration
        ↓
AP advertises SSID
        ↓
Laptop discovers SSID
        ↓
Laptop authenticates
        ↓
Wireless connection established
```

---

# Enterprise Wireless Architecture

The lab demonstrated the difference between physical network connectivity and wireless management.

The switch connects the devices together.

The WLC centrally controls the wireless environment.

Therefore:

```text
Switch
→ Connectivity

WLC
→ Control and management

Lightweight AP
→ Provides wireless access

Wireless Client
→ Connects to WLAN
```

---

# Wireless Roaming

The use of the same SSID across multiple APs also introduces the concept of wireless roaming.

Consider:

```text
AP1                                      AP2
 )))                                    (((
    \                                  /
     \                                /
      Laptop → → → → → → → → Laptop
```

As a wireless client moves away from one AP and toward another, it can potentially reassociate with the AP providing a better connection while remaining on the same WLAN.

Conceptually:

```text
Connected to AP1
       ↓
Move through building
       ↓
AP1 signal becomes weaker
       ↓
AP2 becomes preferable
       ↓
Client reassociates with AP2
       ↓
Still connected to Enterprise-WiFi
```

A physical roaming test was not performed because it would require configuring the physical workspace and wireless coverage behavior within Packet Tracer.

The concept was still demonstrated through the use of multiple APs broadcasting the same SSID.

---

# Enterprise Authentication

This lab used WPA2 with a pre-shared key for simplicity.

A larger enterprise environment may instead use:

```text
Wireless Client
       ↓
      AP
       ↓
      WLC
       ↓
802.1X / RADIUS
       ↓
Authentication Server
```

This allows individual users or devices to authenticate instead of giving every employee the same Wi-Fi password.

802.1X/RADIUS authentication was left for a future lab so this project could remain focused on WLC fundamentals.

---

# Basic WLC Workflow

The overall process demonstrated in the lab was:

```text
Build wired infrastructure
          ↓
Configure router
          ↓
Configure DHCP
          ↓
Power lightweight APs
          ↓
APs receive IP configuration
          ↓
APs discover/join WLC
          ↓
Create WLAN on WLC
          ↓
Configure SSID
          ↓
Configure WPA2/AES/PSK
          ↓
WLC manages multiple APs
          ↓
APs advertise same SSID
          ↓
Wireless client connects
```

---

# What I Learned

- A Wireless LAN Controller provides centralized management for enterprise wireless networks.
- APs do not have to physically connect directly to the WLC.
- APs and the WLC can communicate through the switched network.
- The switch provides network connectivity while the WLC provides wireless management.
- Lightweight APs can obtain their IP configuration through DHCP.
- Lightweight APs can discover and join a WLC.
- Multiple APs can be managed from one controller.
- A WLAN can be created centrally instead of configuring every AP separately.
- The SSID is the wireless network name visible to clients.
- Multiple APs can advertise the same SSID.
- Different APs advertising the same SSID still have different BSSIDs.
- WPA2 can protect a WLAN.
- AES provides wireless encryption.
- A PSK provides a shared authentication credential.
- WLAN security settings can be centrally managed.
- Multiple APs advertising the same WLAN can support wireless roaming.
- Roaming allows clients to move between AP coverage areas while remaining on the same WLAN.
- Enterprise wireless environments can use 802.1X/RADIUS instead of a shared PSK.
- AP Groups can organize APs and their WLAN assignments.
- Centralized management becomes increasingly useful as the number of APs increases.

---

# Skills Practiced

- Cisco Packet Tracer
- Enterprise wireless networking
- Wireless LAN Controllers
- Lightweight access points
- WLC management
- AP discovery
- AP Groups
- WLAN configuration
- SSID configuration
- DHCP
- IPv4 addressing
- Default gateways
- WPA2
- AES
- Pre-shared keys
- Wireless client configuration
- Centralized WLAN management
- SSID vs BSSID
- Wireless roaming concepts
- Autonomous vs controller-based wireless architecture
- Basic enterprise wireless troubleshooting

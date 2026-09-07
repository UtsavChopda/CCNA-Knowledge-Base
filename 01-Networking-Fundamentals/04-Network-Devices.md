# 🔌 Network Devices

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Identify common network devices and their purposes.
- Explain the difference between a NIC, hub, switch, router, access point, firewall, modem, and server.
- Understand **Layer 2 vs Layer 3** device behavior at a beginner level.
- Identify common interfaces and ports.
- Understand where each device is commonly placed in a network.
- Use basic Cisco IOS commands to inspect interfaces and neighbors.
- Troubleshoot common device and connectivity problems.

---

# 1. What Is a Network Device?

A **network device** is hardware that helps connect, control, forward, protect, or provide services over a network.

Think of a network like a city's transportation system:

- 💻 **PC/phone** = person traveling
- 🛣️ **Cable/Wi-Fi** = road
- 🔀 **Switch** = local traffic organizer
- 🧭 **Router** = chooses the road to another network
- 🛡️ **Firewall** = security checkpoint
- 📡 **Access Point** = wireless entry point
- 🗄️ **Server** = service provider

Different devices solve different networking problems.

---

# 2. The Most Important Devices

| Device | Main purpose | Typical OSI focus |
|---|---|---|
| NIC | Connects a host to a network | Layer 1/2 |
| Repeater | Regenerates signals | Layer 1 |
| Hub | Repeats traffic to ports | Layer 1 |
| Bridge | Connects LAN segments using MAC addresses | Layer 2 |
| Switch | Forwards Ethernet frames using MAC addresses | Layer 2, sometimes Layer 3 |
| Router | Forwards packets between IP networks | Layer 3 |
| Wireless AP | Connects wireless clients to a LAN | Layers 1/2 |
| Firewall | Enforces security policy | Multiple layers |
| Modem/ONT | Provides access to a service-provider connection | Depends on technology |
| Server | Provides network services | Multiple layers |

> ⚠️ OSI-layer descriptions are simplified learning models. Real enterprise devices can perform functions across multiple layers.

---

# 3. Network Interface Card (NIC) 💻

A **Network Interface Card (NIC)** provides a device with network connectivity.

It may be:

- Built into a motherboard
- A physical expansion card
- A USB network adapter
- A wireless adapter

A NIC commonly has a **MAC address** used for Layer 2 Ethernet communication.

### Examples

- Laptop Wi-Fi adapter
- Desktop Ethernet adapter
- Server Ethernet interface
- USB-to-Ethernet adapter

### Basic idea

```text
[Computer]
    |
   NIC
    |
 Ethernet / Wi-Fi
    |
 [Network]
```

Without an appropriate network interface, a host cannot directly participate in that network technology.

---

# 4. Repeater 📶

A **repeater** receives a signal and regenerates it so the signal can travel farther.

It primarily operates at **Layer 1 — Physical**.

### Simple idea

```text
Signal ───> [Repeater] ───> Regenerated Signal
```

Repeaters do not make routing decisions and do not normally inspect destination IP addresses.

Modern Ethernet networks generally use switches and fiber technologies rather than standalone repeaters for ordinary LAN extension.

---

# 5. Hub 🔁

A **hub** is a basic Layer 1 device that repeats an incoming signal out other ports.

```text
              [Hub]
             /  |  \
           PC1 PC2 PC3
```

If PC1 sends traffic into the hub, the hub repeats the signal toward the other ports.

### Important characteristics

- Layer 1 device
- No MAC-address learning
- No intelligent frame forwarding
- Shared collision domain
- Half-duplex operation in traditional Ethernet hub environments

### Why hubs are rarely used today

Switches provide much more efficient Ethernet communication and are the standard choice for modern wired LANs.

### Interview point

> **Hub = repeat to ports. Switch = intelligently forward based on MAC addresses.**

---

# 6. Bridge 🌉

A **bridge** connects network segments and makes forwarding decisions using Layer 2 information such as MAC addresses.

Historically, bridges were separate devices. Modern Ethernet switches perform bridging functions and are essentially multiport bridges.

```text
LAN A ─── [Bridge] ─── LAN B
```

### Key idea

> A switch can be thought of as a **high-performance multiport bridge**.

---

# 7. Switch 🔀 ⭐

A **switch** is one of the most important devices in the CCNA.

A typical Layer 2 switch forwards Ethernet **frames** based on destination **MAC addresses**.

```text
PC1 ─┐
PC2 ─┼── [SWITCH] ─── Server
PC3 ─┤
PC4 ─┘
```

### What does a switch learn?

A switch builds a **MAC address table**.

Conceptually:

```text
MAC Address          Port
-------------------  -----
AAAA.AAAA.AAAA       Gi0/1
BBBB.BBBB.BBBB       Gi0/2
CCCC.CCCC.CCCC       Gi0/3
```

The switch learns the source MAC address of incoming frames and associates it with the receiving interface.

### Basic forwarding behavior

If the destination MAC is known:

➡️ Forward the frame toward the appropriate port.

If the destination MAC is unknown:

➡️ Flood the frame within the relevant Layer 2 scope, excluding the port on which it was received.

If the frame is broadcast:

➡️ Flood it within the relevant broadcast domain, subject to VLAN and other controls.

---

# 8. Switch Interfaces and Ports

A switch may have interfaces such as:

```text
FastEthernet0/1
GigabitEthernet0/1
GigabitEthernet0/2
TenGigabitEthernet1/1/1
```

The exact naming depends on the Cisco platform.

### Common speed terms

| Term | Typical meaning |
|---|---|
| Fast Ethernet | 100 Mbps |
| Gigabit Ethernet | 1 Gbps |
| 10 Gigabit Ethernet | 10 Gbps |
| 25/40/100+ Gigabit Ethernet | Higher-speed enterprise/data-center links |

> Always verify the actual interface capabilities of the specific device. Interface numbering and supported speeds vary by model.

---

# 9. Router 🧭

A **router** connects different IP networks and makes Layer 3 forwarding decisions.

```text
LAN A                         LAN B
192.168.1.0/24                10.0.0.0/24
     |                              |
   [Switch] ─── [ROUTER] ─── [Switch]
```

A router examines the destination IP address and uses its **routing table** to determine where to send the packet.

### Simple example

PC1:

```text
IP: 192.168.1.10/24
Gateway: 192.168.1.1
```

Server:

```text
IP: 10.0.0.10/24
```

Because the server is in a different IP network, PC1 sends traffic toward its default gateway.

The router then makes a Layer 3 forwarding decision.

---

# 10. Switch vs Router 🔀🧭

| Feature | Switch | Router |
|---|---|---|
| Main data unit | Ethernet frame | IP packet |
| Main address | MAC address | IP address |
| Primary function | Connect devices in LANs | Connect different IP networks |
| Typical OSI focus | Layer 2 | Layer 3 |
| Uses MAC table | Yes | Not as its primary forwarding table |
| Uses routing table | Not for basic Layer 2 switching | Yes |
| Separates IP networks | No, not as a basic L2 switch | Yes |
| Typical example | Access switch | Enterprise router |

> Modern multilayer switches can also perform Layer 3 routing, so the device category and exact capabilities depend on the platform.

---

# 11. Default Gateway 🚪

A **default gateway** is the Layer 3 device a host uses when it needs to send traffic outside its local IP subnet.

Example:

```text
PC
IP:      192.168.1.10
Mask:    255.255.255.0
Gateway: 192.168.1.1
             |
          [Router]
             |
         Other networks
```

The gateway is commonly the IP address of a router or Layer 3 switch interface on the local subnet.

### Important distinction

A switch does not automatically become a host's default gateway simply because the PC is connected to it.

---

# 12. Wireless Access Point 📡

A **Wireless Access Point (AP)** provides wireless network access to client devices.

```text
       Laptop 📱
          ))
         ))
      [Wi-Fi AP]
           |
           |
        [Switch]
           |
       [Network]
```

An AP commonly bridges wireless client traffic into the wired LAN.

### Common uses

- Office Wi-Fi
- College campus Wi-Fi
- Hotel Wi-Fi
- Home wireless network
- Enterprise wireless networks

### AP vs Router

A wireless router sold for home use may combine several functions in one box:

- Router
- Switch
- Wireless AP
- DHCP server
- Firewall/NAT functions

Enterprise networks often use separate dedicated devices or integrated platforms depending on the architecture.

---

# 13. Firewall 🛡️

A **firewall** controls traffic according to security policies.

```text
Internet
   |
[Firewall]
   |
Internal Network
```

It may inspect information such as:

- Source/destination IP
- Protocol
- Source/destination port
- Connection state
- Application information, depending on the firewall

### Example policy

> Allow HTTPS traffic to a public web service but block unauthorized inbound traffic.

Firewalls can operate across multiple OSI layers depending on their capabilities.

### Firewall vs Router

A router's primary role is **packet forwarding between networks**.

A firewall's primary role is **security policy enforcement**.

A modern firewall can also perform routing, NAT, VPN, and other functions.

---

# 14. Modem / ONT 🌐

A **modem** or **ONT (Optical Network Terminal)** can provide the customer-side interface to a service-provider access technology.

The exact device depends on the ISP technology.

### Example

```text
ISP
 |
Fiber / Cable / DSL
 |
[ONT / Modem]
 |
[Router / Firewall]
 |
[LAN]
```

Do not assume every Internet connection uses a traditional modem. Fiber connections commonly use an ONT or integrated optical equipment.

---

# 15. Server 🗄️

A **server** provides services to clients over a network.

Examples:

- 🌐 Web server
- 📁 File server
- 🧑‍💻 Authentication server
- 🗃️ Database server
- 📧 Mail server
- 🕒 NTP server
- 🏷️ DNS server
- 🔐 RADIUS/TACACS+ server

A server is not defined only by its physical shape. “Server” primarily describes a system providing services to other systems.

---

# 16. Network Devices in a Real Network 🏢

A small enterprise could look like:

```text
                    Internet
                       |
                 [ISP Modem/ONT]
                       |
                [Firewall/Router]
                       |
                  [Core/L3 SW]
                  /          \
             [Access SW1]  [Access SW2]
              /   |   \      /   |   \
            PCs  APs Phones PCs  APs Phones
                       |
                    Servers
```

Each device has a different role.

---

# 17. Cisco Device Interface Basics

Cisco devices have different interface types.

### Ethernet interfaces

```cisco
GigabitEthernet0/0
GigabitEthernet0/1
```

### Serial interfaces

Some older Cisco platforms and lab scenarios use serial interfaces for WAN concepts.

```cisco
Serial0/0/0
```

> Modern enterprise WANs commonly use Ethernet and other technologies, but serial interfaces still appear in many CCNA learning labs and legacy environments.

---

# 18. Important Cisco Verification Commands 🔎

## `show ip interface brief`

```cisco
show ip interface brief
```

Quickly displays interface names, IP addresses, status, and protocol state.

---

## `show interfaces`

```cisco
show interfaces
```

Provides detailed interface information, including counters, errors, speed, duplex, and operational state.

---

## `show interfaces status`

```cisco
show interfaces status
```

Useful on switches for a quick port summary.

---

## `show mac address-table`

```cisco
show mac address-table
```

Displays the switch's learned MAC address table.

This is one of the most important commands for understanding Layer 2 switching.

---

## `show arp`

```cisco
show arp
```

Displays ARP information maintained by a Cisco device.

It helps relate IPv4 addresses to MAC addresses on reachable local networks/interfaces.

---

## `show ip route`

```cisco
show ip route
```

Displays the router's IPv4 routing table.

This is one of the most important commands for understanding Layer 3 forwarding.

---

## `show cdp neighbors`

```cisco
show cdp neighbors
```

Shows directly connected Cisco neighbors discovered through CDP.

---

## `show lldp neighbors`

```cisco
show lldp neighbors
```

Shows neighbors discovered through LLDP when enabled.

---

# 19. Basic Device Configuration Concepts ⚙️

Before configuring a Cisco device, understand the major IOS modes.

```text
Switch>
   ↓
Switch#
   ↓
Switch(config)#
   ↓
Switch(config-if)#
```

### User EXEC

```text
Switch>
```

Basic monitoring access.

### Privileged EXEC

```text
Switch#
```

Use commands such as:

```cisco
show running-config
show interfaces status
show mac address-table
```

### Global configuration

```text
Switch(config)#
```

Used to configure the device globally.

### Interface configuration

```text
Switch(config-if)#
```

Used to configure a specific interface.

> You will learn Cisco IOS modes and configuration in much greater depth in the switching and VLAN modules.

---

# 20. Mini Lab — Explore a Switch 🧪

## Objective

Understand what a switch can tell you about connected devices.

## Topology

```text
PC1 ───┐
PC2 ───┼── [SW1]
PC3 ───┘
```

## Tasks

On the switch, run:

```cisco
show interfaces status
show mac address-table
show interfaces
```

### Questions

1. Which ports are active?
2. Which MAC addresses were learned?
3. Which interface learned each MAC address?
4. What happens to the MAC table when a PC is disconnected?

---

# 21. Mini Lab — Explore a Router 🧪

## Topology

```text
[PC1] ─── [SW1] ─── [R1]
                         |
                      [Network B]
```

Run:

```cisco
show ip interface brief
show ip route
show arp
```

### Questions

1. Which router interfaces are up?
2. Which networks appear in the routing table?
3. Which IP-to-MAC relationships are present in ARP?

---

# 22. Layer 2 vs Layer 3 — The Core Idea 🧠

A very useful beginner distinction is:

```text
Layer 2
  ↓
MAC address
Ethernet frame
Switching

Layer 3
  ↓
IP address
IP packet
Routing
```

### Example

PC1 wants to communicate with another host on the same subnet:

```text
PC1 → Ethernet switch → Destination host
```

For a remote IP network:

```text
PC1 → Switch → Default Gateway/Router → Remote Network
```

This distinction will become extremely important when you study:

- VLANs
- Trunking
- Inter-VLAN routing
- Static routing
- OSPF
- ACLs
- NAT

---

# 23. Common Beginner Mistakes ❌

### Mistake 1: “A switch and router do the same thing.”

They can overlap on advanced platforms, but their primary roles are different: switching is primarily Layer 2 forwarding, while routing is Layer 3 forwarding.

### Mistake 2: “A hub learns MAC addresses.”

A traditional hub does not learn MAC addresses for forwarding decisions.

### Mistake 3: “Every wireless router is only a router.”

Consumer devices often combine router, switch, AP, DHCP, NAT, and firewall functions.

### Mistake 4: “The default gateway is the switch port.”

A host's default gateway is an IP address belonging to a Layer 3 device/interface on its local network.

### Mistake 5: Confusing MAC and IP addresses

MAC addresses are used primarily for local Layer 2 delivery. IP addresses provide Layer 3 addressing across networks.

### Mistake 6: Assuming every interface uses the same naming format

Cisco interface names vary by platform. Always verify the actual interfaces available.

---

# 24. Troubleshooting by Device 🔧

## PC/NIC

Check:

- Cable or Wi-Fi connection
- Adapter enabled
- IP configuration
- Default gateway

Useful commands:

```powershell
ipconfig /all
ping 127.0.0.1
ping <gateway-ip>
```

## Switch

Check:

```cisco
show interfaces status
show mac address-table
show interfaces
```

Look for:

- Down interfaces
- Incorrect VLAN assignment
- Interface errors
- Unexpected MAC learning

## Router

Check:

```cisco
show ip interface brief
show ip route
show arp
```

Look for:

- Interfaces down
- Missing routes
- Incorrect IP addressing
- ARP issues

## Firewall

Check:

- Security policies
- NAT rules
- Interfaces/zones
- Logs
- Routing

> The exact commands depend on the firewall vendor and platform.

---

# 25. Real-World Scenario 🏫

A college has:

- 500 students
- 80 faculty members
- 4 computer labs
- Campus Wi-Fi
- A server room
- Internet connectivity

A possible architecture is:

```text
                    Internet
                       |
                 [Firewall/Router]
                       |
                  [Core Switches]
                  /     |      \
              [Lab]   [APs]   [Servers]
                |
           Access Switches
                |
               PCs
```

### Why use different devices?

- **Access switches** connect wired endpoints.
- **APs** provide wireless connectivity.
- **Core/L3 switches or routers** connect networks.
- **Firewall** enforces security policy.
- **Servers** provide services.

---

# 26. Scenario Challenge 🧠

A company reports:

> “All computers connected to Switch A lost network access, but computers on Switch B are still working.”

### Questions

1. Which device would you investigate first?
2. Which commands would you run on Switch A?
3. Could the problem be a failed uplink?
4. Could the switch itself have failed?
5. If local communication works but Internet access fails, what Layer 3 device should you investigate?

### Think first — possible approach

Start with the affected switch and inspect interface status, uplinks, MAC learning, and logs where available.

Useful starting commands include:

```cisco
show interfaces status
show interfaces
show mac address-table
show cdp neighbors
show lldp neighbors
```

Then follow the path toward the router/firewall and verify Layer 3 connectivity.

---

# 27. Practice Exercises ✍️

### Exercise 1 — Identify the device

Match the requirement to the device:

1. Connect 24 PCs inside a LAN.
2. Connect two different IP networks.
3. Provide Wi-Fi access to laptops.
4. Enforce security policies between networks.
5. Provide DNS service.
6. Provide a physical network interface to a laptop.

### Exercise 2 — Compare

Explain:

- Hub vs switch
- Switch vs router
- Router vs firewall
- AP vs wireless router
- MAC table vs routing table

### Exercise 3 — Packet Tracer

Build:

```text
PC1 ── SW1 ── R1 ── SW2 ── PC2
```

Configure suitable IP addressing and verify:

```cisco
show interfaces status
show mac address-table
show ip interface brief
show ip route
```

Document what each command tells you.

---

# 28. Interview Questions 🎤

## Beginner

1. What is a network device?
2. What is a NIC?
3. What is a hub?
4. What is a switch?
5. What is a router?
6. What is an access point?
7. What is a firewall?
8. What is a server?

## Intermediate

9. How does a switch forward a frame?
10. What is a MAC address table?
11. What is a routing table?
12. What is a default gateway?
13. What is the difference between a hub and a switch?
14. What is the difference between a Layer 2 switch and a Layer 3 switch?
15. Why does a router separate IP networks?

## Advanced

16. Can a switch perform routing?
17. Why would an enterprise use a multilayer switch?
18. What information does `show ip route` provide?
19. How would you identify a directly connected Cisco neighbor?
20. How can redundant Layer 2 links affect switches?

## Troubleshooting

21. A switch port is down. What would you check?
22. A PC can reach other local PCs but cannot reach the Internet. What should you investigate?
23. A router interface is administratively down. What does that mean?
24. A switch does not learn a MAC address. What could cause this?
25. Two devices have correct IP addresses but cannot communicate. What Layer 2 and Layer 3 checks would you perform?

---

# 29. Quick Revision ⚡

```text
NIC       → gives a host network connectivity
Repeater  → regenerates physical signal
Hub       → repeats traffic; Layer 1
Bridge    → connects LAN segments; Layer 2
Switch    → forwards frames using MAC addresses
Router    → forwards packets between IP networks
AP        → provides wireless LAN access
Firewall  → enforces security policy
Modem/ONT  → interfaces with ISP access technology
Server    → provides network services
```

### Remember

```text
MAC  → Layer 2 → Frame → Switching
IP   → Layer 3 → Packet → Routing
```

---

# 30. Command Cheat Sheet 📌

| Command | Main purpose |
|---|---|
| `show ip interface brief` | Quick interface/IP/status view |
| `show interfaces` | Detailed interface information |
| `show interfaces status` | Switch port summary |
| `show mac address-table` | View learned MAC addresses |
| `show arp` | View ARP information |
| `show ip route` | View routing table |
| `show cdp neighbors` | View Cisco neighbors |
| `show lldp neighbors` | View LLDP neighbors |
| `show running-config` | View active configuration |

---

# 31. Connection to the Next Topic 🔗

You now know **what the major network devices do**.

Next, we need to understand how information actually moves through these devices.

➡️ **05 — Network Cables and Transmission Media**

You will learn about:

- Copper cables
- Fiber-optic cables
- UTP/STP
- Straight-through and crossover concepts
- Connectors
- Ethernet standards
- Speed and duplex
- Wireless media
- Cable troubleshooting

This will connect directly to your Packet Tracer and real-world networking labs.

---

# ✅ Completion Checklist

- [ ] I understand what a NIC does.
- [ ] I understand repeater and hub concepts.
- [ ] I understand why a switch is mainly a Layer 2 device.
- [ ] I understand why a router connects IP networks.
- [ ] I understand the purpose of an access point.
- [ ] I understand the purpose of a firewall.
- [ ] I understand what a server provides.
- [ ] I understand default gateway at a basic level.
- [ ] I can distinguish MAC tables from routing tables.
- [ ] I can use basic Cisco interface verification commands.
- [ ] I can use `show mac address-table`.
- [ ] I can use `show ip route`.
- [ ] I completed the Packet Tracer exercises.
- [ ] I can explain Layer 2 vs Layer 3 in simple terms.

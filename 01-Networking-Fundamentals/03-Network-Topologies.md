# 🌐 Network Topologies

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what a network topology is.
- Differentiate between **physical** and **logical** topology.
- Identify common topology types.
- Compare the advantages and disadvantages of each topology.
- Understand why modern Ethernet networks commonly use a **star / hierarchical star** design.
- Understand where redundancy and mesh designs are useful.
- Use Cisco and endpoint commands to help discover and verify network structure.
- Troubleshoot basic topology-related problems.

---

## 1. What Is Network Topology?

**Network topology** describes how devices and connections are arranged in a network.

Think about a city:

- 🏠 Houses = end devices
- 🛣️ Roads = network links
- 🚦 Intersections = network devices
- 🗺️ Road layout = topology

The same devices can be connected in different ways. The way those connections are arranged affects **performance, reliability, scalability, cost, and troubleshooting**.

### Simple definition

> **Topology = the arrangement of devices and communication links in a network.**

---

## 2. Physical vs Logical Topology

There are two important ways to look at a network.

| Type | Meaning | Example |
|---|---|---|
| **Physical topology** | How devices and cables are physically connected | PCs connected to a central switch |
| **Logical topology** | How data logically flows between devices | Traffic moving between VLANs through a router |

### Example

A modern office may physically look like a **star** because every PC connects to a switch.

Logically, however, traffic may follow different paths depending on VLANs, routing, STP, and other network functions.

> **Remember:** Physical = *what the network looks like*. Logical = *how communication behaves or flows*.

---

## 3. Why Does Topology Matter?

Topology influences:

- 🔌 Cable requirements
- 💰 Cost
- 🚀 Performance
- 🛡️ Fault tolerance
- 📈 Scalability
- 🔧 Troubleshooting difficulty
- 🔄 Redundancy
- 📡 Traffic flow

For example, if 20 computers all depend on one central switch, that switch becomes an important point of failure. A redundant design can reduce this risk.

---

# 4. Common Network Topologies

## 4.1 Point-to-Point Topology

Two devices are connected directly to each other.

```text
[Router A] ───────── [Router B]
```

### Examples

- Router-to-router WAN connection
- Direct switch-to-switch link
- Dedicated device connection

### Advantages

- Simple
- Easy to understand
- Dedicated connection
- Easy to troubleshoot

### Disadvantages

- Connects only two endpoints
- Scaling to many devices requires many individual links

---

## 4.2 Bus Topology

All devices share one main communication cable.

```text
PC1    PC2    PC3    PC4
 |      |      |      |
 =======================
       Backbone
```

### Advantages

- Simple concept
- Historically inexpensive
- Requires less cable than some alternatives

### Disadvantages

- Backbone failure can affect the entire network
- Shared medium can create performance problems
- Difficult to troubleshoot as the network grows
- Poor scalability

> ⚠️ **CCNA note:** Traditional bus Ethernet is largely a legacy concept in modern switched LANs. You should understand it for networking fundamentals and exam questions, but modern enterprise Ethernet is generally not built this way.

---

## 4.3 Star Topology ⭐

All end devices connect to a central device, normally a switch in a modern LAN.

```text
             [PC1]
                |
                |
[PC2] ───── [SWITCH] ───── [PC3]
                |
                |
             [PC4]
```

### Advantages

- Easy to install
- Easy to expand
- Failure of one endpoint cable normally affects only that endpoint
- Easy to troubleshoot
- Excellent fit for switched Ethernet

### Disadvantage

- Failure of the central switch can affect all connected devices

### Real-world example

A college computer lab may have 30 PCs connected individually to access switches.

---

## 4.4 Ring Topology

Each device connects to two neighboring devices, forming a ring.

```text
      [PC1] ───── [PC2]
        |             |
        |             |
      [PC4] ───── [PC3]
```

### Advantages

- Predictable path structure
- Historically useful for some network technologies

### Disadvantages

- A link/device failure can affect communication depending on the implementation
- Changes can be more difficult
- Less common for ordinary modern Ethernet LAN access

> **CCNA note:** Ring topology is important conceptually and historically, but typical modern switched Ethernet access networks use star or hierarchical-star designs instead.

---

## 4.5 Mesh Topology

Devices have multiple connections to other devices.

### Full Mesh

Every device has a direct connection to every other device.

```text
        [A]
       / | \
      /  |  \
    [B]--|--[C]
      \  |  /
       \ | /
        [D]
```

### Advantages

- High redundancy
- Multiple possible paths
- Excellent fault tolerance

### Disadvantages

- Expensive
- Requires many links/interfaces
- Complex to manage as the number of devices increases

### Real-world use

Full mesh is more practical for a small number of critical devices than for hundreds of ordinary endpoints.

---

## 4.6 Partial Mesh

Only important devices have multiple redundant connections.

```text
       [Router A]
       /        \
      /          \
[Router B] ───── [Router C]
                    |
                 [Router D]
```

This gives a balance between **redundancy and cost**.

### Real-world example

An enterprise may give its core routers multiple paths while ordinary access switches have fewer links.

---

## 4.7 Tree / Hierarchical Topology 🌳

Devices are arranged in layers, creating a hierarchy.

A traditional enterprise LAN may be described using:

```text
                 [Core]
                /      \
           [Distribution] [Distribution]
             /    \          /    \
          [SW]   [SW]      [SW]   [SW]
          / \     / \      / \     / \
        PCs PCs  PCs PCs  PCs PCs  PCs PCs
```

Common layers are:

- **Access** — connects end devices
- **Distribution** — provides policy, aggregation, and Layer 3 functions in traditional designs
- **Core** — provides fast, highly available connectivity between major network sections

> Modern Cisco designs can use different architectures, but the hierarchical model is still an important CCNA concept.

---

## 4.8 Hybrid Topology

A hybrid topology combines two or more topology types.

For example:

```text
        Core
       /    \
   Star     Star
   / | \    / | \
  PC PC PC  PC PC PC
```

An enterprise network is often effectively a **hybrid** because different sections may use different physical and logical designs.

---

## 4.9 Spine-Leaf Topology

Spine-leaf is common in modern data-center networks.

```text
        [Spine1] ───── [Spine2]
         / | \          / | \
        /  |  \        /  |  \
   [Leaf1][Leaf2][Leaf3][Leaf1][Leaf2][Leaf3]
```

The basic idea is:

- **Spine switches** provide the backbone.
- **Leaf switches** connect servers, services, or other endpoints.
- Leaf switches typically connect to multiple spine switches.

This creates predictable connectivity and supports scalable east-west traffic.

> **CCNA note:** Know the concept, but focus first on traditional LAN topology, switching, VLANs, trunks, STP, and routing fundamentals.

---

# 5. Topology Comparison

| Topology | Main idea | Main strength | Main weakness | Modern relevance |
|---|---|---|---|---|
| Point-to-point | One link between two devices | Simple | Limited scale | ⭐⭐⭐⭐⭐ |
| Bus | Shared backbone | Simple/low historical cost | Backbone failure, shared medium | ⭐ |
| Star | Devices connect to a center | Easy management | Central-device dependency | ⭐⭐⭐⭐⭐ |
| Ring | Devices form a loop | Predictable structure | Failure/change concerns | ⭐⭐ |
| Full mesh | Everyone connects to everyone | Maximum redundancy | Very expensive/complex | ⭐⭐ |
| Partial mesh | Selected redundant paths | Balance of cost and redundancy | More complex than star | ⭐⭐⭐⭐ |
| Tree/hierarchical | Layered structure | Scalable | Design complexity | ⭐⭐⭐⭐⭐ |
| Hybrid | Combination | Flexible | Can be complex | ⭐⭐⭐⭐⭐ |
| Spine-leaf | Leaf switches connect to spines | Scalable, predictable | Requires appropriate design | ⭐⭐⭐⭐ |

---

# 6. Topology and Redundancy 🔄

Redundancy means providing an alternative path or component if the primary one fails.

Example:

```text
             [Core A]
            /        \
      [Access 1]   [Access 2]
            \        /
             [Core B]
```

If one path fails, another path may still exist.

However, adding physical links can create **Layer 2 loops** in switched Ethernet.

This is where technologies such as **Spanning Tree Protocol (STP)** become important.

We will study STP in detail later in the CCNA path.

---

# 7. Topology and Failure Domains

Topology helps you understand what happens when a component fails.

### Example: Star

```text
PC1 ─┐
PC2 ─┤
PC3 ─┼── [SWITCH]
PC4 ─┤
PC5 ─┘
```

If PC3's cable fails:

➡️ PC3 is affected.

If the central switch fails:

➡️ All directly connected PCs may lose network connectivity.

This is why important switches are often designed with redundancy in enterprise networks.

---

# 8. Choosing a Topology

Network engineers consider:

### 1. Cost 💰
How much will equipment, interfaces, cables, and maintenance cost?

### 2. Scalability 📈
Can new users and devices be added easily?

### 3. Availability 🛡️
What happens when a link or device fails?

### 4. Performance 🚀
Can the topology handle the expected traffic?

### 5. Manageability 🔧
Can engineers easily monitor and troubleshoot it?

### 6. Physical environment 🏢
Does the building, campus, or data center support the design?

### 7. Security 🔐
Does the design support segmentation, access control, and monitoring?

---

# 9. How Cisco Commands Help You Discover a Topology

Cisco IOS does not have one command called `show topology` that automatically explains the entire network.

Instead, engineers combine multiple commands.

## 9.1 `show cdp neighbors`

```cisco
show cdp neighbors
```

Shows directly connected Cisco devices discovered through **CDP (Cisco Discovery Protocol)**.

Useful for answering:

> “What Cisco device is connected to this interface?”

---

## 9.2 `show cdp neighbors detail`

```cisco
show cdp neighbors detail
```

Provides more information about discovered Cisco neighbors, such as device identity, IP information when advertised, platform, and interface details.

---

## 9.3 `show lldp neighbors`

```cisco
show lldp neighbors
```

Shows neighbors discovered through **LLDP (Link Layer Discovery Protocol)** when LLDP is enabled.

LLDP is vendor-neutral, while CDP is Cisco proprietary.

---

## 9.4 `show lldp neighbors detail`

```cisco
show lldp neighbors detail
```

Provides detailed LLDP neighbor information.

---

## 9.5 `show interfaces status`

```cisco
show interfaces status
```

Useful for quickly checking:

- Port status
- VLAN assignment
- Duplex
- Speed
- Port type

---

## 9.6 `show ip interface brief`

```cisco
show ip interface brief
```

Provides a quick summary of interfaces and their IP addresses/status.

Example:

```text
Interface              IP-Address      Status       Protocol
GigabitEthernet0/0     192.168.1.1     up           up
GigabitEthernet0/1     unassigned      up           up
```

---

## 9.7 `show spanning-tree`

```cisco
show spanning-tree
```

This becomes especially useful when the topology contains redundant Layer 2 links.

It can help you understand:

- Root bridge
- Port roles
- Forwarding/blocking states
- Layer 2 topology decisions

> STP itself is covered later in this repository.

---

# 10. Endpoint Commands That Help Understand a Path

## Windows

### `tracert`

```powershell
tracert 8.8.8.8
```

Shows Layer 3 hops between your computer and a destination.

### `arp -a`

```powershell
arp -a
```

Displays the local ARP cache and helps relate local IPv4 addresses to MAC addresses.

---

## Linux

### `ip route`

```bash
ip route
```

Shows the host's routing table.

### `ip neigh`

```bash
ip neigh
```

Shows neighbor information such as IP-to-MAC relationships.

### `traceroute`

```bash
traceroute 8.8.8.8
```

Shows Layer 3 hops toward a destination when the utility is installed and permitted.

---

# 11. Practical Lab — Build a Star Topology in Packet Tracer 🧪

## Objective

Build a basic star topology using one switch and four PCs.

## Topology

```text
              PC1
               |
               |
PC2 ─────── [SW1] ─────── PC3
               |
               |
              PC4
```

## Requirements

- 1 Cisco switch
- 4 PCs
- Copper straight-through cables
- Packet Tracer

## Suggested addressing

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC1 | 192.168.1.11 | 255.255.255.0 |
| PC2 | 192.168.1.12 | 255.255.255.0 |
| PC3 | 192.168.1.13 | 255.255.255.0 |
| PC4 | 192.168.1.14 | 255.255.255.0 |

No default gateway is required for communication within this same subnet.

## Tasks

1. Place the switch and four PCs.
2. Connect every PC to the switch.
3. Configure the IP addresses.
4. Wait for interfaces to become active.
5. Ping PC1 from PC2.
6. Ping PC4 from PC3.
7. Disconnect PC3's cable.
8. Verify whether PC1, PC2, and PC4 can still communicate.

### Expected result

Disconnecting PC3 should normally affect PC3 only. The other PCs should continue communicating through the switch.

---

# 12. Practical Lab — Observe Redundancy

Create two switches and connect them with **two physical links**.

```text
        ┌───────────────┐
        │               │
     [SW1]=========== [SW2]
        │               │
       PC1             PC2
```

> ⚠️ Do not assume that two parallel Layer 2 links will both forward traffic normally. A Layer 2 loop can occur, so STP may place one link into a non-forwarding state.

### Observe with

```cisco
show spanning-tree
```

This is an important bridge between **topology** and the later **STP** module.

---

# 13. Troubleshooting Topology Problems 🔧

When a device cannot communicate, ask:

### Step 1 — Is the physical connection correct?

Check:

- Cable
- Interface
- Link light/status
- Correct port

### Step 2 — Is the interface up?

```cisco
show ip interface brief
```

or:

```cisco
show interfaces status
```

### Step 3 — What is connected to this port?

```cisco
show cdp neighbors
```

or:

```cisco
show lldp neighbors
```

### Step 4 — Is the addressing correct?

Check:

- IP address
- Subnet mask
- Default gateway when required

### Step 5 — Is there a Layer 2 loop or STP issue?

```cisco
show spanning-tree
```

### Step 6 — Test connectivity

```text
PC → local gateway → remote gateway → destination
```

This layered troubleshooting approach prevents random configuration changes.

---

# 14. Common Beginner Mistakes ❌

### Mistake 1: Thinking topology means only cable shape

Topology also includes logical connectivity and traffic behavior.

### Mistake 2: Assuming mesh is always better

More links provide redundancy, but also increase cost and complexity.

### Mistake 3: Assuming one disconnected PC means the whole star network failed

In a star topology, one endpoint link normally affects only that endpoint.

### Mistake 4: Ignoring redundancy loops

Adding extra Layer 2 links without understanding STP can create loops.

### Mistake 5: Confusing topology with network type

**LAN/WAN** describes the scope/type of a network.  
**Star/mesh/ring** describes how devices are arranged.

### Mistake 6: Treating legacy topologies as common modern LAN designs

Bus and traditional ring concepts are useful for learning, but modern switched Ethernet commonly uses star/hierarchical designs.

---

# 15. Real-World Scenario 🏢

## Small Company

A company has:

- 40 employees
- 2 network switches
- 1 router/firewall
- Wi-Fi access points
- A server

A reasonable high-level design might look like:

```text
                 Internet
                    |
             [Router/Firewall]
                    |
                 [Core/SW]
                 /       \
             [SW1]      [SW2]
             /  \        /  \
           PCs  APs     PCs  APs
                    |
                 [Server]
```

This is not simply “one topology.” It combines hierarchical/star connectivity, wireless access, and potentially redundant links.

---

# 16. Scenario Challenge 🧠

### Problem

A bank has:

- 2 core switches
- 6 access switches
- 100 employee PCs
- 20 IP phones
- 10 wireless access points

Management says:

> “We want the network to continue working even if one important link fails.”

### Questions

1. Would a simple single-switch star design be enough?
2. Where would you consider redundant links?
3. What risk can redundant Layer 2 links introduce?
4. Which Cisco technology will help prevent Layer 2 loops?
5. Which command can help you inspect the resulting Layer 2 topology?

### Think first — solution

A strong design would use appropriate redundancy around important infrastructure rather than connecting every endpoint to every other device. Redundant Layer 2 paths introduce loop risk, so **STP** is important. `show spanning-tree` can be used to inspect STP's view of the topology.

---

# 17. Practice Exercises ✍️

### Exercise 1 — Identify the topology

Identify the likely topology:

1. Four PCs connected to one switch.
2. Two routers connected directly.
3. Every router has a direct link to every other router.
4. Devices connected in a closed loop.

### Exercise 2 — Compare

Explain the difference between:

- Star vs mesh
- Physical vs logical topology
- LAN vs star topology
- Full mesh vs partial mesh

### Exercise 3 — Packet Tracer

Build:

- One star topology
- One redundant two-switch topology

For each, document:

- Devices
- Interfaces
- Links
- IP addressing
- Verification commands
- What happens when a link fails

---

# 18. Interview Questions 🎤

## Beginner

1. What is network topology?
2. What is a star topology?
3. What is a physical topology?
4. What is a logical topology?
5. What is a point-to-point connection?

## Intermediate

6. Why is star topology common in modern Ethernet?
7. What are the disadvantages of full mesh?
8. What is partial mesh?
9. What is a hierarchical network topology?
10. Why are bus and ring topologies less common in modern Ethernet LANs?

## Advanced

11. How can redundant Layer 2 links create a loop?
12. How does STP interact with redundant topology?
13. Why can a hierarchical topology scale better than a flat topology?
14. When would partial mesh be preferable to full mesh?
15. Why is topology design important for high availability?

## Troubleshooting

16. A PC loses connectivity after its cable is disconnected. What does that tell you about the likely topology?
17. Two switches have two physical links between them. What should you investigate?
18. Which commands can help identify directly connected Cisco devices?
19. How would you discover whether a switch port has an active neighbor?
20. How would you investigate a suspected Layer 2 loop?

---

# 19. Quick Revision ⚡

```text
Network Topology
│
├── Physical → physical arrangement
│
├── Logical → logical/data-flow behavior
│
├── Point-to-Point → two devices
│
├── Bus → shared backbone (legacy)
│
├── Star → central switch/device
│
├── Ring → closed loop
│
├── Full Mesh → every device connected to every other
│
├── Partial Mesh → selected redundant connections
│
├── Tree/Hierarchical → layered design
│
├── Hybrid → combination of designs
│
└── Spine-Leaf → modern scalable data-center design
```

### Key commands

```cisco
show cdp neighbors
show cdp neighbors detail
show lldp neighbors
show lldp neighbors detail
show interfaces status
show ip interface brief
show spanning-tree
```

---

# 20. Cheat Sheet 📌

| Question | Answer |
|---|---|
| What is topology? | Arrangement of network devices/links |
| Physical topology? | Physical connection arrangement |
| Logical topology? | Logical communication/data flow |
| Most common modern LAN pattern? | Star / hierarchical star |
| Highest redundancy? | Full mesh |
| More practical redundant mesh? | Partial mesh |
| Legacy shared-medium topology? | Bus |
| Topology with a closed loop? | Ring |
| Layered enterprise design? | Hierarchical/tree |
| Data-center design often using spine and leaf? | Spine-leaf |
| Cisco neighbor discovery? | CDP / LLDP |
| Inspect STP topology? | `show spanning-tree` |

---

# 21. Connection to the Next Topic 🔗

Now you know:

**What a network is → Types of networks → How devices are arranged.**

Next, we need to learn about the actual hardware that creates these connections:

➡️ **04 — Network Devices**

You will learn about:

- NIC
- Hub
- Switch
- Router
- Wireless Access Point
- Modem
- Firewall
- Server
- Network interfaces and ports
- Layer 2 vs Layer 3 devices

Understanding devices will make later topics such as **switching, VLANs, trunking, routing, ACLs, NAT, and troubleshooting** much easier.

---

# ✅ Completion Checklist

- [ ] I understand what network topology means.
- [ ] I can explain physical vs logical topology.
- [ ] I can identify common topology types.
- [ ] I understand star topology.
- [ ] I understand full and partial mesh.
- [ ] I understand hierarchical topology.
- [ ] I know why bus/ring are mostly legacy concepts for modern Ethernet LANs.
- [ ] I understand why redundancy is important.
- [ ] I understand why redundant Layer 2 links can create loops.
- [ ] I can use CDP/LLDP to inspect neighbors.
- [ ] I can use `show spanning-tree` at a basic level.
- [ ] I completed the Packet Tracer exercises.
- [ ] I can explain the difference between topology and network type.

---

## 📚 Navigation

⬅️ Previous: [02 — Types of Networks](./02-Types-of-Networks.md)  
➡️ Next: [04 — Network Devices](./04-Network-Devices.md)  
📘 Module: [Networking Fundamentals](./README.md)

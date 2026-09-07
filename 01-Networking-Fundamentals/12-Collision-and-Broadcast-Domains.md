# 12. Collision Domains and Broadcast Domains

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

---

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what a **collision domain** is.
- Explain what a **broadcast domain** is.
- Understand the difference between collisions and broadcasts.
- Explain how hubs, switches, routers, and VLANs affect each domain.
- Understand why modern switched Ethernet largely eliminates traditional Ethernet collisions.
- Predict the number of collision and broadcast domains in common network topologies.
- Verify Layer 2 and Layer 3 behavior with Cisco IOS commands.
- Troubleshoot excessive broadcasts, Layer 2 loops, and connectivity problems.

---

## 1. Why Do These Domains Matter?

When devices communicate, two important questions are:

1. **How many devices share the same area where an Ethernet collision could occur?**
2. **How many devices receive a Layer 2 broadcast?**

These are different concepts.

A useful mental model:

> **Collision domain = who shares the same Ethernet collision space?**  
> **Broadcast domain = who receives the same Layer 2 broadcast?**

Understanding these concepts helps you understand why switches, routers, and VLANs are designed the way they are.

---

# 2. What Is a Collision?

A **collision** occurs when two Ethernet devices attempt to transmit on the same shared medium at the same time and their signals interfere with each other.

### Simple analogy 📢

Imagine five people sharing one small walkie-talkie channel where everyone can transmit at once.

If two people speak simultaneously, neither message is properly understood.

That shared communication space is similar to a traditional Ethernet collision domain.

---

# 3. Collision Domain

A **collision domain** is a portion of a network in which Ethernet frames could potentially collide when devices share the same transmission medium.

Historically, collision domains were especially important with:

- Shared coaxial Ethernet
- Ethernet hubs
- Half-duplex Ethernet

With modern switched Ethernet using **full-duplex** links, collisions normally do not occur on the link.

---

# 4. Hub and Collision Domains

A hub operates at **Layer 1**.

It does not make forwarding decisions based on MAC addresses. When a signal enters one port, the hub repeats it out the other ports.

```text
             HUB
        ┌──────┼──────┐
        │      │      │
       PC1    PC2    PC3
```

All connected devices share the same physical medium from a collision perspective.

### Result

**One hub = one shared collision domain.**

If PC1 and PC2 transmit simultaneously, a collision can occur in a traditional half-duplex Ethernet environment.

---

# 5. Switch and Collision Domains

A switch operates primarily at **Layer 2** and creates separate Ethernet segments per switch port.

```text
PC1 ───┐
       │
      SW1
       │
PC2 ───┘
```

PC1 and PC2 are connected through separate switch ports.

Conceptually:

```text
PC1 ──[Port 1] SW1 [Port 2]── PC2
        ↑              ↑
   Collision       Collision
     domain          domain
```

### Important rule

> **Each switch port represents a separate collision domain.**

For example, a 24-port switch with 24 active device links has **24 separate collision domains** under the traditional CCNA counting model.

However, if two switch ports are connected by one Ethernet link, that link itself is one collision domain on each side of the connection; with full-duplex operation, actual collisions are not expected.

---

# 6. Full Duplex and Modern Ethernet

Modern switched Ethernet normally uses **full-duplex** communication.

Full duplex means:

- Device can transmit and receive simultaneously.
- The link is point-to-point.
- Traditional Ethernet collisions do not occur.
- CSMA/CD is not needed for that link.

```text
PC1 ⇄────────────⇄ SW1
      full duplex
```

### Remember

**Half duplex → collisions possible**  
**Full duplex → collisions are not expected**

This is why seeing a duplex mismatch can cause serious performance problems.

---

# 7. CSMA/CD — Historical Concept

**CSMA/CD** means:

> Carrier Sense Multiple Access with Collision Detection

It was used by traditional shared, half-duplex Ethernet.

Basic idea:

1. Listen before transmitting.
2. If the medium appears free, transmit.
3. Detect a collision if another transmission occurs.
4. Stop transmitting.
5. Wait using a backoff mechanism.
6. Try again.

### Modern relevance

You should understand CSMA/CD for CCNA concepts and historical Ethernet behavior, but it is **not used to manage collisions on normal full-duplex switched Ethernet links**.

---

# 8. What Is a Broadcast?

A **broadcast** is traffic intended for every device in the local Layer 2 broadcast domain.

For IPv4, the Ethernet broadcast MAC address is:

```text
FF:FF:FF:FF:FF:FF
```

A common IPv4 example is a DHCP client discovering a DHCP server.

```text
Client
  │
  │ DHCP Discover
  │ Destination MAC = FF:FF:FF:FF:FF:FF
  ▼
Switch
  │
  ├── PC2
  ├── PC3
  ├── PC4
  └── DHCP Server
```

All devices in that Layer 2 broadcast domain receive the broadcast, although only the appropriate device may process it.

---

# 9. Broadcast Domain

A **broadcast domain** is the set of Layer 2 devices that receive a given Layer 2 broadcast.

A Layer 2 broadcast normally remains within its local broadcast domain.

### Key rule

> **Routers separate broadcast domains.**

A router does not normally forward a Layer 2 broadcast from one interface into another Layer 3 network.

---

# 10. Switches and Broadcast Domains

A basic Layer 2 switch forwards/floods broadcasts within the same VLAN.

```text
             SW1
       ┌──────┼──────┐
       │      │      │
      PC1    PC2    PC3
```

If all ports belong to the same VLAN:

```text
       ONE BROADCAST DOMAIN
       ┌──────────────────┐
       │ PC1 PC2 PC3 SW1  │
       └──────────────────┘
```

So:

- Switch separates collision domains.
- A basic switch does **not** automatically separate broadcast domains.

---

# 11. Router and Broadcast Domains

Routers operate at Layer 3.

Consider:

```text
LAN A                         LAN B
PC1 ── SW1 ── R1 ───────── SW2 ── PC2
```

There are two broadcast domains:

```text
Broadcast Domain 1       Broadcast Domain 2
┌────────────────┐       ┌────────────────┐
│ PC1 ─ SW1 ─ R1 │       │ R1 ─ SW2 ─ PC2 │
└────────────────┘       └────────────────┘
```

A broadcast generated on LAN A is not normally forwarded by R1 into LAN B.

### Important rule

> **Each routed Layer 3 interface/subnet represents a separate broadcast domain.**

---

# 12. Collision Domain vs Broadcast Domain

| Feature | Collision Domain | Broadcast Domain |
|---|---|---|
| Main concern | Ethernet collisions | Layer 2 broadcasts |
| Associated with | Shared Ethernet media | Layer 2 segments/VLANs |
| Hub effect | One shared collision domain | Usually one broadcast domain |
| Switch effect | Separates collision domains per port | Keeps same VLAN in same broadcast domain |
| Router effect | Separates physical/routed segments | Separates broadcast domains |
| VLAN effect | Switch ports remain separate collision domains | Each VLAN is a separate broadcast domain |
| Modern full duplex | Collisions normally absent | Broadcasts still exist |

---

# 13. The Most Important Counting Rules 🧠

For CCNA-style questions, remember these simplified rules:

### Hub

```text
One hub = one collision domain
```

### Switch

```text
Each switch port = separate collision domain
```

But:

```text
One VLAN = one broadcast domain
```

### Router

```text
Each router interface = separate broadcast domain
```

### VLAN

```text
Each VLAN = separate broadcast domain
```

---

# 14. Example 1 — Hub

```text
             HUB
       ┌─────┼─────┐
       │     │     │
      PC1   PC2   PC3
```

### Collision domains

**1**

### Broadcast domains

**1**

---

# 15. Example 2 — One Switch

```text
             SW1
       ┌─────┼─────┐
       │     │     │
      PC1   PC2   PC3
```

Assume all ports are in VLAN 1.

### Collision domains

**3** device-facing switch links.

### Broadcast domains

**1** VLAN.

---

# 16. Example 3 — Two Switches

```text
PC1 ── SW1 ───── SW2 ── PC2
```

Assume all ports are in the same VLAN.

### Collision domains

There are three Ethernet links:

1. PC1 ↔ SW1
2. SW1 ↔ SW2
3. SW2 ↔ PC2

So the simplified count is **3 collision domains**.

### Broadcast domains

**1**, because the same VLAN spans both switches.

---

# 17. Example 4 — Router Between Two Switches

```text
PC1 ── SW1 ── R1 ── SW2 ── PC2
```

### Collision domains

Four Ethernet links:

1. PC1 ↔ SW1
2. SW1 ↔ R1
3. R1 ↔ SW2
4. SW2 ↔ PC2

### Broadcast domains

Two:

```text
LAN A                LAN B
PC1-SW1-R1           R1-SW2-PC2
```

The router separates the broadcast domains.

---

# 18. VLANs and Broadcast Domains

VLANs logically divide a Layer 2 switch into separate broadcast domains.

```text
                 SW1
        ┌─────────┼─────────┐
        │         │         │
      VLAN 10   VLAN 20   VLAN 30
       PC1 PC2   PC3 PC4   PC5 PC6
```

Even though all devices use the same physical switch:

- VLAN 10 = one broadcast domain
- VLAN 20 = one broadcast domain
- VLAN 30 = one broadcast domain

So one physical switch can contain many broadcast domains.

---

# 19. VLAN Broadcast Example

Suppose PC1 in VLAN 10 sends a broadcast.

```text
PC1 VLAN 10
     │
     ▼
    SW1
   /   \
 VLAN10 VLAN20
  PC2    PC3
```

PC2 in VLAN 10 can receive the broadcast.

PC3 in VLAN 20 does not receive that VLAN 10 broadcast.

To communicate between VLANs, Layer 3 routing is required.

---

# 20. Topology 1 — Shared Medium

```text
PC1
 │
 ├──────── HUB ──────── PC2
 │                       │
 └────────────────────── PC3
```

**Collision domains:** 1  
**Broadcast domains:** 1

---

# 21. Topology 2 — Switched LAN

```text
PC1 ──┐
      │
PC2 ──┼── SW1
      │
PC3 ──┘
```

**Collision domains:** one per active Ethernet segment/port  
**Broadcast domains:** one if all ports are in the same VLAN

---

# 22. Topology 3 — VLAN Segmentation

```text
             SW1
       ┌──────┼──────┐
       │      │      │
     VLAN10 VLAN20 VLAN30
       │      │      │
      PC1    PC2    PC3
```

**Collision domains:** still separated by switch ports  
**Broadcast domains:** 3

---

# 23. Topology 4 — Router Segmentation

```text
LAN 10                    LAN 20
PC1 ─ SW1 ── R1 ── SW2 ─ PC2
```

**Broadcast domains:** 2

The router provides the Layer 3 boundary.

---

# 24. Topology 5 — Router-on-a-Stick

```text
              R1
               │
            trunk
               │
              SW1
          ┌────┼────┐
          │    │    │
       VLAN10 VLAN20 VLAN30
```

Each VLAN is a separate broadcast domain.

The router uses logical subinterfaces to route between them.

---

# 25. Topology 6 — Multiple Switches and VLANs

```text
 PC1 ── SW1 ═════════ SW2 ── PC3
        │              │
      VLAN10          VLAN10

 PC2 ── SW1 ═════════ SW2 ── PC4
        │              │
      VLAN20          VLAN20
```

The trunk can carry multiple VLANs.

Broadcast domains:

- VLAN 10 = one broadcast domain across SW1 and SW2
- VLAN 20 = another broadcast domain across SW1 and SW2

Physical distance does not determine the broadcast domain. **VLAN membership does.**

---

# 26. Why Broadcasts Can Become a Problem 🚨

Broadcasts are normal and necessary.

Examples include:

- ARP requests in IPv4
- DHCP discovery
- Some discovery protocols

The problem occurs when broadcasts become excessive.

Possible effects:

- Higher CPU utilization
- Increased bandwidth consumption
- Slower network performance
- Unstable switches
- Poor application performance

A serious Layer 2 loop can create a **broadcast storm**.

---

# 27. Broadcast Storm

A broadcast storm occurs when an excessive amount of broadcast traffic circulates through a Layer 2 network.

A common cause is a Layer 2 loop.

```text
        SW1
       /   \
      /     \
    SW2 ─── SW3
```

If Layer 2 forwarding loops exist without proper loop prevention, broadcast frames can circulate repeatedly.

This is one reason **Spanning Tree Protocol (STP)** is important.

> STP is covered in detail later in the Switching section.

---

# 28. Broadcast Domain vs Subnet

At the CCNA level, an IPv4 subnet and a Layer 2 broadcast domain are commonly associated in a typical design.

For example:

```text
VLAN 10
10.10.10.0/24
```

But do not treat the terms as identical in every possible design.

- **VLAN** is a Layer 2 concept.
- **Broadcast domain** is a Layer 2 forwarding boundary.
- **Subnet** is a Layer 3 addressing concept.

Good network design often maps one VLAN to one IP subnet.

---

# 29. Cisco IOS Commands — Collision/Broadcast Troubleshooting

## Check interfaces

```cisco
show ip interface brief
```

Useful for:

- Interface status
- IP addresses
- Up/down state

---

## Check detailed interface statistics

```cisco
show interfaces
```

Look for:

- Input errors
- CRC errors
- Collisions
- Late collisions
- Duplex
- Speed
- Interface counters

Example filtering:

```cisco
show interfaces gigabitEthernet 0/1
```

---

## Check MAC address table

```cisco
show mac address-table
```

Useful for understanding:

- Which MAC addresses were learned
- Which VLAN they belong to
- Which switch port learned them

---

## Check VLANs

```cisco
show vlan brief
```

Useful for identifying:

- VLANs
- Access ports
- VLAN membership

---

## Check trunking

```cisco
show interfaces trunk
```

Useful for:

- Trunk ports
- Allowed VLANs
- Native VLAN information
- VLANs active on the trunk

---

## Check STP

```cisco
show spanning-tree
```

Useful when investigating:

- Layer 2 loops
- Blocked ports
- Root bridge selection
- STP state

---

## Test Layer 3 reachability

```cisco
ping 192.168.10.10
```

Remember: ping tests IP reachability, not every Layer 2 behavior.

---

# 30. Windows Commands

## IP configuration

```powershell
ipconfig /all
```

Check:

- IPv4 address
- Subnet mask
- Default gateway
- DNS
- Physical adapter information

## Test connectivity

```powershell
ping 192.168.10.1
```

## View ARP cache

```powershell
arp -a
```

## Trace Layer 3 path

```powershell
tracert 8.8.8.8
```

---

# 31. Linux Commands

## Address information

```bash
ip addr
```

## Routing table

```bash
ip route
```

## Neighbor/ARP information

```bash
ip neigh
```

## Test connectivity

```bash
ping 192.168.10.1
```

## Interface statistics

```bash
ip -s link
```

---

# 32. Practical Lab 1 — Hub Concept

> **Packet Tracer note:** Depending on the Packet Tracer version and available devices, use a hub device if available.

### Objective

Observe the behavior of a shared Layer 1 medium.

### Topology

```text
PC1 ─┐
PC2 ─┼── HUB
PC3 ─┘
```

### Tasks

1. Connect three PCs to a hub.
2. Configure IP addresses in the same subnet.
3. Test connectivity.
4. Observe the traffic in Simulation Mode.
5. Relate the shared medium to the collision-domain concept.

### Expected result

All PCs belong to the same broadcast domain and share the same collision domain conceptually.

---

# 33. Practical Lab 2 — Switch vs Hub

### Topology A

```text
PC1 ─┐
PC2 ─┼── HUB
PC3 ─┘
```

### Topology B

```text
PC1 ─┐
PC2 ─┼── SW1
PC3 ─┘
```

### Tasks

Compare:

- Device behavior
- MAC learning
- Collision-domain structure
- Broadcast behavior

### Key learning

A switch creates separate collision domains per port while still allowing broadcasts to remain within the same VLAN.

---

# 34. Practical Lab 3 — VLAN Broadcast Domains

### Topology

```text
          SW1
      ┌────┼────┐
      │    │    │
     PC1  PC2  PC3
     V10  V10  V20
```

### Example addressing

| Device | VLAN | IP Address |
|---|---:|---|
| PC1 | 10 | 192.168.10.10/24 |
| PC2 | 10 | 192.168.10.20/24 |
| PC3 | 20 | 192.168.20.10/24 |

### Tasks

1. Create VLAN 10.
2. Create VLAN 20.
3. Assign PC1 and PC2 to VLAN 10.
4. Assign PC3 to VLAN 20.
5. Test same-VLAN communication.
6. Observe what happens between VLANs without routing.

### Expected result

PC1 and PC2 share a broadcast domain.

PC3 belongs to a different broadcast domain.

---

# 35. Practical Lab 4 — Router Separating Broadcast Domains

### Topology

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ PC2
```

### Addressing

| Device | Interface | IP |
|---|---|---|
| PC1 | NIC | 192.168.10.10/24 |
| R1 | G0/0 | 192.168.10.1/24 |
| R1 | G0/1 | 192.168.20.1/24 |
| PC2 | NIC | 192.168.20.10/24 |

### Tasks

1. Configure both LANs.
2. Configure R1 interfaces.
3. Configure PC default gateways.
4. Test PC1 → PC2.
5. Use `show ip interface brief` on R1.
6. Use `ping` to verify Layer 3 reachability.

### Expected result

The router separates the two broadcast domains and provides Layer 3 communication between them.

---

# 36. Troubleshooting Methodology 🔎

When investigating a problem, separate the concepts.

### Step 1 — Check physical connectivity

```cisco
show interfaces
```

Look for:

- Link state
- Errors
- CRC errors
- Collisions
- Duplex mismatch

### Step 2 — Check VLAN membership

```cisco
show vlan brief
```

### Step 3 — Check trunking

```cisco
show interfaces trunk
```

### Step 4 — Check MAC learning

```cisco
show mac address-table
```

### Step 5 — Check STP

```cisco
show spanning-tree
```

### Step 6 — Check Layer 3

```cisco
show ip interface brief
show ip route
```

### Step 7 — Test

```cisco
ping <destination>
```

---

# 37. Troubleshooting Scenario — High Errors

### Situation

A switch port shows increasing errors and poor performance.

### What should you investigate?

- Cable quality
- Speed/duplex settings
- Interface errors
- Physical transceiver
- NIC problems
- Duplex mismatch

Useful command:

```cisco
show interfaces gigabitEthernet 0/1
```

Do not immediately assume the problem is a broadcast storm.

---

# 38. Troubleshooting Scenario — Broadcast Storm

### Situation

Users across several VLANs report severe slowness. Switch CPU utilization is high and broadcast traffic is unusually large.

### Investigation

Check:

```cisco
show spanning-tree
show interfaces
show mac address-table
```

Look for:

- Unexpected Layer 2 loops
- MAC address movement/flapping
- STP instability
- Excessive broadcasts
- Misconfigured redundant links

### Lesson

A broadcast storm is usually a **Layer 2 design/forwarding problem**, not simply a high number of users.

---

# 39. Troubleshooting Scenario — Wrong VLAN

### Situation

PC1 cannot communicate with PC2 even though both are connected to the same switch.

PC1:

```text
VLAN 10
```

PC2:

```text
VLAN 20
```

### Check

```cisco
show vlan brief
```

If they are intentionally in different VLANs, they are in different broadcast domains and require Layer 3 routing to communicate.

---

# 40. Real-World Scenario 🏢

Imagine a company with:

- 200 employees
- 4 departments
- 2 switches
- Multiple VLANs
- Redundant switch links
- Central Layer 3 routing

A sensible design could be:

```text
                 Core / L3
                 Switch/Router
                  /       \
               SW1         SW2
             /  |  \      / |  \
          V10 V20 V30   V10 V20 V30
```

The network designer wants:

- Separate broadcast domains for departments.
- Separate collision domains for switch ports.
- Redundancy without Layer 2 loops.
- Controlled routing between VLANs.

This leads naturally to topics such as:

- VLANs
- Trunking
- Inter-VLAN routing
- STP
- EtherChannel

---

# 41. Scenario Challenge 🎯

You are given:

```text
             SW1
        ┌─────┼─────┐
        │     │     │
       PC1   PC2   PC3
       V10   V10   V20
              │
             R1
```

### Questions

1. How many broadcast domains exist?
2. Which PCs share a broadcast domain?
3. Does a switch create a separate broadcast domain for every port?
4. What device/function is required for communication between VLAN 10 and VLAN 20?
5. What command can verify VLAN membership?
6. What command can help verify Layer 3 interface status?

### Try first!

Do not look up the answer immediately. Draw the boundaries yourself.

<details>
<summary>💡 Reveal the solution</summary>

- VLAN 10 = one broadcast domain.
- VLAN 20 = another broadcast domain.
- PC1 and PC2 share VLAN 10.
- A switch does not create a new broadcast domain per port when the ports are in the same VLAN.
- Layer 3 routing is required between VLAN 10 and VLAN 20.
- `show vlan brief` verifies VLAN membership.
- `show ip interface brief` verifies Layer 3 interface status.

</details>

---

# 42. Common Beginner Mistakes ❌

### Mistake 1

> “Every switch port is a broadcast domain.”

❌ Incorrect.

A switch port is a separate collision-domain segment, but ports in the same VLAN normally share one broadcast domain.

### Mistake 2

> “A router forwards broadcasts like normal traffic.”

❌ Normally incorrect for Layer 2 broadcasts.

Routers provide the boundary between broadcast domains.

### Mistake 3

> “Collision domains are still a major problem on every modern Ethernet link.”

❌ Not on normal full-duplex switched links.

### Mistake 4

> “VLANs only organize ports.”

❌ VLANs also create separate Layer 2 broadcast domains.

### Mistake 5

> “More broadcasts always mean something is broken.”

❌ Broadcasts are normal. Excessive broadcasts are the concern.

---

# 43. Practice Exercises ✍️

### Exercise 1

A hub has four connected PCs.

- How many collision domains?
- How many broadcast domains?

### Exercise 2

A switch has six active PC ports, all in VLAN 10.

- How many collision domains?
- How many broadcast domains?

### Exercise 3

A switch has VLAN 10, VLAN 20, and VLAN 30.

- How many broadcast domains?

### Exercise 4

Two switches are connected by a trunk carrying VLANs 10 and 20.

- How many broadcast domains are represented by these VLANs?

### Exercise 5

Two LANs are connected through a router.

- Does a broadcast from LAN A normally cross the router to LAN B?

### Exercise 6

A switch port reports late collisions.

- What physical/duplex conditions should you investigate?

---

# 44. Interview Questions 🎤

## Beginner

1. What is a collision domain?
2. What is a broadcast domain?
3. What device creates separate collision domains per port?
4. What device separates broadcast domains?
5. What is the IPv4 Ethernet broadcast MAC address?
6. What is the difference between a hub and a switch?
7. What is full-duplex Ethernet?

## Intermediate

8. How many collision domains does a 24-port switch have if all ports are active?
9. How many broadcast domains exist if all switch ports are in VLAN 1?
10. How do VLANs affect broadcast domains?
11. Why does a router stop Layer 2 broadcasts from normally crossing interfaces?
12. Why are collisions not normally expected on full-duplex switched Ethernet?
13. What is CSMA/CD?
14. Why can a Layer 2 loop cause a broadcast storm?

## Advanced

15. How would you troubleshoot excessive broadcast traffic?
16. What relationship exists between VLANs and IP subnets in a typical enterprise design?
17. How can redundant Layer 2 links cause loops?
18. Why is STP required in many redundant Layer 2 designs?
19. What does MAC address flapping suggest?
20. How can a duplex mismatch affect interface behavior?

## Troubleshooting

21. A user reports intermittent connectivity and the interface has increasing CRC and late-collision errors. What would you inspect?
22. Users across a VLAN report extreme slowness and the switch appears overloaded. What Layer 2 conditions would you investigate?
23. Two PCs are connected to the same switch but cannot communicate because they are in different VLANs. What is required?
24. A trunk carries VLAN 10 and VLAN 20. Do these VLANs share one broadcast domain?

---

# 45. Quick Revision ⚡

```text
COLLISION DOMAIN
    ↓
Area where Ethernet collisions can occur
    ↓
Switch port = separate collision-domain segment
    ↓
Full duplex = collisions normally absent
```

```text
BROADCAST DOMAIN
    ↓
Devices receiving a Layer 2 broadcast
    ↓
VLAN = one broadcast domain
    ↓
Router = separates broadcast domains
```

### Memorize this table

| Device/Technology | Collision Domains | Broadcast Domains |
|---|---|---|
| Hub | Shared | One shared domain |
| Switch | One per port/segment | One per VLAN |
| Router | Separate physical/routed segments | One per Layer 3 interface/subnet boundary |
| VLAN | Switch ports remain separate | One per VLAN |

---

# 46. Cheat Sheet 📋

```text
Collision Domain
= Area where collisions can occur

Broadcast Domain
= Area receiving a Layer 2 broadcast

Hub
= 1 shared collision domain

Switch
= Separate collision domain per port

VLAN
= Separate broadcast domain

Router
= Separates broadcast domains

Full Duplex
= No traditional Ethernet collisions expected

Half Duplex
= Collision detection may be required

Broadcast MAC
= FF:FF:FF:FF:FF:FF

Useful Cisco commands
= show interfaces
= show mac address-table
= show vlan brief
= show interfaces trunk
= show spanning-tree
= show ip interface brief
```

---

# 47. Connection to Previous Topics 🔗

You already learned:

- Network communication
- Ethernet frames
- MAC addressing
- Switching fundamentals
- Client-server networking
- Peer-to-peer networking

Now connect them:

```text
Ethernet
   ↓
MAC Address
   ↓
Switching
   ↓
Collision Domains
   ↓
Broadcast Domains
   ↓
VLANs
   ↓
Trunking
   ↓
STP
   ↓
Inter-VLAN Routing
```

These concepts build directly on each other.

---

# 48. Industry Relevance 💼

Understanding domains helps a network engineer:

- Design scalable LANs.
- Reduce unnecessary broadcast traffic.
- Troubleshoot Layer 2 performance issues.
- Understand VLAN segmentation.
- Identify Layer 2 loops.
- Diagnose duplex and physical-layer problems.
- Design redundant networks safely.

For a SOC or cybersecurity analyst, these concepts are also useful because VLANs and Layer 2 boundaries influence:

- Network visibility
- Segmentation
- Broadcast behavior
- Lateral movement analysis
- Packet capture interpretation
- Incident investigation

---

# 49. Completion Checklist ✅

Before moving forward, you should be able to:

- [ ] Define a collision domain.
- [ ] Define a broadcast domain.
- [ ] Explain why a hub creates a shared collision domain.
- [ ] Explain how a switch separates collision domains.
- [ ] Explain why full duplex eliminates traditional Ethernet collisions.
- [ ] Explain CSMA/CD conceptually.
- [ ] Explain Layer 2 broadcasts.
- [ ] Explain why a VLAN is a broadcast domain.
- [ ] Explain how a router separates broadcast domains.
- [ ] Count domains in common topologies.
- [ ] Identify a possible broadcast storm.
- [ ] Use `show interfaces` for interface troubleshooting.
- [ ] Use `show vlan brief` to verify VLAN membership.
- [ ] Use `show interfaces trunk` to verify trunks.
- [ ] Use `show spanning-tree` to investigate Layer 2 loops.
- [ ] Troubleshoot a wrong-VLAN problem.

---

# 🚀 Next Topic

**13 — VLAN Fundamentals**

Next we will go deeper into **VLANs**, including why VLANs are required, VLAN IDs, access ports, VLAN membership, configuration, verification, troubleshooting, and realistic Packet Tracer labs.

---

> 📚 **Study rule:** Don't memorize domain-counting answers blindly. Draw the topology, identify Layer 2 boundaries, identify VLANs, identify routed interfaces, and then count. Understanding the boundary is more valuable than memorizing the number.
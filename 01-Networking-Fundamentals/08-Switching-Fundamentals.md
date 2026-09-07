# 🔀 Switching Fundamentals

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what a network switch does.
- Understand how a switch learns MAC addresses.
- Explain how a switch forwards, filters, and floods Ethernet frames.
- Understand the MAC address table and CAM table concept.
- Differentiate known unicast, unknown unicast, broadcast, and multicast behavior.
- Understand collision domains and the relationship between switches and VLANs.
- Understand basic switch interfaces and Cisco IOS commands.
- Perform basic switch configuration and verification.
- Troubleshoot common Layer 2 switching problems.

---

# 1. What Is a Network Switch?

A **network switch** is a Layer 2 networking device that connects devices and forwards Ethernet frames using destination MAC addresses.

Simple example:

```text
💻 PC1 ───┐
💻 PC2 ───┼── 🔀 SW1
💻 PC3 ───┘
```

Instead of sending every frame to every port, a switch can learn where devices are located and forward traffic toward the correct interface.

### Simple analogy 🏢

Think of a switch like a receptionist in an office.

The receptionist learns:

```text
Employee → Desk location
```

A switch learns:

```text
MAC address → Switch interface
```

When a frame arrives, the switch checks the destination and sends it where it needs to go.

---

# 2. Why Do We Need Switches?

Without modern switching, a shared Ethernet segment would create unnecessary contention and traffic.

A switch provides:

- 🔗 Device connectivity
- 🧠 MAC-based forwarding
- 🚦 Traffic filtering
- ⚡ Dedicated switched Ethernet links
- 🧩 VLAN support
- 🌳 STP support for loop prevention
- 🔐 Features such as port security

Switching is therefore one of the most important foundations of a modern LAN.

---

# 3. Switch vs Hub

| Feature | Hub | Switch |
|---|---|---|
| OSI focus | Layer 1 | Layer 2 primarily |
| Learns MAC addresses | ❌ | ✅ |
| Forwards based on MAC | ❌ | ✅ |
| Each port is a separate collision domain | ❌ | ✅ in normal full-duplex Ethernet |
| Sends incoming traffic everywhere | Generally yes | Only when forwarding/flooding requires it |
| Duplex | Historically half-duplex | Commonly full-duplex |
| Modern LAN use | Obsolete | Standard |

### Key idea

```text
Hub
Frame → all ports

Switch
Frame → appropriate port
```

---

# 4. What Happens When a Frame Enters a Switch?

A simplified switching process is:

```text
        Ethernet frame
              │
              ▼
       ┌──────────────┐
       │    Switch    │
       └──────────────┘
              │
       Read source MAC
              │
              ▼
       Learn/update MAC
              │
              ▼
      Read destination MAC
              │
       ┌──────┴───────┐
       │              │
     Known          Unknown
       │              │
       ▼              ▼
    Forward         Flood
```

The exact internal implementation varies by platform, but this is the essential CCNA model.

---

# 5. Step 1 — Source MAC Learning 🧠

Suppose PC1 sends a frame into Gi0/1.

```text
PC1
MAC = AAAA.AAAA.AAAA
        │
        ▼
     Gi0/1
        │
       SW1
```

The switch learns:

```text
AAAA.AAAA.AAAA → Gi0/1
```

This information is stored in the switch's Layer 2 forwarding database, commonly viewed through the MAC address table.

### Important rule

> A switch learns from the **source MAC address** of incoming frames.

---

# 6. Step 2 — Destination MAC Lookup 🔎

After processing the source MAC, the switch examines the destination MAC.

Suppose:

```text
Destination = BBBB.BBBB.BBBB
```

If the table contains:

```text
BBBB.BBBB.BBBB → Gi0/2
```

The switch can forward the frame toward Gi0/2.

This is called a **known unicast**.

---

# 7. Known Unicast Forwarding

Example:

```text
PC1                 PC2
AAAA                BBBB
  │                   │
  └── Gi0/1   Gi0/2 ─┘
          SW1
```

MAC table:

```text
AAAA.AAAA.AAAA → Gi0/1
BBBB.BBBB.BBBB → Gi0/2
```

PC1 sends to PC2:

```text
Destination MAC = BBBB.BBBB.BBBB
```

SW1 knows that BBBB is on Gi0/2.

➡️ The frame is forwarded out Gi0/2.

---

# 8. Unknown Unicast Flooding 📢

What if the destination MAC is not currently in the switch's MAC table?

Example:

```text
Destination = CCCC.CCCC.CCCC
```

But the table has no entry for CCCC.

The switch normally floods the frame within the relevant VLAN, except back out the interface where the frame arrived.

```text
             PC2
              ↑
              │
PC1 ──────── SW1 ─────── PC3
              │
              ↓
             PC4
```

PC2, PC3 and PC4 may receive the flooded frame, but the receiving device accepts it only if the destination MAC matches its own or the frame is otherwise applicable to it.

### Important

> Unknown unicast flooding is normal switching behavior. It is not the same thing as a broadcast.

---

# 9. Broadcast Forwarding 📣

The Ethernet broadcast destination is:

```text
FF:FF:FF:FF:FF:FF
```

A switch floods a broadcast within the relevant VLAN/broadcast domain.

Example:

```text
             PC2
              ↑
              │
PC1 ──────── SW1 ─────── PC3
              │
              ↓
             PC4
```

If PC1 sends a broadcast:

➡️ SW1 floods it to the other eligible ports in the same VLAN.

Routers normally do not forward Layer 2 broadcasts between IP networks by default.

---

# 10. Multicast Forwarding 👥

Multicast is designed for a group of receivers.

Switch behavior depends on the multicast traffic, platform, and configuration.

A switch may flood some multicast traffic when it does not have the information needed to limit forwarding, while features such as **IGMP snooping** can help switches intelligently forward IPv4 multicast traffic based on receiver membership.

Detailed multicast behavior is outside this introductory switching lesson.

---

# 11. Frame Filtering 🚦

A switch does not always forward a frame out every port.

For a known destination:

```text
Incoming port
     ↓
Destination lookup
     ↓
Correct outgoing port
```

If the destination is known to be reachable through the same interface where the frame arrived, the switch can filter/drop the frame instead of sending it back out that same interface.

This prevents unnecessary transmission on that port.

---

# 12. MAC Address Table

You can inspect a Cisco switch's MAC address table with:

```cisco
show mac address-table
```

Example output concept:

```text
Vlan    Mac Address       Type       Ports
----    -----------       --------   -----
1       aaaa.aaaa.aaaa   DYNAMIC    Gi0/1
1       bbbb.bbbb.bbbb   DYNAMIC    Gi0/2
```

Think of the table as:

```text
MAC address → VLAN + switch port
```

---

# 13. CAM Table Concept

You may hear the term **CAM table** in networking.

CAM stands for **Content Addressable Memory**.

It is associated with hardware-based Layer 2 lookup on switches.

For CCNA purposes, you should understand:

```text
MAC address
     ↓
Layer 2 forwarding information
     ↓
Switch forwards frame
```

Cisco CLI commonly exposes this information through the **MAC address table**.

> Do not get stuck on hardware implementation details at this stage. Focus on how the switch uses learned MAC information to forward frames.

---

# 14. MAC Address Aging ⏳

Dynamic MAC entries are not necessarily permanent.

A switch can remove an entry after it has not observed traffic from that source for the configured aging period.

Simplified process:

```text
Learn MAC
   ↓
Store dynamically
   ↓
Traffic continues?
   │
   ├── Yes → refresh/maintain entry
   │
   └── No → eventually age out
```

This allows switches to adapt when devices move or disappear.

---

# 15. Collision Domains 💥

A **collision domain** is a network segment in which devices could potentially contend for the same shared Ethernet medium.

Modern switched full-duplex Ethernet does not normally experience Ethernet collisions on each individual switch link.

Conceptually:

```text
PC1 ── SW1 ── PC2
 │             │
Separate switched links
```

Each normal switch port represents a separate collision domain.

### Hub comparison

With a hub:

```text
PC1 ─┐
PC2 ─┼── HUB
PC3 ─┘
```

All devices share the same collision domain.

---

# 16. Broadcast Domains 📢

A **broadcast domain** is the Layer 2 scope in which a broadcast frame is propagated.

A basic Layer 2 switch without VLAN separation places its active ports in the same broadcast domain.

Routers separate broadcast domains.

VLANs can also divide a switch into multiple logical broadcast domains.

Example:

```text
VLAN 10
PC1 ──┐
PC2 ──┼── SW1
PC3 ──┘

VLAN 20
PC4 ──┐
PC5 ──┼── SW1
PC6 ──┘
```

VLAN 10 and VLAN 20 are separate Layer 2 broadcast domains.

VLANs will be studied in depth later.

---

# 17. Collision Domain vs Broadcast Domain

| Concept | Collision Domain | Broadcast Domain |
|---|---|---|
| Concern | Shared access/collisions | Broadcast propagation |
| Modern switch ports | Separate collision domains | Usually same broadcast domain within a VLAN |
| Router | Separates | Separates |
| VLAN | Does not primarily define collision domains | Separates broadcast domains |
| Example problem | Shared-medium contention | Excessive broadcast traffic |

### Easy memory trick 🧠

```text
Collision → Who could compete for the medium?
Broadcast → Who receives the broadcast?
```

---

# 18. Switching Methods

Cisco switching hardware can use different forwarding approaches.

### Store-and-forward

The switch receives the complete frame before forwarding it.

It can check the frame's FCS before forwarding.

```text
Receive full frame
       ↓
Check
       ↓
Forward
```

### Cut-through

The switch can begin forwarding before receiving the complete frame.

```text
Read enough information
       ↓
Start forwarding
```

This can reduce forwarding latency, but it provides less opportunity to validate the complete frame before forwarding.

### CCNA focus

Know the basic difference and trade-off:

```text
Store-and-forward
→ more complete validation
→ potentially more latency

Cut-through
→ lower latency
→ less complete validation before forwarding
```

---

# 19. Full-Duplex Ethernet ↔️

Modern switched Ethernet links normally operate in full-duplex mode when supported by both sides.

Full duplex means:

```text
Device A  ⇄  Device B
```

Traffic can travel in both directions simultaneously.

This eliminates the traditional Ethernet collision mechanism on that link.

Check interface details with:

```cisco
show interfaces gigabitEthernet 0/1
```

Look for speed, duplex, errors, and other interface information.

---

# 20. Basic Cisco Switch Configuration ⚙️

A basic lab switch can be configured like this:

```cisco
enable
configure terminal
hostname SW1
```

Configure an access interface:

```cisco
interface gigabitEthernet 0/1
switchport mode access
no shutdown
exit
```

Save the configuration when appropriate in a lab:

```cisco
copy running-config startup-config
```

> Interface names and supported commands vary by Cisco switch model and IOS/IOS XE release. Use `?` and the device's interface list to confirm exact syntax.

---

# 21. Basic Switch Verification 🔎

### Check interface summary

```cisco
show interfaces status
```

### Check IP interface summary

```cisco
show ip interface brief
```

### Check MAC table

```cisco
show mac address-table
```

### Check running configuration

```cisco
show running-config
```

### Check device information

```cisco
show version
```

### Check VLANs

```cisco
show vlan brief
```

### Check STP

```cisco
show spanning-tree
```

Some commands are introduced here because switching troubleshooting becomes much easier when you can correlate interface, VLAN, MAC, and STP information.

---

# 22. Understanding Interface Status

A common output pattern is:

```text
Port      Name      Status      Vlan
Gi0/1               connected   1
Gi0/2               notconnect  1
```

### Common states

- `connected` → physical/link state is generally up.
- `notconnect` → interface is not detecting an active link.
- `disabled` → administratively disabled or otherwise inactive depending on platform/output.

Always inspect the actual device output rather than memorizing every possible status string.

---

# 23. Practical Lab 1 — Basic Switching 🧪

## Objective

Build a small switched LAN and observe MAC learning.

## Topology

```text
PC1 ─────┐
         │
PC2 ─────┼──── 🔀 SW1
         │
PC3 ─────┘
```

## Suggested addressing

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC1 | 192.168.1.10 | 255.255.255.0 |
| PC2 | 192.168.1.20 | 255.255.255.0 |
| PC3 | 192.168.1.30 | 255.255.255.0 |

No default gateway is required for PC-to-PC communication within this same subnet.

## Tasks

1. Connect the PCs to SW1.
2. Configure the IP addresses.
3. Ping PC2 from PC1.
4. On SW1 run:

```cisco
show mac address-table
```

5. Identify the MAC address and port for each PC.

## Expected result

- PC1 can reach PC2 and PC3.
- SW1 learns the source MAC addresses.
- Dynamic MAC entries appear in the table.

---

# 24. Practical Lab 2 — Known vs Unknown Destination 🧪

1. Clear dynamic MAC entries if supported:

```cisco
clear mac address-table dynamic
```

2. Check:

```cisco
show mac address-table
```

3. Generate traffic between two PCs.
4. Check the table again.
5. Explain why the switch initially may flood traffic for a destination it has not yet learned.
6. Repeat the communication after the MAC addresses have been learned.

### Goal

Observe the transition:

```text
Unknown destination
        ↓
Flood
        ↓
Source MAC learned
        ↓
Known destination
        ↓
Selective forwarding
```

---

# 25. Practical Lab 3 — VLAN Awareness 🧪

Create two groups:

```text
VLAN 10 → PC1, PC2
VLAN 20 → PC3, PC4
```

Then compare:

```cisco
show vlan brief
show mac address-table
```

### Questions

1. Are all MAC addresses in the same VLAN?
2. Can VLAN 10 hosts communicate directly with VLAN 20 hosts?
3. What device/function is required for communication between different IP networks/VLANs?

This introduces the reason **Inter-VLAN Routing** will be required later.

---

# 26. Troubleshooting Scenario 🔧

## Problem

PC1 is connected to SW1, but cannot communicate with another PC.

Use this sequence:

### Step 1 — Physical/link check

```cisco
show interfaces status
```

### Step 2 — Interface details

```cisco
show interfaces gigabitEthernet 0/1
```

### Step 3 — MAC learning

```cisco
show mac address-table
```

### Step 4 — VLAN membership

```cisco
show vlan brief
```

### Step 5 — STP state

```cisco
show spanning-tree
```

### Step 6 — Host configuration

On Windows:

```powershell
ipconfig /all
ping 192.168.1.20
arp -a
```

### Step 7 — Compare the evidence

Do not change configuration randomly.

Ask:

```text
Is the link up?
     ↓
Is the MAC learned?
     ↓
Is the port in the correct VLAN?
     ↓
Is STP forwarding?
     ↓
Is the host IP configuration correct?
```

---

# 27. Common Switching Problems ⚠️

| Problem | Possible Cause | First Checks |
|---|---|---|
| Port down | Cable/device issue | `show interfaces status` |
| MAC not learned | No traffic, wrong link, interface issue | MAC table + interface |
| Wrong VLAN | Port assigned incorrectly | `show vlan brief` |
| Intermittent connectivity | Loop, errors, duplex issue, topology problem | Interfaces + STP |
| MAC flapping | Loop or unexpected path | MAC table + STP |
| Excessive broadcast | Large L2 domain or loop | VLAN/STP/broadcast analysis |
| Host works on one port only | VLAN or port configuration | Interface + VLAN |

---

# 28. Real-World Scenario 🏢

A company has 50 employees connected to two access switches.

Users report that computers in one department cannot reach a server in another department.

The engineer checks:

```cisco
show interfaces status
show vlan brief
show mac address-table
show spanning-tree
```

The switches are learning MAC addresses correctly, but the departments are placed in different VLANs.

### Lesson

Correct MAC learning does **not** automatically mean end-to-end communication is possible.

The network may still require:

```text
Different VLANs
      ↓
Different Layer 3 networks
      ↓
Inter-VLAN routing
```

---

# 29. Scenario-Based Challenge 🎯

## The New Switch Problem

A small office installs a new switch.

The topology is:

```text
             🌐 Internet
                  │
               Router
                  │
               🔀 SW1
              /     \
            PC1     PC2
```

PC1 can ping the router but cannot ping PC2.

The engineer observes:

```text
SW1 Gi0/1 → PC1
SW1 Gi0/2 → PC2
```

### Your tasks

Before looking at the solution, determine:

1. Are both interfaces physically up?
2. Are both ports in the expected VLAN?
3. Are both PC MAC addresses learned?
4. Are both PCs in the same IP subnet?
5. Does the ARP table contain the expected entry?
6. Is there any STP issue?

Useful commands:

```cisco
show interfaces status
show vlan brief
show mac address-table
show spanning-tree
```

### Expected troubleshooting mindset

Do not immediately configure a router or change IP addresses.

First establish whether the failure is:

```text
Layer 1
  ↓
Layer 2
  ↓
Layer 3
```

---

# 30. Practice Exercises ✍️

### Exercise 1 — MAC learning

Build a three-PC topology and document:

- PC MAC addresses
- Switch ports
- MAC table before traffic
- MAC table after traffic

### Exercise 2 — Known vs unknown

Clear dynamic MAC entries and observe what changes after a ping.

### Exercise 3 — Broadcast

Use ARP activity to reason about broadcast forwarding within a VLAN.

### Exercise 4 — Collision domains

Compare a hub topology with a switch topology conceptually.

### Exercise 5 — VLAN boundary

Create two VLANs and explain why a basic Layer 2 switch does not route traffic between them.

### Exercise 6 — Troubleshooting

Intentionally put a PC access port in the wrong VLAN and diagnose the problem using show commands.

---

# 31. Interview Questions 🎤

## Beginner

1. What is a switch?
2. At which OSI layer does a traditional Ethernet switch primarily operate?
3. What address does a switch use for Layer 2 forwarding?
4. How does a switch learn a MAC address?
5. What is a MAC address table?
6. What happens when the destination MAC is unknown?
7. What is a broadcast MAC address?

## Intermediate

8. What is the difference between forwarding and flooding?
9. What is a collision domain?
10. What is a broadcast domain?
11. How does a switch differ from a hub?
12. What is MAC address aging?
13. What is a CAM table?
14. Why does a VLAN create a separate broadcast domain?
15. Why does a switch not normally route between VLANs?

## Advanced

16. Compare store-and-forward and cut-through switching.
17. Why are modern switched Ethernet links normally full duplex?
18. What could cause MAC flapping?
19. Why can unknown unicast flooding occur?
20. How can STP affect switching behavior?

## Troubleshooting

21. A MAC address is not appearing in the MAC table. What do you check?
22. A port is up but the host cannot communicate. What should you investigate?
23. A host works on one switch port but not another. What could be different?
24. How would you determine whether a problem is Layer 1, Layer 2, or Layer 3?
25. Which Cisco commands would you use to troubleshoot a basic switched LAN?

---

# 32. Quick Revision ⚡

```text
🔀 SWITCH
│
├── Primarily Layer 2
│
├── Learns SOURCE MAC
│
├── Looks up DESTINATION MAC
│
├── Known unicast → forward
│
├── Unknown unicast → flood within VLAN
│
├── Broadcast → flood within VLAN
│
├── MAC table → MAC + VLAN + interface
│
├── Dynamic entries → can age out
│
├── Each normal switch port → separate collision domain
│
└── VLAN → separate broadcast domain
```

---

# 33. Switching Cheat Sheet 📋

| Concept | Remember |
|---|---|
| Switch | Connects devices and forwards frames |
| Primary forwarding address | Destination MAC |
| Learning source | Source MAC |
| MAC table | MAC → VLAN/interface |
| Known unicast | Forward selectively |
| Unknown unicast | Flood within relevant VLAN |
| Broadcast | Flood within relevant VLAN |
| Collision domain | Separate per normal switch port |
| Broadcast domain | Defined by Layer 2/VLAN boundary |
| Router | Separates broadcast domains and routes IP packets |
| VLAN | Creates logical Layer 2 broadcast domain |
| Store-and-forward | Receives full frame before forwarding |
| Cut-through | Can begin forwarding before full frame received |
| Verification | `show mac address-table` |
| Interface status | `show interfaces status` |
| VLAN verification | `show vlan brief` |
| STP verification | `show spanning-tree` |

---

# 34. Connection to the Next Topics 🔗

Switching is the foundation for the next major CCNA Layer 2 technologies.

```text
🔀 Switching
     ↓
🏷️ VLANs
     ↓
🔗 Trunking
     ↓
🌳 STP
     ↓
🔗 EtherChannel
     ↓
🛣️ Inter-VLAN Routing
```

You already know that a switch uses MAC addresses.

Next, we will introduce **VLANs**, which allow one physical switch infrastructure to be logically divided into separate Layer 2 broadcast domains.

➡️ **Next: 09 — VLAN Fundamentals**

---

# ✅ Completion Checklist

- [ ] I can explain what a switch does.
- [ ] I understand source MAC learning.
- [ ] I understand destination MAC lookup.
- [ ] I understand known unicast forwarding.
- [ ] I understand unknown unicast flooding.
- [ ] I understand broadcast forwarding.
- [ ] I understand the MAC address table.
- [ ] I understand the CAM table concept.
- [ ] I understand MAC aging.
- [ ] I understand collision domains.
- [ ] I understand broadcast domains.
- [ ] I understand store-and-forward and cut-through at a basic level.
- [ ] I can check switch interfaces.
- [ ] I can inspect the MAC address table.
- [ ] I can verify VLAN membership.
- [ ] I can inspect STP state.
- [ ] I completed the switching Packet Tracer labs.
- [ ] I can troubleshoot a basic Layer 2 problem systematically.

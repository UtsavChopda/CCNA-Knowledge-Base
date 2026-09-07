# 🖧 Ethernet Fundamentals

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what Ethernet is and where it is used.
- Understand Ethernet frames and their major fields.
- Explain source and destination MAC addresses.
- Differentiate unicast, broadcast, and multicast traffic.
- Understand how a switch learns and forwards Ethernet frames.
- Explain collision domains and broadcast domains at a beginner level.
- Understand full-duplex Ethernet and the historical role of CSMA/CD.
- Use Cisco commands to inspect Ethernet switching behavior.
- Troubleshoot common Layer 2 Ethernet problems.

---

# 1. What Is Ethernet?

**Ethernet** is a family of technologies used to carry data over local and metropolitan networks. Modern wired LANs overwhelmingly use Ethernet.

Ethernet defines important parts of communication such as:

- Frame format
- MAC addressing
- Media access behavior
- Physical signaling requirements
- Link speeds
- Ethernet standards

A simple network looks like:

```text
[PC] ───── Ethernet ───── [Switch] ───── Ethernet ───── [Server]
```

Ethernet mainly operates at the **Physical Layer (Layer 1)** and **Data Link Layer (Layer 2)** of the OSI model.

---

# 2. Ethernet and the OSI Model 🧠

```text
OSI Layer 2
    ↓
Ethernet frame
MAC addressing
Switching

OSI Layer 1
    ↓
Electrical / optical signals
Cables / connectors
Bit transmission
```

Remember:

> **Ethernet frame = Layer 2**  
> **Ethernet physical signaling = Layer 1**

IP operates primarily at Layer 3 and is carried inside Ethernet frames on an Ethernet LAN.

---

# 3. Ethernet Frame 📦

An Ethernet frame is the Layer 2 unit used to transport data across an Ethernet link.

A simplified Ethernet frame looks like:

```text
+----------+----------+----------+----------+----------+
| Preamble | Dest MAC | Src MAC  | Type/Len | Payload  |
+----------+----------+----------+----------+----------+
|                         ...                         |
+----------------------+------------------------------+
| FCS                  |
+----------------------+
```

The exact format has additional details, but these fields are the most important for CCNA fundamentals.

---

# 4. Important Ethernet Frame Fields

## 4.1 Preamble

The preamble helps synchronize the receiver with the incoming Ethernet frame.

For basic CCNA understanding:

> It helps the receiving interface prepare to receive the frame.

---

## 4.2 Destination MAC Address

Identifies the intended Layer 2 destination.

Example:

```text
Destination MAC: 00:11:22:33:44:55
```

A switch uses the destination MAC address when making a Layer 2 forwarding decision.

---

## 4.3 Source MAC Address

Identifies the Layer 2 source of the frame.

Example:

```text
Source MAC: AA:BB:CC:DD:EE:FF
```

Switches use the source MAC address to learn which interface leads toward a device.

---

## 4.4 Type / Length

The field after the source MAC identifies the upper-layer protocol when using Ethernet II framing, such as IPv4 or IPv6.

Examples of EtherTypes include:

```text
0x0800 → IPv4
0x0806 → ARP
0x86DD → IPv6
```

---

## 4.5 Payload / Data

This contains the encapsulated higher-layer data.

For example:

```text
Ethernet Frame
└── IPv4 Packet
    └── TCP Segment
        └── Application Data
```

---

## 4.6 FCS — Frame Check Sequence

The **Frame Check Sequence (FCS)** helps detect corruption in the received Ethernet frame.

If the calculated check does not match the transmitted value, the receiver can detect that the frame was damaged.

A common symptom associated with corrupted Ethernet frames is a **CRC error** reported by an interface.

---

# 5. Encapsulation 🔄

When an application sends data, each layer adds information as the data moves down the protocol stack.

```text
Application Data
       ↓
TCP/UDP Segment
       ↓
IP Packet
       ↓
Ethernet Frame
       ↓
Bits / Signals
```

On the receiving side, the process is reversed:

```text
Bits / Signals
       ↓
Ethernet Frame
       ↓
IP Packet
       ↓
TCP/UDP Segment
       ↓
Application Data
```

This process is called **encapsulation and de-encapsulation**.

---

# 6. MAC Address 🏷️

A **MAC address** is a Layer 2 address used by Ethernet interfaces.

A common representation is:

```text
00:1A:2B:3C:4D:5E
```

or:

```text
001A.2B3C.4D5E
```

A traditional Ethernet MAC address is **48 bits (6 bytes)**.

```text
48 bits
  ↓
6 bytes
```

---

# 7. MAC Address Structure

A MAC address can be conceptually divided into two parts:

```text
00:1A:2B | 3C:4D:5E
     ↓          ↓
  OUI /        Interface
  vendor       identifier
  portion      portion
```

The first portion traditionally identifies an organizationally assigned prefix, while the remaining bits identify the interface according to the addressing scheme.

Do not assume every MAC address is a simple permanent “manufacturer serial number.” Modern systems can use locally administered and randomized MAC addresses.

---

# 8. Unicast, Broadcast, and Multicast 📡

Ethernet traffic can be categorized by destination.

## Unicast

One sender → one destination.

```text
PC1 ─────────→ PC2
```

Example:

```text
Source MAC      → Destination MAC
AA:AA:AA:AA:AA:AA → BB:BB:BB:BB:BB:BB
```

---

## Broadcast

One sender → all devices in the relevant Layer 2 broadcast domain.

The Ethernet broadcast destination is:

```text
FF:FF:FF:FF:FF:FF
```

Example:

```text
             ┌── PC2
             ├── PC3
PC1 ── SW ───┼── PC4
             └── PC5
```

The switch floods a broadcast within the applicable VLAN/broadcast domain.

---

## Multicast

One sender → a selected group of receivers.

```text
             ┌── Receiver A
             ├── Receiver B
Sender ──────┼── Receiver C
             └── Other devices do not necessarily receive it
```

Multicast is useful for applications such as streaming, routing protocols, and other group-based communication.

---

# 9. Switch MAC Learning 🔀

A Layer 2 switch builds a MAC address table by examining the **source MAC address** of incoming frames.

Example:

```text
PC1 → SW1

Source MAC = AAAA.AAAA.AAAA
Incoming port = Gi0/1
```

The switch learns:

```text
AAAA.AAAA.AAAA → Gi0/1
```

If PC2 sends from Gi0/2:

```text
BBBB.BBBB.BBBB → Gi0/2
```

The table becomes:

| MAC Address | Interface |
|---|---|
| AAAA.AAAA.AAAA | Gi0/1 |
| BBBB.BBBB.BBBB | Gi0/2 |

---

# 10. How a Switch Forwards a Frame

Consider:

```text
PC1 ── Gi0/1 [SW1] Gi0/2 ── PC2
```

PC1 sends a frame to PC2.

### Step 1 — Frame arrives

SW1 receives the frame on Gi0/1.

### Step 2 — Learn source MAC

SW1 learns PC1's MAC on Gi0/1.

### Step 3 — Look up destination MAC

SW1 checks its MAC address table for PC2's MAC.

### Step 4 — Forward or flood

If the destination is known:

➡️ Forward only toward the destination interface.

If the destination is unknown:

➡️ Flood within the relevant VLAN, excluding the receiving interface.

---

# 11. Unknown Unicast Flooding

Suppose PC1 sends to a MAC address that the switch has not learned yet.

```text
             ┌── PC2
             ├── PC3
PC1 ──→ [SW] ─┼── PC4
             └── PC5
```

The switch floods the unknown unicast frame out applicable ports except the port where it arrived.

When the destination device responds, the switch can learn its source MAC address.

Eventually, the MAC table becomes more complete and forwarding becomes more selective.

---

# 12. MAC Address Table

On Cisco switches:

```cisco
show mac address-table
```

You may see output similar to:

```text
Vlan    Mac Address       Type       Ports
----    -----------       --------   -----
   1    aaaa.aaaa.aaaa   DYNAMIC    Gi0/1
   1    bbbb.bbbb.bbbb   DYNAMIC    Gi0/2
```

### Important terms

**Dynamic** — learned automatically.

**Static** — manually configured/installed according to the platform's configuration mechanisms.

---

# 13. MAC Table Aging ⏳

Dynamic MAC entries are not normally permanent.

If a switch does not see traffic from a learned source for the configured aging period, the dynamic entry can be removed.

Why?

Because devices can move between ports.

Example:

```text
Initially:
PC1 → Gi0/1

Later:
PC1 → Gi0/5
```

The switch needs to update its knowledge of where PC1 is connected.

---

# 14. Ethernet Frame Forwarding Example 🧠

```text
PC1
MAC A
 |
 | Frame:
 | Src = A
 | Dst = B
 v
[SW1]
 |
 | MAC table says B → Gi0/2
 v
PC2
MAC B
```

The switch does **not** need to send the frame to every port when it already knows the destination interface.

That is a major reason switches are much more efficient than traditional hubs.

---

# 15. Collision Domain 💥

A **collision** occurs when multiple devices transmit onto a shared medium at the same time and their signals interfere.

A traditional hub-based Ethernet segment forms a shared collision domain.

```text
PC1 ─┐
PC2 ─┼── [Hub]
PC3 ─┘
```

Modern switched full-duplex Ethernet normally eliminates collisions on each point-to-point switch link.

### Key idea

> A switch creates separate collision domains per active Ethernet port in normal switched Ethernet operation.

---

# 16. Broadcast Domain 📢

A **broadcast domain** is the Layer 2 area in which a broadcast frame is propagated.

A basic Layer 2 switch floods broadcasts within the same VLAN.

```text
             PC1
              |
PC2 ─────── [SW1] ───── PC3
              |
             PC4
```

If these ports belong to the same VLAN, a broadcast can reach the devices in that VLAN.

Routers and Layer 3 boundaries normally prevent a Layer 2 broadcast from simply propagating into another IP network.

VLANs also allow a switched network to be divided into separate broadcast domains.

---

# 17. Collision Domain vs Broadcast Domain

| Concept | Meaning |
|---|---|
| Collision domain | Area where shared-medium collisions could occur |
| Broadcast domain | Area receiving a Layer 2 broadcast |

### Typical modern switch

```text
          [Switch]
         /   |   \
       PC1  PC2  PC3
```

Each switch port is normally its own collision domain in full-duplex operation.

If all ports are in VLAN 10, they are part of the same broadcast domain.

### Router

A router separates Layer 2 broadcast domains between its interfaces.

---

# 18. Full Duplex vs Half Duplex

## Full duplex

Both directions can transmit simultaneously.

```text
A ⇄ B
```

Modern switched Ethernet links normally operate this way.

## Half duplex

Only one direction can transmit at a time.

```text
A → B
then
B → A
```

Half duplex was important in older shared Ethernet environments.

---

# 19. CSMA/CD — Historical Ethernet Concept

**CSMA/CD** stands for:

> Carrier Sense Multiple Access with Collision Detection

It was used by traditional shared, half-duplex Ethernet.

The simplified process was:

```text
Listen
  ↓
Is medium free?
  ↓
Transmit
  ↓
Collision?
  ↓
Yes → Stop → Backoff → Retry
```

### Modern Ethernet

With point-to-point full-duplex switched Ethernet, collisions are not expected during normal operation, so CSMA/CD is not used for normal full-duplex links.

> **CCNA tip:** Understand CSMA/CD as an important historical Ethernet mechanism and know why full-duplex switched Ethernet does not need it.

---

# 20. Ethernet Standards and Naming

Ethernet technologies have historically used names such as:

```text
10BASE-T
100BASE-TX
1000BASE-T
10GBASE-SR
10GBASE-LR
```

The naming can communicate information about speed, signaling, and medium.

Examples:

- **10BASE-T** → 10 Mbps Ethernet over twisted pair
- **100BASE-TX** → Fast Ethernet over twisted pair
- **1000BASE-T** → Gigabit Ethernet over twisted pair
- **10GBASE-SR** → 10 Gigabit Ethernet using short-reach optical technology
- **10GBASE-LR** → 10 Gigabit Ethernet using longer-reach optical technology

Exact distance and media requirements depend on the standard and physical implementation.

---

# 21. Ethernet Frame Size 📏

A traditional Ethernet frame has a minimum and maximum size for the normal Ethernet frame format.

Common values taught for Ethernet II are:

- **Minimum frame size:** 64 bytes, from Destination MAC through FCS
- **Maximum standard frame size:** 1518 bytes, from Destination MAC through FCS

The preamble and Start Frame Delimiter are transmitted separately and are not included in those frame-size values.

### What about jumbo frames?

Some networks and devices support **jumbo frames** larger than the traditional Ethernet maximum, but this requires compatible equipment and configuration throughout the path.

> **CCNA exam/lab focus:** Know the standard 64–1518 byte Ethernet frame range and understand that jumbo frames are an extension, not universal Ethernet behavior.

---

# 22. Ethernet and VLANs 🔗

Ethernet frames can carry VLAN information when using **802.1Q tagging** on trunk links.

Conceptually:

```text
Normal Ethernet frame
        ↓
802.1Q VLAN tag added on tagged trunk traffic
        ↓
Frame crosses trunk
        ↓
Tag handled according to VLAN/trunk behavior
```

You will study VLAN tagging and trunking in detail later.

---

# 23. Ethernet and ARP

IPv4 hosts use **ARP** to discover the MAC address associated with a local IPv4 address.

Example:

```text
PC1 knows:
IP = 192.168.1.20

PC1 asks:
“Who has 192.168.1.20?”

ARP request → Broadcast

Target responds with its MAC address.
```

The resulting IP-to-MAC relationship can be stored in the host's ARP cache.

This demonstrates an important relationship:

```text
IPv4 address → ARP → MAC address → Ethernet frame
```

---

# 24. Ethernet Communication: Same Subnet

Suppose:

```text
PC1
IP: 192.168.1.10/24
MAC: AAAA.AAAA.AAAA

PC2
IP: 192.168.1.20/24
MAC: BBBB.BBBB.BBBB
```

PC1 determines PC2 is on the same subnet.

It needs PC2's MAC address and then sends an Ethernet frame:

```text
Source MAC      = AAAA.AAAA.AAAA
Destination MAC = BBBB.BBBB.BBBB
```

The switch forwards the frame using its MAC table.

---

# 25. Ethernet Communication: Different Subnet

Suppose:

```text
PC1: 192.168.1.10/24
Server: 10.0.0.10/24
Gateway: 192.168.1.1
```

PC1 determines that the server is on another IP network.

It sends the Ethernet frame toward the **default gateway's MAC address**, not directly to the remote server's MAC address.

Conceptually:

```text
PC1
  ↓ Ethernet frame to gateway MAC
Switch
  ↓
Router / L3 Gateway
  ↓ IP routing
Remote Network
  ↓
Server
```

This is one of the most important relationships between Layer 2 Ethernet and Layer 3 IP routing.

---

# 26. Cisco Commands for Ethernet Verification 🔎

## `show mac address-table`

```cisco
show mac address-table
```

Displays learned Layer 2 MAC entries.

---

## `show mac address-table dynamic`

```cisco
show mac address-table dynamic
```

Filters the output to dynamic MAC entries on platforms supporting this syntax.

---

## `show interfaces status`

```cisco
show interfaces status
```

Useful for checking switch port state, VLAN, speed, and duplex information.

---

## `show interfaces`

```cisco
show interfaces
```

Shows detailed Ethernet interface information, including counters and errors.

---

## `show interfaces counters errors`

On supported Cisco platforms:

```cisco
show interfaces counters errors
```

Useful for quickly viewing interface error counters.

---

## `show arp`

```cisco
show arp
```

Displays ARP entries on the Cisco device.

---

## `clear mac address-table dynamic`

On supported platforms:

```cisco
clear mac address-table dynamic
```

This can clear dynamic MAC entries so you can observe relearning behavior.

> Command syntax can vary by Cisco IOS/IOS XE release and platform. Always verify available syntax with `?`.

---

# 27. Using `?` to Learn Cisco Commands 🧑‍💻

Cisco IOS provides contextual help.

Example:

```cisco
Switch# show mac address-table ?
```

You can also type:

```cisco
Switch# show interfaces ?
```

This is an important professional habit:

> **Do not memorize every command blindly. Learn how to discover valid syntax on the device.**

---

# 28. Mini Lab — Observe MAC Learning 🧪

## Topology

```text
PC1 ───┐
PC2 ───┼── [SW1]
PC3 ───┘
```

## Tasks

1. Configure IP addresses on the PCs.
2. Ping between PCs.
3. On SW1, run:

```cisco
show mac address-table
```

4. Identify the MAC address learned on each port.
5. Disconnect PC2.
6. Observe the MAC table over time.
7. Reconnect PC2.
8. Generate traffic and observe relearning.

### Learning goal

Understand that a switch learns from **source MAC addresses**.

---

# 29. Mini Lab — Unknown Unicast

Create a small switch topology.

```text
PC1 ──┐
PC2 ──┼── SW1
PC3 ──┘
```

Clear dynamic MAC entries if supported:

```cisco
clear mac address-table dynamic
```

Then generate traffic.

Observe:

```cisco
show mac address-table
```

### Question

Why does the switch initially need to flood a frame when the destination MAC is unknown?

### Answer

Because it does not yet know which interface leads to that destination MAC address.

---

# 30. Mini Lab — Collision and Broadcast Domains

Build:

```text
PC1 ─┐
PC2 ─┼── SW1 ─── Router
PC3 ─┘
```

Discuss:

1. How many normal switch-port collision domains exist?
2. How many broadcast domains exist if all PCs are in the same VLAN and the router interface is the Layer 3 boundary?
3. What changes if you create a second VLAN?

This lab prepares you for the VLAN module.

---

# 31. Troubleshooting Ethernet 🔧

Use a structured approach.

### Step 1 — Physical layer

Check:

- Cable
- Transceiver
- Link lights
- Interface state

Commands:

```cisco
show interfaces status
show ip interface brief
```

### Step 2 — Interface errors

```cisco
show interfaces gigabitEthernet 0/1
```

Look for:

- CRC errors
- Input errors
- Output errors
- Drops
- Runts
- Giants

### Step 3 — MAC learning

```cisco
show mac address-table
```

Ask:

> Is the switch learning the expected MAC address on the expected port?

### Step 4 — VLAN

Later you will verify VLAN membership with commands such as:

```cisco
show vlan brief
```

### Step 5 — IP/ARP

```cisco
show arp
```

Check whether the expected IP-to-MAC relationship exists.

### Step 6 — Routing

If traffic is leaving the local subnet:

```cisco
show ip route
```

---

# 32. Common Ethernet Problems ❌

| Problem | Possible cause |
|---|---|
| Link down | Cable, interface, remote port, transceiver |
| CRC errors | Physical/media problem, interference, faulty component |
| High collisions | Legacy half-duplex/shared-medium condition |
| Wrong MAC learned | Cabling, topology, device movement, loop, or other Layer 2 issue |
| Unknown unicast flooding | Destination MAC not currently learned |
| Broadcast flooding | Normal behavior within broadcast domain, or excessive broadcast traffic |
| Local devices cannot communicate | VLAN, interface, cable, MAC, or host issue |
| Remote network unreachable | Gateway/routing/IP problem |

---

# 33. Real-World Scenario 🏢

A company has:

```text
          [Router]
             |
          [SW-Core]
          /       \
      [SW-A]     [SW-B]
      /   \       /   \
    PCs   APs   PCs   Phones
```

A user on SW-A cannot reach another user on SW-B.

### Investigation

Start with:

```cisco
show interfaces status
show mac address-table
show cdp neighbors
```

Then verify VLAN configuration and the uplink.

If the users are in different IP networks, continue into Layer 3 verification.

This is why Ethernet knowledge is essential before learning VLANs and routing.

---

# 34. Scenario Challenge 🧠

### Problem

PC1 and PC2 are connected to the same switch and same VLAN.

Both have valid IP addresses.

PC1 can communicate with the gateway but cannot communicate with PC2.

### Questions

1. Is the router necessarily the problem?
2. What would you check in the MAC table?
3. What would you check on both switch ports?
4. Could the host firewalls be involved?
5. What happens if the switch does not learn PC2's MAC address?

### Think first — solution

The router is not necessarily the problem because both hosts are supposed to communicate locally.

Start with:

```cisco
show interfaces status
show mac address-table
show interfaces
```

Then check VLAN membership, host NICs, cabling, and endpoint firewalls.

If PC2's MAC is not learned, investigate the PC2 link and Layer 2 path first.

---

# 35. Practice Exercises ✍️

### Exercise 1 — Frame analysis

Given:

```text
Source MAC      = AAAA.AAAA.AAAA
Destination MAC = BBBB.BBBB.BBBB
```

Answer:

1. Which device is sending the frame?
2. Which device is the Layer 2 destination?
3. Which address will a switch primarily use to decide where to forward the frame?

### Exercise 2 — Traffic types

Classify each as unicast, broadcast, or multicast:

1. PC sends directly to one server.
2. ARP request.
3. Traffic sent to a subscribed multicast group.

### Exercise 3 — MAC learning

Build a Packet Tracer network and document:

- MAC address of each PC
- Switch port
- MAC table before traffic
- MAC table after ping

### Exercise 4 — Troubleshooting

Create a fault by shutting down a switch port:

```cisco
interface gigabitEthernet 0/1
shutdown
```

Observe the failure and restore it:

```cisco
no shutdown
```

Document what changed.

---

# 36. Interview Questions 🎤

## Beginner

1. What is Ethernet?
2. What is an Ethernet frame?
3. What is a MAC address?
4. What is the size of a traditional Ethernet MAC address?
5. What is unicast?
6. What is broadcast?
7. What is multicast?
8. What is FCS?

## Intermediate

9. How does a switch learn MAC addresses?
10. What happens when a destination MAC is unknown?
11. What is a MAC address table?
12. What is a collision domain?
13. What is a broadcast domain?
14. Why do modern switched Ethernet links normally not have collisions?
15. What is the purpose of full duplex?

## Advanced

16. Explain the difference between a Layer 2 frame and a Layer 3 packet.
17. Why does a host use the gateway's MAC address when sending to a remote IP network?
18. What is CSMA/CD and why is it not required on modern full-duplex Ethernet?
19. What information can you obtain from `show mac address-table`?
20. What is the difference between standard Ethernet frames and jumbo frames?

## Troubleshooting

21. A switch is not learning a host's MAC address. What would you check?
22. A port has increasing CRC errors. What should you investigate?
23. Two hosts are in the same VLAN but cannot communicate. What Layer 1 and Layer 2 checks would you perform?
24. Why might a switch flood an unknown unicast frame?
25. How would you determine whether the problem is Layer 2 or Layer 3?

---

# 37. Quick Revision ⚡

```text
Ethernet
│
├── Layer 1 → physical signaling
│
├── Layer 2 → frames + MAC addressing
│
├── Frame
│   ├── Destination MAC
│   ├── Source MAC
│   ├── Type/Length
│   ├── Payload
│   └── FCS
│
├── Traffic
│   ├── Unicast
│   ├── Broadcast
│   └── Multicast
│
└── Switch
    ├── Learns source MAC
    ├── Checks destination MAC
    ├── Forwards known destination
    └── Floods unknown/broadcast as appropriate
```

### Remember

```text
MAC address → Layer 2
Ethernet frame → Layer 2
IP address → Layer 3
IP packet → Layer 3
```

---

# 38. Cheat Sheet 📌

| Concept | Key point |
|---|---|
| Ethernet | Major wired LAN technology |
| Frame | Layer 2 Ethernet data unit |
| MAC | Layer 2 address |
| MAC size | 48 bits traditionally |
| Broadcast MAC | `FF:FF:FF:FF:FF:FF` |
| Switch learning | Uses source MAC |
| Known unicast | Forward to learned destination port |
| Unknown unicast | Flood within relevant Layer 2 scope |
| Broadcast | Flood within relevant broadcast domain |
| Collision domain | Area where collisions can occur |
| Broadcast domain | Area receiving a Layer 2 broadcast |
| Full duplex | Send and receive simultaneously |
| CSMA/CD | Historical shared/half-duplex Ethernet mechanism |
| FCS | Detects frame corruption |
| Standard Ethernet frame | 64–1518 bytes, excluding preamble/SFD |

---

# 39. Connection to the Next Topic 🔗

You now understand:

**Network → devices → cables → Ethernet → frames → MAC addresses → switching behavior.**

The next topic focuses specifically on the address that makes Layer 2 Ethernet communication possible:

➡️ **07 — MAC Addressing**

You will go deeper into:

- MAC address structure
- OUI
- Unicast/multicast/broadcast MAC addresses
- Locally administered addresses
- MAC address tables
- Static vs dynamic MAC entries
- MAC learning and aging
- MAC address troubleshooting

This will prepare you for **switching and VLANs**.

---

# ✅ Completion Checklist

- [ ] I understand what Ethernet is.
- [ ] I understand Layer 1 vs Layer 2 Ethernet concepts.
- [ ] I understand the basic Ethernet frame structure.
- [ ] I understand source and destination MAC addresses.
- [ ] I understand unicast, broadcast, and multicast.
- [ ] I understand how a switch learns MAC addresses.
- [ ] I understand known vs unknown unicast forwarding.
- [ ] I understand collision domains.
- [ ] I understand broadcast domains.
- [ ] I understand full-duplex Ethernet.
- [ ] I understand the historical role of CSMA/CD.
- [ ] I understand FCS and CRC errors at a basic level.
- [ ] I understand standard Ethernet frame size.
- [ ] I can use `show mac address-table`.
- [ ] I can inspect Ethernet interface errors.
- [ ] I completed the Packet Tracer labs.
- [ ] I can explain how Ethernet and IP work together.

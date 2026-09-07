# 🏷️ MAC Addressing

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what a MAC address is and why Ethernet uses it.
- Understand the structure and size of a traditional MAC address.
- Differentiate unicast, multicast, and broadcast MAC addresses.
- Understand globally administered and locally administered MAC addresses.
- Explain how switches learn, store, age, and use MAC addresses.
- Understand dynamic and static MAC entries.
- Relate MAC addressing to ARP and Ethernet forwarding.
- Use Cisco IOS commands to inspect MAC-address information.
- Troubleshoot common MAC-address and Layer 2 problems.

---

# 1. What Is a MAC Address?

**MAC** stands for **Media Access Control**.

A MAC address is a Layer 2 address associated with a network interface and is used for communication on Ethernet and other Layer 2 technologies that use MAC addressing.

Example:

```text
00:1A:2B:3C:4D:5E
```

Cisco commonly displays MAC addresses like:

```text
001A.2B3C.4D5E
```

A traditional Ethernet MAC address is **48 bits (6 bytes)**.

```text
48 bits
  ↓
6 bytes
  ↓
12 hexadecimal digits
```

---

# 2. Why Do We Need MAC Addresses?

Imagine an apartment building.

- 🏢 Building address = IP network
- 🚪 Apartment number = specific host/interface
- 📦 Delivery = Ethernet frame

An IP address helps identify a device at Layer 3, while a MAC address helps identify the Layer 2 interface used for local Ethernet delivery.

A switch primarily uses the **destination MAC address** in an Ethernet frame to decide which interface should receive the frame.

---

# 3. MAC Address Representation

The same 48-bit address can be written in different formats.

### Colon notation

```text
00:1A:2B:3C:4D:5E
```

### Hyphen notation

```text
00-1A-2B-3C-4D-5E
```

### Cisco notation

```text
001A.2B3C.4D5E
```

These represent the same underlying 48-bit value.

---

# 4. Hexadecimal Refresher 🔢

MAC addresses are commonly written in hexadecimal.

Hexadecimal uses:

```text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

Each hexadecimal digit represents **4 bits**.

Therefore:

```text
12 hexadecimal digits × 4 bits
= 48 bits
```

Example:

```text
A = 1010
F = 1111
```

You do not need to convert every MAC address to binary manually, but understanding hexadecimal will help throughout networking.

---

# 5. MAC Address Structure

A traditional MAC address can be conceptually viewed as:

```text
00:1A:2B | 3C:4D:5E
   ↓             ↓
Organization   Interface-specific
identifier     portion
```

The first 24 bits are commonly associated with an **OUI (Organizationally Unique Identifier)** assigned through the IEEE registration system.

The remaining bits are used according to the organization's addressing scheme.

### Important modern consideration

Not every MAC address should be assumed to be globally unique and permanently tied to a manufacturer.

Devices can use **locally administered MAC addresses**, and modern operating systems may randomize MAC addresses for privacy, especially on Wi-Fi.

---

# 6. OUI — Organizationally Unique Identifier

An **OUI** is a globally assigned identifier associated with an organization for certain IEEE-defined identifiers.

For a traditional MAC address, the first 24 bits are commonly used as the OUI portion.

Example:

```text
00:1A:2B:3C:4D:5E
^^^^^^^^
OUI portion
```

> Do not rely on OUI alone to identify the physical manufacturer of every modern device because locally administered and virtual/randomized MAC addresses exist.

---

# 7. Unicast MAC Address 👤

A **unicast MAC address** identifies a single destination interface.

Example:

```text
00:11:22:33:44:55
```

A unicast Ethernet frame is intended for one Layer 2 destination.

```text
PC1 ─────────→ PC2
```

A switch can normally forward a known unicast frame to the interface associated with that destination MAC.

---

# 8. Broadcast MAC Address 📢

The Ethernet broadcast destination is:

```text
FF:FF:FF:FF:FF:FF
```

It means:

> Deliver this Layer 2 broadcast to all devices in the relevant broadcast domain.

Example:

```text
              PC2
               ↑
               |
PC1 ──────── [SW] ───── PC3
               |
               ↓
              PC4
```

A switch floods a broadcast within the applicable VLAN/broadcast domain, excluding the port where the frame arrived.

---

# 9. Multicast MAC Address 👥

A multicast destination represents a group of receivers.

```text
Sender
  |
  +──→ Receiver A
  +──→ Receiver B
  +──→ Receiver C
```

IPv4 and IPv6 multicast use defined mappings into Ethernet multicast MAC addresses.

For example, IPv4 multicast MAC addresses commonly begin with:

```text
01:00:5E
```

IPv6 multicast Ethernet addresses commonly begin with:

```text
33:33
```

The complete mapping rules are more detailed and will be covered when multicast and IPv6 are studied.

---

# 10. How to Recognize MAC Address Type

Two important bits in the first octet help classify MAC addresses.

### I/G bit

The least significant bit of the first octet indicates:

```text
0 → Individual / unicast
1 → Group / multicast
```

The broadcast address is a special group address:

```text
FF:FF:FF:FF:FF:FF
```

### U/L bit

The second least significant bit of the first octet indicates whether the address is:

```text
0 → Universally administered
1 → Locally administered
```

You do not need to calculate these bits for every address, but understanding them helps explain modern MAC addressing.

---

# 11. Globally Administered vs Locally Administered MAC

## Globally administered

A MAC address assigned according to a globally coordinated addressing scheme.

## Locally administered

A MAC address intentionally assigned for local use by a device, administrator, virtualization platform, or operating system.

Common reasons include:

- Virtual machines
- Containers
- Network virtualization
- Wi-Fi privacy/randomization
- Specialized network configurations

### Important

> A MAC address beginning with a locally administered first octet should not automatically be treated as a vendor-assigned hardware identity.

---

# 12. MAC Address vs IP Address 🧠

| Feature | MAC Address | IP Address |
|---|---|---|
| Main layer | Layer 2 | Layer 3 |
| Used by | Ethernet/L2 technologies | IP networking |
| Typical purpose | Local frame delivery | Logical network addressing/routing |
| Format | Hexadecimal | IPv4 decimal or IPv6 hexadecimal |
| Example | `001A.2B3C.4D5E` | `192.168.1.10` |
| Switch uses it? | Yes | Not for basic L2 forwarding |
| Router uses it? | Present on local interfaces/frames | Yes, for routing decisions |

### Simple rule

```text
MAC → local Layer 2 delivery
IP  → Layer 3 logical addressing
```

This is a simplified model, but it is extremely useful for CCNA learning.

---

# 13. Does a MAC Address Travel Across the Internet?

Not as the same Layer 2 destination information end-to-end.

Consider:

```text
PC1 → Switch → Router → Internet → Web Server
```

The Ethernet frame is local to a Layer 2 segment.

When a router forwards an IP packet to another network, it creates a new Layer 2 frame appropriate for the next link.

Conceptually:

```text
PC1 LAN
Src MAC = PC1
Dst MAC = Gateway
       ↓
     Router
       ↓
Next LAN
Src MAC = Router interface
Dst MAC = Next-hop device
```

The **IP packet** is routed across networks, while the **Layer 2 frame/MAC addresses** are rewritten for each local link.

---

# 14. MAC Address and ARP 🔍

IPv4 uses **ARP (Address Resolution Protocol)** to discover a MAC address associated with a local IPv4 address.

Suppose PC1 knows:

```text
Destination IP = 192.168.1.20
```

But it does not know the destination MAC.

It sends an ARP request similar to:

> Who has 192.168.1.20?

The request is sent as an Ethernet broadcast.

The destination responds with its MAC address.

Conceptually:

```text
IPv4 address
     ↓
    ARP
     ↓
MAC address
     ↓
Ethernet frame
```

---

# 15. ARP Cache vs MAC Address Table

Do not confuse these two tables.

### Host ARP cache

Maps:

```text
IPv4 address → MAC address
```

Example:

```text
192.168.1.20 → BBBB.BBBB.BBBB
```

### Switch MAC address table

Maps approximately:

```text
MAC address → switch interface
```

Example:

```text
BBBB.BBBB.BBBB → Gi0/2
```

### Combined picture

```text
PC1
ARP cache:
192.168.1.20 → BBBB.BBBB.BBBB
                  |
                  v
                [SW]
                  |
            Gi0/2 → BBBB.BBBB.BBBB
```

The host and switch use different information for different Layer 2 functions.

---

# 16. Switch MAC Address Learning 🔀

A switch learns MAC addresses from the **source MAC address** of incoming frames.

Example:

```text
PC1
MAC = AAAA.AAAA.AAAA
   |
   | incoming frame
   v
SW1 Gi0/1
```

SW1 learns:

```text
AAAA.AAAA.AAAA → Gi0/1
```

If PC2 sends on Gi0/2:

```text
BBBB.BBBB.BBBB → Gi0/2
```

The switch now knows where both devices are located.

---

# 17. Destination MAC Lookup

After learning the source, the switch examines the destination MAC.

### Known destination

```text
Destination = BBBB.BBBB.BBBB
MAC table:
BBBB.BBBB.BBBB → Gi0/2
```

➡️ Forward toward Gi0/2.

### Unknown destination

If the destination is not in the table:

➡️ Flood the frame within the relevant VLAN, excluding the receiving port.

### Broadcast

```text
FF:FF:FF:FF:FF:FF
```

➡️ Flood within the relevant broadcast domain.

---

# 18. Dynamic MAC Entries ⏳

Most switch MAC entries are learned dynamically.

Example:

```text
Vlan    Mac Address       Type       Ports
----    -----------       --------   -----
10      aaaa.aaaa.aaaa   DYNAMIC    Gi0/1
10      bbbb.bbbb.bbbb   DYNAMIC    Gi0/2
```

Dynamic entries can age out when the switch does not receive traffic from the corresponding source for the configured aging period.

This allows the switch to adapt when devices move or disappear.

---

# 19. Static MAC Entries

A static MAC entry can be configured so a MAC address is associated with a specific interface/VLAN according to the platform's supported configuration.

Conceptually:

```text
MAC → VLAN + interface
```

Static entries can be useful in controlled designs, but they require administration and can become incorrect when a device or topology changes.

### Important

Static MAC configuration is different from port security. Port security can use secure MAC-address learning and additional violation controls.

Port security is covered later in the Network Security section.

---

# 20. MAC Address Table Commands 🔎

## View the MAC table

```cisco
show mac address-table
```

### Dynamic entries

```cisco
show mac address-table dynamic
```

### Filter by interface

On supported platforms/syntax:

```cisco
show mac address-table interface gigabitEthernet 0/1
```

### Filter by VLAN

```cisco
show mac address-table vlan 10
```

Exact filtering syntax can vary by Cisco platform and IOS/IOS XE release. Use `?` for available options.

---

# 21. Clear Dynamic MAC Entries

On supported Cisco platforms:

```cisco
clear mac address-table dynamic
```

This is useful in labs when you want to observe the switch relearning MAC addresses.

> Be careful when clearing learned state on a production switch. Understand the operational effect before using clearing commands.

---

# 22. MAC Address Table Aging

A simplified learning sequence is:

```text
Frame arrives
     ↓
Read source MAC
     ↓
Learn/update MAC → interface
     ↓
Read destination MAC
     ↓
Known? ── Yes → forward
  |
  No
  ↓
Flood within VLAN
```

If a dynamic source is not seen for the aging period:

```text
MAC entry
   ↓
Ages out
   ↓
Removed from dynamic table
```

When traffic returns, the switch can learn the address again.

---

# 23. MAC Flapping ⚠️

**MAC flapping** occurs when a switch repeatedly learns the same MAC address on different interfaces.

Example:

```text
AAAA.AAAA.AAAA → Gi0/1
AAAA.AAAA.AAAA → Gi0/2
AAAA.AAAA.AAAA → Gi0/1
AAAA.AAAA.AAAA → Gi0/2
```

Possible causes include:

- Layer 2 loops
- Incorrect cabling
- Redundant paths
- Misconfigured virtualization/network bridging
- A device appearing through multiple paths

MAC flapping is an important troubleshooting clue.

---

# 24. MAC Address Movement

A legitimate device can move from one switch port to another.

Example:

```text
Before:
Laptop MAC → Gi0/5

After moving cable:
Laptop MAC → Gi0/10
```

The switch should update its learned location when it receives traffic from that MAC on the new port.

Therefore:

> A MAC address changing ports is not automatically a network attack or a failure. Investigate the context.

---

# 25. MAC Flooding Attack Concept 🔐

A Layer 2 switch has finite resources for storing learned MAC addresses.

An attacker could attempt to generate many source MAC addresses to exhaust the switch's MAC-learning resources.

This is commonly called a **MAC flooding / CAM table exhaustion attack**.

Possible defensive controls include:

- Port security
- Limiting learned MAC addresses
- Monitoring switch behavior
- Proper access-layer security

> This is an awareness-level security concept here. Detailed defensive configuration belongs in the Network Security module.

---

# 26. MAC Spoofing Concept 🔐

A device can sometimes change the MAC address presented by its network interface.

Possible legitimate uses include:

- Privacy/randomization
- Testing
- Virtualization
- Compatibility scenarios

It can also be abused for impersonation or bypassing poorly designed controls.

Therefore, do not treat a MAC address alone as a permanent identity or strong authentication mechanism.

---

# 27. Cisco MAC Troubleshooting Flow 🔧

When a host cannot communicate at Layer 2:

### Step 1 — Check interface

```cisco
show interfaces status
```

### Step 2 — Check the MAC table

```cisco
show mac address-table
```

Ask:

> Is the expected MAC address present?

### Step 3 — Check the expected port

```cisco
show mac address-table interface gigabitEthernet 0/1
```

### Step 4 — Check VLAN

On supported switches:

```cisco
show vlan brief
```

### Step 5 — Check physical errors

```cisco
show interfaces gigabitEthernet 0/1
```

### Step 6 — Check neighboring devices

```cisco
show cdp neighbors
show lldp neighbors
```

### Step 7 — Continue to Layer 3

If Layer 2 is working, check:

```cisco
show arp
show ip route
```

---

# 28. Mini Lab — Watch MAC Learning 🧪

## Topology

```text
PC1 ───┐
PC2 ───┼── [SW1]
PC3 ───┘
```

## Tasks

1. Connect all PCs to SW1.
2. Configure IP addresses in the same subnet.
3. Before generating traffic, check:

```cisco
show mac address-table
```

4. Ping between PCs.
5. Check the MAC table again.
6. Identify which MAC address belongs to which switch port.

### Expected learning

You should see dynamic MAC entries appear after traffic is generated.

---

# 29. Mini Lab — MAC Movement

1. Connect PC1 to Gi0/1.
2. Generate traffic.
3. Verify:

```cisco
show mac address-table
```

4. Move PC1 to another switch port.
5. Generate traffic again.
6. Check the table.

### Question

Why did the MAC address move to another interface?

### Answer

The switch observed frames from the source MAC arriving on the new interface and updated its learned location.

---

# 30. Mini Lab — Unknown Unicast

Create:

```text
PC1 ───┐
PC2 ───┼── SW1
PC3 ───┘
```

Clear dynamic MAC entries if supported:

```cisco
clear mac address-table dynamic
```

Then generate traffic and observe:

```cisco
show mac address-table
```

### Challenge

Explain why the first frame may be flooded but later frames can be forwarded selectively.

---

# 31. Real-World Scenario 🏢

An office user says:

> “My laptop works when connected to one switch port but not another.”

The network engineer checks:

```cisco
show interfaces status
show mac address-table
show vlan brief
```

The MAC address is learned on the new port, but that port belongs to a different VLAN.

### Lesson

MAC learning can be completely correct while communication still fails because the **Layer 2 VLAN context** is different.

This is why troubleshooting must consider both:

```text
MAC address + VLAN
```

not the MAC address alone.

---

# 32. Scenario Challenge 🧠

### Problem

A switch log repeatedly indicates that a host's MAC address is being learned on two different interfaces.

```text
AAAA.AAAA.AAAA → Gi0/1
AAAA.AAAA.AAAA → Gi0/2
```

### Questions

1. What is this behavior commonly called?
2. Could a Layer 2 loop cause it?
3. Could incorrect cabling cause it?
4. Could virtualization or bridging cause it?
5. Which commands would you use to investigate?

### Think first — solution

This looks like **MAC flapping**.

Start with:

```cisco
show mac address-table
show mac address-table dynamic
show cdp neighbors
show lldp neighbors
show spanning-tree
```

Then inspect the physical topology and relevant switch interfaces.

---

# 33. Common Beginner Mistakes ❌

### Mistake 1: “MAC address = IP address.”

They are different addressing systems used at different networking layers.

### Mistake 2: “A MAC address always permanently identifies the physical device.”

Virtualization, local administration, and MAC randomization can change this assumption.

### Mistake 3: “The switch learns destination MAC addresses.”

The switch learns primarily from **source MAC addresses** and then looks up the destination.

### Mistake 4: “Unknown unicast means the frame is dropped.”

A Layer 2 switch normally floods an unknown unicast within the relevant VLAN rather than simply dropping it.

### Mistake 5: “The MAC table maps IP addresses to ports.”

The MAC table maps MAC addresses to Layer 2 forwarding information such as VLAN and interface.

### Mistake 6: “MAC flapping always means an attack.”

Loops, cabling, legitimate mobility, virtualization, and other topology problems can also cause it.

### Mistake 7: “A correct MAC table guarantees connectivity.”

VLANs, IP addressing, ACLs, routing, and endpoint security can still prevent communication.

---

# 34. Practice Exercises ✍️

### Exercise 1 — Address identification

Classify:

```text
AA:BB:CC:DD:EE:FF
FF:FF:FF:FF:FF:FF
01:00:5E:00:00:01
```

Determine whether each is a likely unicast, broadcast, or multicast destination.

### Exercise 2 — Table comparison

Create two tables:

```text
ARP cache:
IP → MAC

Switch MAC table:
MAC → VLAN/interface
```

Explain why both are needed.

### Exercise 3 — MAC learning

Build a three-PC Packet Tracer topology and document:

- PC MAC addresses
- Switch ports
- MAC table before traffic
- MAC table after traffic
- MAC table after moving one PC

### Exercise 4 — Troubleshooting

Create a topology where two redundant Layer 2 links exist and investigate:

```cisco
show mac address-table
show spanning-tree
```

Look for evidence of normal STP behavior or unexpected MAC movement.

---

# 35. Interview Questions 🎤

## Beginner

1. What is a MAC address?
2. How many bits are in a traditional Ethernet MAC address?
3. What is the broadcast MAC address?
4. What is a unicast MAC address?
5. What is a multicast MAC address?
6. What is an OUI?
7. What is the difference between MAC and IP addressing?

## Intermediate

8. How does a switch learn MAC addresses?
9. What happens when a switch receives a frame for an unknown MAC address?
10. What is MAC address aging?
11. What is the difference between an ARP cache and a MAC address table?
12. What is a locally administered MAC address?
13. Why does a router use a different Layer 2 frame on its next link?

## Advanced

14. What do the I/G and U/L bits indicate?
15. Why can a MAC address move between switch ports?
16. What is MAC flapping?
17. How can a Layer 2 loop produce MAC flapping?
18. What is a CAM table exhaustion attack?
19. Why should a MAC address not be treated as a strong permanent identity?

## Troubleshooting

20. A switch does not learn a host MAC. What do you check?
21. A MAC address appears on the wrong interface. What could cause it?
22. A MAC repeatedly moves between two ports. What commands would you use?
23. The MAC is learned correctly, but the host still cannot communicate. What should you investigate next?
24. How would you distinguish a MAC-learning problem from an IP/routing problem?

---

# 36. Quick Revision ⚡

```text
MAC Address
│
├── Layer 2 address
├── Traditional Ethernet size = 48 bits
├── Written in hexadecimal
│
├── Traffic types
│   ├── Unicast → one destination
│   ├── Multicast → group
│   └── Broadcast → FF:FF:FF:FF:FF:FF
│
├── Switch
│   ├── Learns SOURCE MAC
│   ├── Looks up DESTINATION MAC
│   ├── Known → forward
│   └── Unknown → flood within VLAN
│
└── Troubleshooting
    ├── MAC table
    ├── VLAN
    ├── Interface
    ├── CDP/LLDP
    └── STP
```

---

# 37. Cheat Sheet 📌

| Concept | Key point |
|---|---|
| MAC | Layer 2 address |
| Traditional MAC size | 48 bits / 6 bytes |
| Cisco format | `001A.2B3C.4D5E` |
| Broadcast MAC | `FF:FF:FF:FF:FF:FF` |
| OUI | First 24 bits in traditional globally administered MAC structure |
| Unicast | One destination |
| Multicast | Group destination |
| Switch learning | Source MAC |
| Destination lookup | Destination MAC |
| Unknown unicast | Flood within relevant VLAN |
| Dynamic MAC | Learned automatically |
| Static MAC | Manually configured/installed |
| MAC aging | Removes stale dynamic entries |
| MAC flapping | Same MAC learned on different interfaces repeatedly |
| ARP cache | IP → MAC |
| MAC table | MAC → VLAN/interface |
| Locally administered | Address assigned for local use |

---

# 38. Connection to the Next Topic 🔗

You now understand the identity used for **Layer 2 Ethernet delivery**.

Next, we move from individual MAC addresses to the device that uses them to make forwarding decisions:

➡️ **08 — Switching Fundamentals**

You will learn:

- How a switch processes frames
- MAC address learning
- Forwarding and flooding
- Frame filtering
- Switching methods
- CAM/MAC table concepts
- Broadcast handling
- Basic switch configuration
- Verification and troubleshooting

This is the foundation for the major CCNA switching topics that follow:

**VLANs → Trunking → STP → EtherChannel → Inter-VLAN Routing**.

---

# ✅ Completion Checklist

- [ ] I understand what a MAC address is.
- [ ] I know a traditional Ethernet MAC address is 48 bits.
- [ ] I can read common MAC address formats.
- [ ] I understand hexadecimal representation.
- [ ] I understand OUI at a basic level.
- [ ] I can identify unicast, multicast, and broadcast concepts.
- [ ] I know the Ethernet broadcast MAC address.
- [ ] I understand globally and locally administered MAC addresses.
- [ ] I understand MAC vs IP addressing.
- [ ] I understand MAC learning.
- [ ] I understand unknown-unicast flooding.
- [ ] I understand dynamic MAC aging.
- [ ] I understand MAC movement and MAC flapping.
- [ ] I understand ARP cache vs MAC address table.
- [ ] I can use `show mac address-table`.
- [ ] I can investigate MAC-related Layer 2 problems.
- [ ] I completed the Packet Tracer labs.

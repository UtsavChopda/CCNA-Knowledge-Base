# 14. Trunking Fundamentals

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

---

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain why trunking is required between network devices when multiple VLANs must cross one link.
- Understand access ports vs trunk ports.
- Explain IEEE 802.1Q VLAN tagging.
- Understand tagged and untagged Ethernet traffic.
- Explain the native VLAN.
- Understand allowed VLANs.
- Configure and verify Cisco trunk ports.
- Troubleshoot common trunk problems.
- Understand trunking in multi-switch and router-on-a-stick topologies.

---

# 1. What Is Trunking?

A **trunk** is a network link that carries traffic for **multiple VLANs** over one physical connection.

Imagine two switches:

```text
VLAN 10 ──┐
VLAN 20 ──┼── SW1 ═════════ SW2 ──┼── VLAN 10
VLAN 30 ──┘                       └── VLAN 20/30
```

Without trunking, you would need separate physical links for every VLAN.

A trunk allows multiple VLANs to share one physical link while keeping their Layer 2 identities separate.

---

# 2. Why Do We Need Trunks?

Suppose a company has three VLANs:

```text
VLAN 10 → USERS
VLAN 20 → VOICE
VLAN 30 → SERVERS
```

There are two switches:

```text
SW1                    SW2
V10                    V10
V20  ════════════════   V20
V30                    V30
```

All three VLANs need to communicate across the switches.

A trunk allows one inter-switch link to carry all three VLANs.

### Without a trunk

```text
SW1                 SW2
VLAN10 ──────────── VLAN10
VLAN20 ──────────── VLAN20
VLAN30 ──────────── VLAN30
```

Multiple physical links would be required.

### With a trunk

```text
SW1 ═════════════════ SW2
      VLAN 10
      VLAN 20
      VLAN 30
```

One physical link can carry multiple VLANs.

---

# 3. Access Port vs Trunk Port

| Feature | Access Port | Trunk Port |
|---|---|---|
| Typical use | End device | Switch-to-switch/device link |
| Typical VLANs | One | Multiple |
| VLAN tagging | Normal endpoint traffic is typically untagged | 802.1Q identifies VLANs on the link |
| Common devices | PC, printer, camera | Switch, router, some APs |
| Main purpose | Connect device to one VLAN | Transport multiple VLANs |

### Easy memory trick 🧠

```text
ACCESS = one VLAN
TRUNK  = multiple VLANs
```

This is a useful CCNA shortcut, while remembering that special device designs can have more nuanced behavior.

---

# 4. Trunk Analogy 🚆

Imagine a railway trunk route carrying different trains:

```text
Main railway line
════════════════════════════
  Train A → City A
  Train B → City B
  Train C → City C
```

One physical railway carries multiple logical services.

Similarly:

```text
One physical Ethernet trunk
════════════════════════════
VLAN 10 traffic
VLAN 20 traffic
VLAN 30 traffic
```

The VLAN information lets the receiving switch determine which logical network each frame belongs to.

---

# 5. IEEE 802.1Q

The standard commonly used for VLAN tagging on Ethernet trunks is **IEEE 802.1Q**.

The VLAN tag contains information that allows the receiving device to identify the VLAN associated with the frame.

Conceptually:

```text
Ethernet Frame
┌────────┬────────┬─────────┬──────────┐
│ Dest   │ Source │ 802.1Q  │ Payload  │
│ MAC    │ MAC    │ Tag     │          │
└────────┴────────┴─────────┴──────────┘
```

The 802.1Q tag is inserted into the Ethernet frame between the source MAC address and the EtherType/length field.

---

# 6. What Information Is in an 802.1Q Tag?

At a conceptual CCNA level, know that the tag includes:

- VLAN identification information
- Priority information used by 802.1p
- Drop Eligible Indicator (DEI)
- Tag Protocol Identifier (TPID)

The **VLAN ID** is the key field for identifying the VLAN.

The VLAN ID field is **12 bits**, supporting VLAN IDs in the range **0–4095**, although not every value represents a normal usable VLAN. Common usable VLAN IDs are 1–4094, with special/reserved values having specific meanings.

---

# 7. Tagged vs Untagged Traffic

On a typical access port:

```text
PC ─── Access Port ─── SW
       VLAN 10
```

The PC normally sends ordinary Ethernet frames without an 802.1Q VLAN tag.

The switch associates those frames with VLAN 10 based on the access-port configuration.

On a trunk:

```text
SW1 ═════════════ SW2
      tagged VLAN traffic
```

The trunk can use 802.1Q tagging to identify VLANs.

---

# 8. Native VLAN

The **native VLAN** is the VLAN associated with **untagged traffic on an 802.1Q trunk**.

This is a critical concept.

Example:

```text
SW1 ═════════════ SW2
       trunk
       VLAN10
       VLAN20
       VLAN30
       Native VLAN 99
```

If a frame is sent untagged over the trunk, the receiving side associates it with the native VLAN.

### Important

Both sides of a trunk should normally use the same native VLAN to avoid unexpected behavior and security problems.

---

# 9. Native VLAN Analogy

Imagine a bus route carrying passengers from several groups.

Most passengers carry colored tickets:

```text
Red ticket   → VLAN 10
Blue ticket  → VLAN 20
Green ticket → VLAN 30
```

But some passengers have no ticket color.

The bus system needs a default category for those passengers.

That concept is similar to the native VLAN for untagged trunk traffic.

---

# 10. Native VLAN Mismatch

Suppose:

```text
SW1 native VLAN = 99
SW2 native VLAN = 10
```

The trunk endpoints disagree about which VLAN should receive untagged traffic.

This can cause:

- CDP/native VLAN mismatch messages on Cisco devices
- Unexpected traffic placement
- Connectivity problems
- Security concerns

### Verify

```cisco
show interfaces trunk
```

Check the native VLAN on both sides.

---

# 11. Allowed VLANs

A trunk does not always need to carry every VLAN.

You can restrict the VLANs allowed on the trunk.

Example:

```text
Trunk
├── VLAN 10 ✓
├── VLAN 20 ✓
├── VLAN 30 ✗
└── VLAN 40 ✗
```

This can reduce unnecessary traffic and improve segmentation.

---

# 12. Configure Allowed VLANs

Cisco IOS example:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
```

Now the trunk is configured to allow VLANs 10 and 20.

You can also modify the list:

```cisco
switchport trunk allowed vlan add 30
```

Remove a VLAN:

```cisco
switchport trunk allowed vlan remove 20
```

Set a specific list:

```cisco
switchport trunk allowed vlan 10,20,30
```

> Exact command availability can vary by Cisco platform/software version. Verify the platform syntax when working on physical equipment.

---

# 13. Native VLAN Configuration

Example:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk native vlan 99
```

Verify:

```cisco
show interfaces trunk
```

### Important

If you intentionally use native VLAN 99, configure the expected native VLAN consistently on both ends of the trunk.

---

# 14. Basic Cisco Trunk Configuration

Suppose SW1 G0/1 connects to SW2 G0/1.

### SW1

```cisco
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
end
```

### SW2

```cisco
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
end
```

Verify:

```cisco
show interfaces trunk
```

---

# 15. Trunk Encapsulation and Older Cisco Switches

Some older Cisco platforms support multiple trunk encapsulation choices, such as ISL and 802.1Q.

Modern Cisco platforms commonly use 802.1Q.

On some older IOS platforms you may encounter:

```cisco
switchport trunk encapsulation dot1q
```

Then:

```cisco
switchport mode trunk
```

However, many modern switches do not support or require the encapsulation command because 802.1Q is the only available trunking method.

### Common lab error

You may see:

```text
Command rejected: An interface whose trunk encapsulation is "Auto"
can not be configured to "trunk" mode.
```

On platforms that support the command, configure:

```cisco
switchport trunk encapsulation dot1q
switchport mode trunk
```

This is especially useful to understand when working with older Packet Tracer switch models.

---

# 16. Verify Trunk Status

The most important command is:

```cisco
show interfaces trunk
```

It can show:

- Port
- Trunk status
- Encapsulation
- Native VLAN
- Allowed VLANs
- Active VLANs
- VLANs forwarding over the trunk

Example concept:

```text
Port    Mode    Encapsulation    Status
Gi0/1   on      802.1q           trunking
```

---

# 17. Verify Switchport Details

Use:

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

Look for:

- Administrative mode
- Operational mode
- Administrative trunking encapsulation
- Operational trunking encapsulation
- Access VLAN
- Native VLAN
- Trunk VLANs

This is one of the best commands for diagnosing a port that is not operating as expected.

---

# 18. Check VLAN Existence

A trunk can be configured correctly while the required VLAN does not exist locally.

Check:

```cisco
show vlan brief
```

For a multi-switch design, verify important VLANs on both switches.

---

# 19. Check MAC Addresses by VLAN

Use:

```cisco
show mac address-table vlan 10
```

This can help determine whether the switch is learning MAC addresses for VLAN 10 and where those addresses are being learned.

---

# 20. Check STP for a VLAN

```cisco
show spanning-tree vlan 10
```

This helps identify:

- Root bridge
- Port roles
- Forwarding/blocking state
- STP topology for the VLAN

STP becomes especially important when multiple trunk links create redundancy.

---

# 21. Trunk Topology 1 — Two Switches, One VLAN

```text
PC1 ── SW1 ═════════ SW2 ── PC2
      VLAN10          VLAN10
```

Even though the link can be configured as a trunk, if only one VLAN is needed, a trunk may not be necessary for this simple design.

This helps reinforce:

> A trunk is useful when a link needs to carry multiple VLANs.

---

# 22. Trunk Topology 2 — Two Switches, Multiple VLANs

```text
             TRUNK
SW1 ═══════════════════ SW2
 │                      │
 ├─ VLAN10               ├─ VLAN10
 ├─ VLAN20               ├─ VLAN20
 └─ VLAN30               └─ VLAN30
```

This is the classic inter-switch trunk topology.

---

# 23. Trunk Topology 3 — Access Devices + Trunk

```text
PC1 ─ Access ─┐
               SW1 ═════════ SW2
PC2 ─ Access ─┘      TRUNK
```

Example:

```text
PC1 → VLAN10
PC2 → VLAN20
SW1 ↔ SW2 → VLAN10 + VLAN20
```

The access ports connect endpoints to individual VLANs.

The trunk transports both VLANs between switches.

---

# 24. Trunk Topology 4 — Multiple Switches

```text
             SW1
           ╱     ╲
       trunk     trunk
         ╱         ╲
       SW2 ═══════ SW3
            trunk
```

Multiple VLANs may be transported through multiple trunk links.

This design also introduces Layer 2 redundancy, so STP becomes important.

---

# 25. Trunk Topology 5 — Router-on-a-Stick

```text
             R1
             │
          802.1Q trunk
             │
             SW1
          ┌──┼──┐
        V10 V20 V30
```

One physical router interface can use multiple logical subinterfaces.

Example concept:

```text
R1 G0/0.10 → VLAN 10
R1 G0/0.20 → VLAN 20
R1 G0/0.30 → VLAN 30
```

This is called **router-on-a-stick** and will be covered in detail with inter-VLAN routing later.

---

# 26. Trunk Topology 6 — Switch + Wireless AP

Some enterprise access points use VLANs to carry multiple wireless networks.

```text
                 AP
                 │
              TRUNK
                 │
                SW1
             ┌───┴───┐
          Corp SSID  Guest SSID
            VLAN10      VLAN20
```

The exact AP configuration depends on the platform, but the concept is important:

> One physical link can carry multiple logical VLANs.

---

# 27. Practical Lab 1 — Basic Two-Switch Trunk

### Objective

Configure a trunk between two Cisco switches.

### Topology

```text
PC1 ── SW1 ═════════ SW2 ── PC2
            G0/1
```

### Tasks

On both switches:

```cisco
configure terminal
interface g0/1
 switchport mode trunk
end
```

Verify:

```cisco
show interfaces trunk
```

### Expected result

G0/1 should operate as a trunk.

---

# 28. Practical Lab 2 — Carry VLAN 10 and VLAN 20

### Topology

```text
PC1 ── SW1 ═════════ SW2 ── PC3
V10       TRUNK           V10

PC2 ── SW1 ═════════ SW2 ── PC4
V20       TRUNK           V20
```

### Requirements

- VLAN 10 = USERS
- VLAN 20 = FACULTY
- Trunk between SW1 and SW2

### Tasks

1. Create VLAN 10 and 20 on both switches.
2. Assign endpoint ports appropriately.
3. Configure the inter-switch link as a trunk.
4. Allow VLANs 10 and 20.
5. Configure IP addresses in matching subnets.
6. Test same-VLAN communication across the trunk.

### Verify

```cisco
show vlan brief
show interfaces trunk
show mac address-table
```

---

# 29. Practical Lab 3 — Allowed VLAN Troubleshooting

### Problem

VLAN 10 works across SW1 and SW2, but VLAN 20 does not.

Check:

```cisco
show interfaces trunk
```

Suppose the allowed list is:

```text
10
```

VLAN 20 is missing.

### Fix

```cisco
configure terminal
interface g0/1
 switchport trunk allowed vlan add 20
end
```

Verify again:

```cisco
show interfaces trunk
```

---

# 30. Practical Lab 4 — Native VLAN Mismatch

### Scenario

SW1:

```cisco
switchport trunk native vlan 99
```

SW2:

```cisco
switchport trunk native vlan 1
```

### Tasks

1. Verify the trunk.
2. Identify the mismatch.
3. Decide which native VLAN should be used.
4. Configure both sides consistently.
5. Verify again.

Example correction:

```cisco
interface g0/1
 switchport trunk native vlan 99
```

Apply the intended design on both ends.

---

# 31. Practical Lab 5 — Router-on-a-Stick Preview

### Topology

```text
          R1
          │
        trunk
          │
         SW1
       ┌──┴──┐
      V10   V20
      PC1   PC2
```

### Goal

Understand that:

- SW1 keeps VLAN 10 and VLAN 20 separate at Layer 2.
- The trunk carries both VLANs toward R1.
- R1 can route between them using logical subinterfaces.

Detailed router-on-a-stick configuration will be covered in the inter-VLAN routing module.

---

# 32. Troubleshooting Methodology 🔎

When a VLAN works on one switch but not another, use a structured process.

### Step 1 — Physical link

```cisco
show interfaces g0/1
```

### Step 2 — Trunk status

```cisco
show interfaces trunk
```

### Step 3 — Switchport details

```cisco
show interfaces g0/1 switchport
```

### Step 4 — VLAN existence

```cisco
show vlan brief
```

### Step 5 — Allowed VLANs

```cisco
show interfaces trunk
```

### Step 6 — Native VLAN

Check both ends.

### Step 7 — STP

```cisco
show spanning-tree vlan 10
```

### Step 8 — MAC learning

```cisco
show mac address-table vlan 10
```

### Step 9 — End-host addressing

```text
ipconfig /all
```

### Step 10 — Test

```text
ping <destination>
```

---

# 33. Troubleshooting Scenario — Port Is Access Instead of Trunk

### Situation

SW1 and SW2 need to carry VLAN 10 and VLAN 20, but the link is configured as an access port.

### Check

```cisco
show interfaces g0/1 switchport
```

You may see:

```text
Administrative Mode: static access
```

### Fix

```cisco
configure terminal
interface g0/1
 switchport mode trunk
end
```

Then:

```cisco
show interfaces trunk
```

---

# 34. Troubleshooting Scenario — VLAN Missing

### Situation

SW1 has VLAN 10 and VLAN 20.

SW2 only has VLAN 10.

VLAN 20 devices cannot communicate across the switches.

### Check

```cisco
show vlan brief
```

### Fix

Create VLAN 20 on SW2:

```cisco
configure terminal
vlan 20
 name FACULTY
end
```

Then verify the trunk and VLAN state again.

---

# 35. Troubleshooting Scenario — Allowed VLAN List

### Situation

The trunk is up.

VLAN 10 works.

VLAN 20 fails.

### Check

```cisco
show interfaces trunk
```

If VLAN 20 is not allowed, add it:

```cisco
interface g0/1
 switchport trunk allowed vlan add 20
```

---

# 36. Troubleshooting Scenario — Native VLAN Mismatch

### Situation

The switch reports a native VLAN mismatch.

### Check

```cisco
show interfaces trunk
```

Compare both sides.

### Fix

Configure the intended native VLAN consistently.

Example:

```cisco
interface g0/1
 switchport trunk native vlan 99
```

Do this on both ends if VLAN 99 is the intended native VLAN.

---

# 37. Troubleshooting Scenario — Trunk Is Up but VLAN Traffic Fails

Possible causes:

- VLAN missing on one switch
- VLAN not allowed on trunk
- Wrong access-port assignment
- Native VLAN mismatch
- STP blocking path
- Incorrect IP addressing
- Physical errors
- Incorrect trunk configuration

Do not immediately change random commands.

Follow the verification sequence.

---

# 38. Common Beginner Mistakes ❌

### Mistake 1

> “Every switch-to-switch link must be a trunk.”

Not necessarily.

A trunk is required when the link needs to carry multiple VLANs.

### Mistake 2

> “A trunk creates a new VLAN.”

No.

A trunk transports VLAN traffic; it does not inherently create the VLAN.

### Mistake 3

> “Access ports carry multiple VLANs to a PC.”

Normally no.

An access port is normally associated with one VLAN.

### Mistake 4

> “If the trunk is up, every VLAN automatically works.”

No.

Check:

- VLAN existence
- Allowed VLANs
- STP
- Access-port membership
- IP addressing

### Mistake 5

> “Native VLAN means the only VLAN carried by the trunk.”

No.

A trunk can carry many VLANs; the native VLAN specifically handles untagged traffic.

### Mistake 6

> “802.1Q is an IP protocol.”

No.

It is an Ethernet/VLAN tagging mechanism at Layer 2.

---

# 39. Real-World Scenario 🏢

A company has four VLANs:

```text
VLAN 10 → Employees
VLAN 20 → Voice
VLAN 30 → Servers
VLAN 40 → Guests
```

There are three access switches.

```text
             Core/L3
            /       \
          SW1 ═══════ SW2
           ╲           ╱
             ═══ SW3 ═
```

The links between switches may use trunks so the required VLANs can span the access layer.

A network engineer should decide:

- Which VLANs must cross each trunk.
- Which VLAN is native.
- Which VLANs should be allowed.
- Where Layer 3 routing occurs.
- How STP protects redundant Layer 2 paths.

---

# 40. Security Considerations 🔐

Trunk configuration has security implications.

Good practices can include:

- Do not leave unnecessary VLANs allowed on trunks.
- Use an intentional native VLAN design.
- Avoid relying on default settings in production.
- Disable unused ports.
- Use appropriate switchport modes.
- Control which interfaces can form trunks.
- Protect management access.
- Monitor for unexpected VLAN/trunk changes.

A common hardening approach is to make an intended access port explicitly access mode instead of allowing it to negotiate into a trunk unexpectedly.

Example:

```cisco
interface g0/10
 switchport mode access
```

Additional security mechanisms such as DHCP snooping, Dynamic ARP Inspection, and switchport security will be covered later.

---

# 41. Scenario Challenge 🎯

You have:

```text
PC1 ── SW1 ═════════ SW2 ── PC2
VLAN10        TRUNK        VLAN10

PC3 ── SW1 ═════════ SW2 ── PC4
VLAN20        TRUNK        VLAN20
```

PC1 can ping PC2.

PC3 cannot ping PC4.

### Questions

1. Is the trunk necessarily down?
2. Which command checks trunk status?
3. Which command checks allowed VLANs?
4. Which command checks whether VLAN 20 exists?
5. What would happen if VLAN 20 were missing on SW2?
6. Could STP affect forwarding?
7. Which command checks STP for VLAN 20?

### Try first!

<details>
<summary>💡 Reveal the solution</summary>

The trunk may still be working because VLAN 10 succeeds.

Check:

```cisco
show interfaces trunk
show vlan brief
show spanning-tree vlan 20
show interfaces g0/1 switchport
```

Likely causes include VLAN 20 not existing, VLAN 20 not being allowed, or an STP/port configuration issue.

</details>

---

# 42. Practice Exercises ✍️

### Exercise 1

Explain the difference between an access port and a trunk port.

### Exercise 2

Why is 802.1Q required when multiple VLANs cross one Ethernet link?

### Exercise 3

Create VLANs 10 and 20 on two switches.

### Exercise 4

Configure the inter-switch link as a trunk.

### Exercise 5

Allow only VLANs 10 and 20 on the trunk.

### Exercise 6

Set native VLAN 99 on both ends.

### Exercise 7

Use `show interfaces trunk` to verify the complete configuration.

### Exercise 8

Intentionally remove VLAN 20 from the allowed list and troubleshoot the resulting connectivity failure.

---

# 43. Interview Questions 🎤

## Beginner

1. What is a trunk port?
2. Why are trunks used?
3. What is the difference between access and trunk ports?
4. What is 802.1Q?
5. What is a VLAN tag?
6. What is the native VLAN?
7. What command verifies trunk status?

## Intermediate

8. Why can multiple VLANs travel through one trunk?
9. What happens to untagged traffic received on an 802.1Q trunk?
10. What is an allowed VLAN list?
11. Why should both sides of a trunk agree on the native VLAN?
12. How do you configure a Cisco trunk?
13. What command shows detailed switchport information?
14. Why might a VLAN work on one switch but not across a trunk?

## Advanced

15. What fields are present in an 802.1Q tag?
16. Why might an administrator restrict allowed VLANs?
17. What security problems can result from poor trunk configuration?
18. How can STP interact with redundant trunk links?
19. Why can native VLAN mismatches cause unexpected behavior?
20. How does router-on-a-stick use a trunk?

## Troubleshooting

21. A trunk is up but VLAN 20 cannot cross it. What do you check?
22. `show interfaces trunk` shows VLAN 10 but not VLAN 20 in the allowed list. What is the fix?
23. One side uses native VLAN 99 and the other uses VLAN 1. What is wrong?
24. A switch-to-switch link is operating as an access port. What command would you inspect and what change may be required?
25. VLAN 20 exists on both switches and is allowed on the trunk, but traffic still fails. What Layer 2 and Layer 3 checks come next?

---

# 44. Quick Revision ⚡

```text
ACCESS
  ↓
Normally one VLAN
  ↓
Used for endpoints

TRUNK
  ↓
Multiple VLANs
  ↓
Usually 802.1Q
  ↓
Used between network devices
```

```text
802.1Q
  ↓
VLAN tagging
  ↓
Identifies VLAN traffic

Native VLAN
  ↓
Untagged traffic on trunk

Allowed VLANs
  ↓
Controls which VLANs cross trunk
```

### Core commands

```cisco
show interfaces trunk
show interfaces g0/1 switchport
show vlan brief
show mac address-table vlan 10
show spanning-tree vlan 10
```

### Basic configuration

```cisco
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport trunk native vlan 99
```

---

# 45. Cheat Sheet 📋

| Concept | Remember |
|---|---|
| Access port | Normally one VLAN |
| Trunk port | Multiple VLANs |
| 802.1Q | VLAN tagging standard |
| VLAN tag | Identifies VLAN on trunk |
| Native VLAN | Handles untagged trunk traffic |
| Allowed VLANs | VLANs permitted over trunk |
| `show interfaces trunk` | Main trunk verification command |
| `show interfaces ... switchport` | Detailed switchport state |
| `show vlan brief` | VLAN existence/access-port membership |
| `show spanning-tree vlan X` | STP state for VLAN |
| `show mac address-table vlan X` | MAC learning by VLAN |

---

# 46. Connection to Previous Topics 🔗

Your learning path is now:

```text
Ethernet
   ↓
MAC Addressing
   ↓
Switching
   ↓
Collision/Broadcast Domains
   ↓
VLANs
   ↓
TRUNKING ← You are here
   ↓
STP
   ↓
EtherChannel
   ↓
Inter-VLAN Routing
```

You now know:

> **VLANs create logical Layer 2 broadcast domains.**

And:

> **Trunks allow multiple VLANs to travel across one physical link.**

---

# 47. Industry Relevance 💼

Trunking is fundamental to enterprise networks.

You will encounter trunks in:

- Access/distribution networks
- Data centers
- Campus networks
- Router-on-a-stick designs
- Layer 3 switch environments
- Enterprise wireless deployments
- Voice/data networks
- Redundant switch designs

For SOC and network-security work, understanding trunks helps when analyzing:

- VLAN segmentation
- Packet captures
- Network diagrams
- Unexpected VLAN traffic
- Lateral movement between segments
- Switch configuration changes

---

# 48. Completion Checklist ✅

Before moving forward, you should be able to:

- [ ] Define trunking.
- [ ] Explain why trunks are required.
- [ ] Differentiate access and trunk ports.
- [ ] Explain 802.1Q.
- [ ] Explain VLAN tagging.
- [ ] Explain tagged vs untagged traffic.
- [ ] Define native VLAN.
- [ ] Explain native VLAN mismatch.
- [ ] Configure a Cisco trunk.
- [ ] Configure allowed VLANs.
- [ ] Configure a native VLAN.
- [ ] Verify trunk status.
- [ ] Verify switchport details.
- [ ] Verify VLAN existence.
- [ ] Troubleshoot allowed-VLAN problems.
- [ ] Troubleshoot native VLAN mismatch.
- [ ] Explain trunking across multiple switches.
- [ ] Explain the router-on-a-stick concept.
- [ ] Explain why STP matters with redundant trunks.

---

# 🚀 Next Topic

**15 — VLAN Trunking Protocols and Dynamic Trunking Concepts**

We will next examine how Cisco switches can negotiate or manage trunk behavior, including **DTP**, trunk negotiation concepts, common modes, verification, security considerations, and practical troubleshooting.

> 📚 **Study rule:** Don't memorize `switchport mode trunk` alone. First ask: **Which VLANs need to cross this link, why do they need to cross it, and how will the receiving switch know which VLAN each frame belongs to?** Those questions lead directly to 802.1Q, native VLANs, allowed VLANs, and proper trunk design.
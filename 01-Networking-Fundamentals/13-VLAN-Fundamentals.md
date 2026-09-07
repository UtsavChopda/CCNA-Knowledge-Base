# 13. VLAN Fundamentals

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

---

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what a VLAN is and why it is used.
- Understand VLAN IDs and the concept of logical segmentation.
- Explain access ports and VLAN membership.
- Understand how VLANs create separate Layer 2 broadcast domains.
- Distinguish a VLAN from an IP subnet.
- Configure basic VLANs on a Cisco switch.
- Assign access ports to VLANs.
- Verify VLAN configuration.
- Troubleshoot common VLAN problems.
- Understand where VLANs fit into real enterprise networks.

---

# 1. What Is a VLAN?

**VLAN** stands for **Virtual Local Area Network**.

A VLAN is a logical Layer 2 network that allows a physical switch to be divided into multiple separate broadcast domains.

Without VLANs, a switch can place many connected devices into the same Layer 2 broadcast domain.

With VLANs, the same physical switch can logically behave like multiple separate Layer 2 networks.

### Simple idea 🧠

```text
ONE PHYSICAL SWITCH

        SW1
 ┌──────┼──────┐
 │      │      │
VLAN10 VLAN20 VLAN30
```

The switch is physically one device, but logically it contains multiple Layer 2 segments.

---

# 2. Why Do We Need VLANs?

Imagine a college network with:

- Students
- Faculty
- Administration
- Security cameras
- Guest users

If every device belongs to one large Layer 2 network, broadcasts and local traffic can spread much farther than necessary.

A VLAN-based design could be:

| Group | VLAN |
|---|---:|
| Students | 10 |
| Faculty | 20 |
| Administration | 30 |
| CCTV | 40 |
| Guests | 50 |

Now each VLAN forms a separate Layer 2 broadcast domain.

This improves organization, segmentation, and control.

---

# 3. VLAN Analogy 🏢

Think of a large office building.

The building is the **physical switch**.

Different departments occupy separate locked floors:

```text
Building = Switch

Floor 1 = VLAN 10 → Students
Floor 2 = VLAN 20 → Faculty
Floor 3 = VLAN 30 → Admin
```

People on Floor 1 do not automatically communicate with people on Floor 2 simply because they are inside the same building.

Likewise, devices in different VLANs are logically separated at Layer 2.

---

# 4. VLAN = Broadcast Domain

This is one of the most important CCNA concepts:

> **Each VLAN represents a separate Layer 2 broadcast domain.**

Example:

```text
             SW1
       ┌──────┼──────┐
       │      │      │
     VLAN10 VLAN20 VLAN30
       │      │      │
      PC1    PC2    PC3
```

There are:

- VLAN 10 → Broadcast Domain 1
- VLAN 20 → Broadcast Domain 2
- VLAN 30 → Broadcast Domain 3

A broadcast from VLAN 10 does not normally reach VLAN 20 or VLAN 30.

---

# 5. VLAN Is a Layer 2 Concept

VLANs operate primarily at **Layer 2**.

They control which Layer 2 Ethernet frames belong to which logical network.

```text
Application
Transport
Network
Data Link  ← VLAN operates here
Physical
```

Later, we will use Layer 3 routing to allow communication between VLANs.

---

# 6. VLAN IDs

VLANs are identified using a **VLAN ID**.

Examples:

```text
VLAN 10
VLAN 20
VLAN 30
VLAN 100
```

On Cisco switches, VLAN 1 exists by default.

### Important

Do not confuse:

```text
VLAN ID ≠ IP address
```

For example:

```text
VLAN 10
IP subnet: 192.168.10.0/24
```

The numbers are commonly chosen to match for easier administration, but there is no technical requirement that VLAN 10 must use `192.168.10.0/24`.

---

# 7. Common VLAN Categories

You will encounter several VLAN roles in enterprise networks.

### Data VLAN

Carries normal user/application traffic.

### Voice VLAN

Used for IP phone traffic.

### Management VLAN

Used for management access to network devices.

### Native VLAN

The VLAN associated with untagged traffic on an 802.1Q trunk.

### Default VLAN

On Cisco switches, VLAN 1 is the default VLAN.

> These roles become more important when we study trunking and enterprise switch design.

---

# 8. VLAN 1

Cisco switches normally contain VLAN 1 by default.

Many switch control and management protocols historically use VLAN 1 behavior in various contexts.

For professional network design, do not assume VLAN 1 should automatically be used for every purpose.

A common enterprise practice is to use dedicated VLANs for specific roles and avoid unnecessarily placing user devices in management-oriented segments.

---

# 9. Access Port

An **access port** is a switch port normally assigned to a single VLAN for end-device connectivity.

Example:

```text
PC1 ───── SW1
          G0/1
         VLAN 10
```

PC1 does not need to understand VLAN tagging for normal access-port connectivity.

The switch associates traffic arriving on that access port with the configured VLAN.

---

# 10. Access Port Analogy

Imagine an airport gate.

A passenger enters through Gate 10.

The airport knows that Gate 10 belongs to a specific area.

Similarly:

```text
PC → Access Port → VLAN
```

The access port determines which VLAN the end device belongs to at Layer 2.

---

# 11. Basic VLAN Topology

```text
                    SW1
          ┌──────────┼──────────┐
          │          │          │
        G0/1       G0/2       G0/3
          │          │          │
        PC1        PC2        PC3
       VLAN10     VLAN10     VLAN20
```

PC1 and PC2 are in the same VLAN.

PC3 is in a different VLAN.

Therefore:

```text
PC1 ↔ PC2 = Layer 2 communication possible
PC1 ↔ PC3 = Requires Layer 3 routing
```

Assuming addressing and configuration are otherwise correct.

---

# 12. VLAN and IP Subnet

A common enterprise design is:

```text
VLAN 10 → 192.168.10.0/24
VLAN 20 → 192.168.20.0/24
VLAN 30 → 192.168.30.0/24
```

This makes troubleshooting easier.

But remember:

> VLAN = Layer 2 logical segment  
> Subnet = Layer 3 logical address range

A VLAN does not itself assign IP addresses.

---

# 13. Same VLAN Communication

Example:

```text
PC1                  PC2
192.168.10.10        192.168.10.20
     │                    │
     └──── SW1 VLAN 10 ──┘
```

If the devices are in the same IP subnet and VLAN:

1. PC1 determines PC2 is local.
2. ARP can discover PC2's MAC address.
3. PC1 sends an Ethernet frame.
4. SW1 forwards the frame within VLAN 10.

No router is required for basic same-subnet communication.

---

# 14. Different VLAN Communication

Example:

```text
PC1                      PC2
VLAN10                   VLAN20
192.168.10.10            192.168.20.10
   │                         │
   └──────── SW1 ────────────┘
```

A Layer 2 switch cannot simply bridge VLAN 10 directly into VLAN 20.

Layer 3 routing is required.

Possible designs include:

- Router-on-a-stick
- Layer 3 switch using SVIs
- Router with separate interfaces

These will be studied later.

---

# 15. How a Switch Uses VLAN Membership

A switch maintains VLAN information for its ports.

Example:

```text
Port G0/1 → VLAN 10
Port G0/2 → VLAN 10
Port G0/3 → VLAN 20
Port G0/4 → VLAN 30
```

When an Ethernet frame enters a port, the switch knows which VLAN context applies to that traffic.

The switch then forwards frames only within the appropriate Layer 2 forwarding domain.

---

# 16. MAC Address Table and VLANs

A switch's MAC address table includes VLAN information.

Example:

```text
VLAN    MAC Address        Port
----    -----------        ----
10      0011.2233.4455    G0/1
10      0066.7788.99AA    G0/2
20      00AA.BBCC.DDEE    G0/3
```

The same MAC address can potentially appear in different VLAN contexts without representing the same Layer 2 segment.

This is why VLAN information is important when interpreting a MAC address table.

---

# 17. VLAN Creation — Cisco IOS

Enter privileged EXEC mode:

```cisco
enable
```

Enter global configuration mode:

```cisco
configure terminal
```

Create VLAN 10:

```cisco
vlan 10
```

Give it a name:

```cisco
name STUDENTS
```

Exit VLAN configuration mode:

```cisco
exit
```

Create another VLAN:

```cisco
vlan 20
name FACULTY
exit
```

---

# 18. Assign an Access Port to a VLAN

Select the interface:

```cisco
interface gigabitEthernet 0/1
```

Set access mode:

```cisco
switchport mode access
```

Assign VLAN 10:

```cisco
switchport access vlan 10
```

Exit:

```cisco
end
```

### Complete example

```cisco
enable
configure terminal
vlan 10
 name STUDENTS
exit
interface gigabitEthernet 0/1
 switchport mode access
 switchport access vlan 10
end
```

---

# 19. Configure Multiple Access Ports

You can configure a range of interfaces.

```cisco
configure terminal
interface range gigabitEthernet 0/1 - 4
 switchport mode access
 switchport access vlan 10
end
```

Now ports G0/1 through G0/4 are access ports in VLAN 10.

> Interface-range syntax can vary slightly by platform and interface naming. Always verify the interfaces available on your switch.

---

# 20. Verify VLANs

Use:

```cisco
show vlan brief
```

This is one of the most important VLAN verification commands.

Example concept:

```text
VLAN Name                             Status    Ports
---- -------------------------------- --------- ----------------
1    default                          active    G0/5 G0/6
10   STUDENTS                         active    G0/1 G0/2 G0/3 G0/4
20   FACULTY                          active    G0/7 G0/8
```

Check:

- VLAN exists.
- VLAN is active.
- Correct ports are assigned.

---

# 21. Verify a Specific Interface

Use:

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

This can show information such as:

- Administrative mode
- Operational mode
- Access VLAN
- Trunk information
- Native VLAN

This is extremely useful when an access port does not behave as expected.

---

# 22. Check the MAC Address Table

```cisco
show mac address-table
```

Or filter by VLAN:

```cisco
show mac address-table vlan 10
```

This helps verify that the switch is learning device MAC addresses in the expected VLAN.

---

# 23. Check Running Configuration

```cisco
show running-config
```

You may see VLAN-related interface configuration such as:

```text
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

This tells you what is currently configured in the running configuration.

---

# 24. Important Cisco IOS Modes

| Command | Mode | Purpose |
|---|---|---|
| `enable` | User EXEC | Enter privileged EXEC |
| `configure terminal` | Privileged EXEC | Enter global configuration |
| `vlan 10` | Global config | Create/select VLAN |
| `name STUDENTS` | VLAN config | Name VLAN |
| `interface g0/1` | Global config | Select interface |
| `switchport mode access` | Interface config | Make port an access port |
| `switchport access vlan 10` | Interface config | Assign access VLAN |
| `show vlan brief` | Privileged EXEC | Verify VLANs/ports |
| `show interfaces g0/1 switchport` | Privileged EXEC | Verify switchport state |

---

# 25. VLAN Configuration Example — College

### Requirement

Create:

- VLAN 10 = Students
- VLAN 20 = Faculty
- VLAN 30 = Administration

Assign:

- G0/1–G0/10 → VLAN 10
- G0/11–G0/15 → VLAN 20
- G0/16–G0/20 → VLAN 30

### Configuration

```cisco
enable
configure terminal

vlan 10
 name STUDENTS
exit

vlan 20
 name FACULTY
exit

vlan 30
 name ADMIN
exit

interface range gigabitEthernet 0/1 - 10
 switchport mode access
 switchport access vlan 10
exit

interface range gigabitEthernet 0/11 - 15
 switchport mode access
 switchport access vlan 20
exit

interface range gigabitEthernet 0/16 - 20
 switchport mode access
 switchport access vlan 30
exit

end
```

Verify:

```cisco
show vlan brief
```

---

# 26. VLAN Naming

Names make troubleshooting easier.

Instead of:

```text
VLAN 10
VLAN 20
VLAN 30
```

Use:

```text
VLAN 10 → STUDENTS
VLAN 20 → FACULTY
VLAN 30 → ADMIN
```

The VLAN ID is still the technical identifier; the name improves human readability.

---

# 27. Default VLAN Behavior

On a new Cisco switch, access ports are normally associated with VLAN 1 unless changed.

Therefore, before connecting production users, a network administrator should intentionally design VLAN membership.

Do not rely on default configuration for an enterprise deployment.

---

# 28. VLANs Across Multiple Switches

A VLAN can span multiple switches when the switches are connected appropriately.

Example:

```text
PC1 ── SW1 ═════════ SW2 ── PC2
      VLAN10          VLAN10
```

The inter-switch link must be designed to carry the required VLAN traffic.

This is where **trunking** becomes important.

We will study trunking in detail in the next module.

---

# 29. Access Port vs Trunk Port

| Feature | Access Port | Trunk Port |
|---|---|---|
| Typical endpoint | PC, printer, camera | Switch, router, AP |
| Typical VLANs carried | One | Multiple |
| VLAN tagging | Usually not presented to normal endpoint | 802.1Q tagging used for VLAN identification |
| Main purpose | Connect endpoint to one VLAN | Carry multiple VLANs between devices |

For now, focus on access ports. Trunking comes next.

---

# 30. VLAN Tagging Preview

When multiple VLANs travel over a trunk, the receiving device needs to know which VLAN a frame belongs to.

802.1Q provides VLAN tagging information.

Conceptually:

```text
Ethernet Frame
┌──────┬──────┬──────────┬──────┐
│ Dest │ Src  │ 802.1Q   │ Data │
│ MAC  │ MAC  │ Tag      │      │
└──────┴──────┴──────────┴──────┘
```

Do not confuse an access-port frame sent to a normal endpoint with a trunk frame carrying VLAN information.

---

# 31. VLAN Security Benefits 🔐

VLANs can help with segmentation.

For example:

```text
Employee VLAN
Guest VLAN
Server VLAN
Management VLAN
```

However:

> **A VLAN is not a complete security boundary by itself.**

Proper security also requires:

- Layer 3 ACLs
- Firewall policies
- Authentication
- Switch security controls
- Monitoring
- Proper management-plane protection

VLANs provide logical segmentation, not complete security.

---

# 32. VLAN and Broadcast Control

Suppose there are 100 devices in one VLAN.

A broadcast may be received by all 100 devices in that broadcast domain.

If those devices are divided into four VLANs:

```text
VLAN 10 → 25 devices
VLAN 20 → 25 devices
VLAN 30 → 25 devices
VLAN 40 → 25 devices
```

A broadcast in VLAN 10 remains within VLAN 10 unless a special Layer 3 service intentionally handles it.

This can reduce the scope of unnecessary Layer 2 broadcast traffic.

---

# 33. VLAN Communication Rules 🧠

### Same VLAN + same subnet

```text
PC1 ── SW ── PC2
```

Layer 2 communication is possible.

### Different VLANs

```text
PC1 VLAN10 ── SW ── PC2 VLAN20
```

Layer 3 routing is required.

### Same VLAN but different IP subnet

This is not a normal design for ordinary end-host access networking. Layer 3 addressing and gateway behavior must be considered carefully.

### Different switches, same VLAN

Communication can work if the inter-switch infrastructure correctly carries that VLAN.

---

# 34. Topology 1 — Single Switch, One VLAN

```text
             SW1
       ┌─────┼─────┐
       │     │     │
      PC1   PC2   PC3
      V10   V10   V10
```

Broadcast domains: **1**

All three PCs belong to VLAN 10.

---

# 35. Topology 2 — Single Switch, Multiple VLANs

```text
                SW1
       ┌────────┼────────┐
       │        │        │
      V10      V20      V30
       │        │        │
      PC1      PC2      PC3
```

Broadcast domains: **3**

Each VLAN is separate at Layer 2.

---

# 36. Topology 3 — Two Switches, Same VLAN

```text
PC1 ── SW1 ═════════ SW2 ── PC2
      VLAN10          VLAN10
```

If VLAN 10 is correctly carried between the switches, PC1 and PC2 can belong to the same Layer 2 broadcast domain.

The `═════════` represents an inter-switch link that must be configured appropriately; later this will normally be a trunk.

---

# 37. Topology 4 — Two Switches, Multiple VLANs

```text
          SW1 ═════════ SW2
         /   \          /   \
       V10   V20      V10   V20
        │     │        │     │
       PC1   PC2      PC3   PC4
```

If the inter-switch link carries both VLANs:

- VLAN 10 remains one broadcast domain across the switches.
- VLAN 20 remains another broadcast domain across the switches.

---

# 38. Topology 5 — VLANs + Router

```text
       VLAN10       VLAN20
        PC1           PC2
         │             │
         └──── SW1 ────┘
                │
                R1
```

Router/L3 device provides communication between VLANs.

This is the foundation for **inter-VLAN routing**.

---

# 39. Practical Lab 1 — Create VLANs

### Objective

Create two VLANs on a Cisco switch.

### Topology

```text
PC1 ── SW1 ── PC2
```

### Requirements

- VLAN 10 = STUDENTS
- VLAN 20 = FACULTY

### Tasks

1. Create VLAN 10.
2. Name it STUDENTS.
3. Create VLAN 20.
4. Name it FACULTY.
5. Verify using `show vlan brief`.

### Expected result

Both VLANs appear as active.

---

# 40. Practical Lab 2 — Assign Access Ports

### Topology

```text
PC1 ── G0/1
PC2 ── G0/2
PC3 ── G0/3
          │
         SW1
```

### Requirements

- PC1 → VLAN 10
- PC2 → VLAN 10
- PC3 → VLAN 20

### Tasks

Configure:

```cisco
interface range g0/1 - 2
 switchport mode access
 switchport access vlan 10
exit

interface g0/3
 switchport mode access
 switchport access vlan 20
exit
```

Verify:

```cisco
show vlan brief
```

---

# 41. Practical Lab 3 — Test Same VLAN Communication

### Addressing

| Device | VLAN | IP Address | Mask |
|---|---:|---|---|
| PC1 | 10 | 192.168.10.10 | /24 |
| PC2 | 10 | 192.168.10.20 | /24 |
| PC3 | 20 | 192.168.20.10 | /24 |

### Test

From PC1:

```text
ping 192.168.10.20
```

Expected:

```text
Success
```

Then:

```text
ping 192.168.20.10
```

Expected without Layer 3 routing:

```text
Failure
```

### Why?

PC3 belongs to another VLAN and another subnet. A Layer 3 routing function is required.

---

# 42. Practical Lab 4 — VLAN Troubleshooting Challenge

### Problem

PC1 and PC2 are supposed to communicate.

Topology:

```text
PC1 ── SW1 ── PC2
```

Expected:

```text
PC1 → VLAN10
PC2 → VLAN10
```

But ping fails.

### Investigate

Run:

```cisco
show vlan brief
show interfaces g0/1 switchport
show interfaces g0/2 switchport
show mac address-table vlan 10
```

Then check the PCs:

```text
ipconfig /all
```

### Possible causes

- Port assigned to wrong VLAN.
- VLAN does not exist.
- Incorrect IP address.
- Incorrect subnet mask.
- Cable/link issue.
- PC firewall blocking ICMP.
- Interface is down.

---

# 43. Troubleshooting Scenario — VLAN Does Not Exist

### Situation

You configure:

```cisco
interface g0/1
switchport mode access
switchport access vlan 50
```

But VLAN 50 was never created on the switch.

### Verify

```cisco
show vlan brief
```

### Fix

```cisco
configure terminal
vlan 50
 name USERS
end
```

Then verify again.

---

# 44. Troubleshooting Scenario — Wrong Port VLAN

### Situation

PC1 should be in VLAN 10 but is accidentally assigned to VLAN 20.

### Verify

```cisco
show vlan brief
```

or:

```cisco
show interfaces g0/1 switchport
```

### Fix

```cisco
configure terminal
interface g0/1
switchport mode access
switchport access vlan 10
end
```

Then test again.

---

# 45. Troubleshooting Scenario — Same VLAN, Different Switch

### Situation

```text
PC1 ─ SW1 ═══ SW2 ─ PC2
     VLAN10        VLAN10
```

Both PCs are correctly configured for VLAN 10, but they cannot communicate.

### Investigate

The problem may be the inter-switch link.

Later, check:

```cisco
show interfaces trunk
```

Also verify VLAN 10 exists on both switches:

```cisco
show vlan brief
```

This is the bridge from VLAN fundamentals to trunking.

---

# 46. Troubleshooting Scenario — VLAN Works Locally but Not Across Switches

### Situation

PC1 and PC2 on SW1 communicate.

PC3 on SW2 is in VLAN 10 but cannot communicate with PC1.

```text
PC1 ─ SW1 ═════ SW2 ─ PC3
 V10                 V10
```

### Likely areas

- Inter-switch link configuration
- Trunk status
- Allowed VLAN list
- Native VLAN mismatch
- VLAN existence
- Physical link
- STP state

Useful commands:

```cisco
show interfaces trunk
show vlan brief
show spanning-tree vlan 10
show interfaces
```

---

# 47. Common Beginner Mistakes ❌

### Mistake 1

> “VLAN 10 means IP network 10.x.x.x.”

❌ No.

VLAN IDs and IP addresses are separate concepts.

### Mistake 2

> “Different VLANs can communicate automatically through a switch.”

❌ No.

Inter-VLAN communication requires Layer 3 routing.

### Mistake 3

> “A VLAN is the same thing as a subnet.”

❌ They are different concepts, although they are commonly mapped one-to-one in enterprise designs.

### Mistake 4

> “Every switch port is automatically a different broadcast domain.”

❌ No.

Ports in the same VLAN share a broadcast domain.

### Mistake 5

> “Creating a VLAN automatically gives devices IP addresses.”

❌ No.

IP addressing must be configured separately.

### Mistake 6

> “VLANs provide complete security.”

❌ No.

They provide segmentation, not complete security.

---

# 48. Real-World Scenario 🏢

A company has:

- 300 employees
- 3 floors
- HR
- Finance
- IT
- Guest Wi-Fi
- IP phones
- Network management devices

A possible logical segmentation:

```text
VLAN 10 → HR
VLAN 20 → Finance
VLAN 30 → IT
VLAN 40 → Guests
VLAN 50 → Voice
VLAN 99 → Management
```

Benefits:

- Smaller broadcast domains
- Easier policy enforcement
- Better organization
- Easier troubleshooting
- Better traffic separation
- Foundation for security controls

Later, routing and ACLs can control which VLANs are allowed to communicate.

---

# 49. Scenario Challenge 🎯

You are the network administrator for a college.

You have one 24-port switch.

Requirements:

| Department | VLAN | Ports |
|---|---:|---|
| Students | 10 | G0/1–G0/12 |
| Faculty | 20 | G0/13–G0/18 |
| Admin | 30 | G0/19–G0/22 |

### Questions

1. How many VLANs should you create?
2. How many broadcast domains will exist?
3. Which ports should belong to VLAN 10?
4. Which command verifies VLAN membership?
5. Which command verifies the switchport state of G0/1?
6. Can a student PC in VLAN 10 directly communicate at Layer 2 with an admin PC in VLAN 30?
7. What is required for VLAN 10 and VLAN 30 to communicate?

### Try it yourself first! 🧠

<details>
<summary>💡 Reveal the solution</summary>

1. Create VLANs 10, 20, and 30.
2. Three broadcast domains.
3. G0/1–G0/12 → VLAN 10.
4. `show vlan brief`.
5. `show interfaces g0/1 switchport`.
6. No, not directly at Layer 2.
7. Layer 3 routing / inter-VLAN routing.

</details>

---

# 50. Practice Exercises ✍️

### Exercise 1

Create VLAN 10 named `USERS`.

### Exercise 2

Create VLAN 20 named `SERVERS`.

### Exercise 3

Assign G0/1–G0/5 to VLAN 10.

### Exercise 4

Assign G0/6–G0/10 to VLAN 20.

### Exercise 5

Verify your configuration using at least three Cisco commands.

### Exercise 6

Draw a topology containing:

- 2 VLANs
- 4 PCs
- 1 switch

### Exercise 7

Explain why a broadcast from VLAN 10 should not normally reach VLAN 20.

### Exercise 8

Explain why VLANs are useful in an enterprise network.

---

# 51. Interview Questions 🎤

## Beginner

1. What is a VLAN?
2. Why are VLANs used?
3. Which OSI layer does VLAN primarily operate at?
4. What is a VLAN ID?
5. What is an access port?
6. What is the default VLAN on a Cisco switch?
7. What command displays VLANs and their assigned ports?

## Intermediate

8. How does a VLAN reduce broadcast scope?
9. What is the difference between a VLAN and an IP subnet?
10. Can two devices in different VLANs communicate directly at Layer 2?
11. What is required for inter-VLAN communication?
12. What is the difference between an access port and a trunk port?
13. Why can the same VLAN span multiple switches?
14. Why is VLAN information important when reading the MAC address table?

## Advanced

15. Why might an enterprise use separate management, voice, guest, and data VLANs?
16. Is a VLAN a complete security boundary? Explain.
17. What happens to a broadcast inside a VLAN?
18. How would you troubleshoot a VLAN that works on one switch but not another?
19. What is the purpose of 802.1Q in VLAN networking?
20. What are some risks of poor VLAN design?

## Troubleshooting

21. A PC is connected to G0/1 but receives no expected connectivity. What VLAN commands would you run?
22. Two PCs are supposed to be in VLAN 10 but `show vlan brief` shows one in VLAN 20. What would you do?
23. VLAN 10 works locally on SW1 but devices on SW2 cannot communicate. What should you investigate?
24. A user can communicate within their VLAN but cannot reach another VLAN. Is that necessarily a VLAN problem? What Layer 3 components should you check?

---

# 52. Quick Revision ⚡

```text
VLAN
 ↓
Virtual Local Area Network
 ↓
Logical Layer 2 segmentation
 ↓
Each VLAN = separate broadcast domain
 ↓
Access port = normally one VLAN
 ↓
Different VLANs need Layer 3 routing
```

### Core commands

```cisco
show vlan brief
show interfaces g0/1 switchport
show mac address-table
show mac address-table vlan 10
show running-config
```

### Configuration

```cisco
vlan 10
 name USERS
exit

interface g0/1
 switchport mode access
 switchport access vlan 10
```

---

# 53. Cheat Sheet 📋

| Concept | Remember |
|---|---|
| VLAN | Logical Layer 2 network |
| VLAN ID | Numeric VLAN identifier |
| VLAN 1 | Default VLAN on Cisco switches |
| Access port | Normally carries one user VLAN |
| Broadcast domain | Each VLAN is one |
| Same VLAN | Layer 2 communication possible |
| Different VLAN | Layer 3 routing required |
| VLAN ≠ subnet | Layer 2 vs Layer 3 concepts |
| `show vlan brief` | Verify VLANs and access-port membership |
| `show interfaces ... switchport` | Detailed switchport verification |
| `show mac address-table` | Verify MAC learning |
| 802.1Q | VLAN tagging mechanism used on trunks |

---

# 54. Connection to Previous Topics 🔗

You already learned:

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
```

The previous lesson taught you:

> **Each VLAN is a separate broadcast domain.**

Now you are learning how to actually create and assign those VLANs on a Cisco switch.

---

# 55. What Comes Next?

VLANs become much more powerful when they need to travel between switches.

That requires understanding **trunks**.

The next topic will cover:

- Trunk ports
- 802.1Q tagging
- Native VLAN
- Allowed VLANs
- Access vs trunk behavior
- Inter-switch VLAN communication
- Cisco trunk configuration
- Verification
- Troubleshooting
- Practical Packet Tracer labs

---

# 56. Completion Checklist ✅

Before moving to trunking, you should be able to:

- [ ] Define VLAN.
- [ ] Explain why VLANs are used.
- [ ] Explain VLANs as broadcast domains.
- [ ] Explain VLAN IDs.
- [ ] Explain access ports.
- [ ] Explain VLAN vs subnet.
- [ ] Create a VLAN on Cisco IOS.
- [ ] Name a VLAN.
- [ ] Assign an access port to a VLAN.
- [ ] Configure an interface range.
- [ ] Verify VLAN membership.
- [ ] Verify switchport configuration.
- [ ] Verify MAC learning by VLAN.
- [ ] Explain why different VLANs need Layer 3 routing.
- [ ] Troubleshoot a wrong-VLAN problem.
- [ ] Explain why VLANs can span multiple switches.
- [ ] Understand why trunks are needed between switches.

---

> 📚 **Study rule:** Don't memorize `switchport access vlan 10` as just a command. Understand what it means: **“traffic arriving on this access port belongs to VLAN 10.”** Once that concept is clear, trunking and inter-VLAN routing become much easier.
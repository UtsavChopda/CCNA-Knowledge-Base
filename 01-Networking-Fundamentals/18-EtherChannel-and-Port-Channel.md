# 18. EtherChannel and Port-Channel

> **Learning path:** VLANs → Trunking → DTP → VTP → STP → **EtherChannel**

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain why EtherChannel is used.
- Understand the difference between physical links and a logical Port-Channel.
- Explain how EtherChannel works with STP.
- Understand LACP and PAgP.
- Distinguish active, passive, desirable, and auto negotiation modes.
- Understand which EtherChannel mode combinations form a bundle.
- Configure Layer 2 EtherChannel on Cisco switches.
- Configure trunk EtherChannel.
- Verify Port-Channel and member interfaces.
- Troubleshoot inconsistent EtherChannel configuration.
- Understand load balancing and hashing.
- Design redundant and high-bandwidth switch links.
- Use EtherChannel security and operational best practices.

---

# 1. What Is EtherChannel?

**EtherChannel** combines multiple physical Ethernet links into one logical link.

Instead of treating four cables as four independent Layer 2 paths, the switches can treat them as one logical interface called a **Port-Channel**.

Example:

```text
Without EtherChannel:

SW1 ================= SW2
     Link 1
SW1 ================= SW2
     Link 2
SW1 ================= SW2
     Link 3
SW1 ================= SW2
     Link 4
```

With EtherChannel:

```text
          EtherChannel
SW1 ======================== SW2
      4 physical links
      1 logical Port-Channel
```

The physical links still exist, but the bundle operates as a logical connection.

---

# 2. Why Do We Need EtherChannel?

Suppose two switches have four 1-Gbps links.

Without EtherChannel, STP may consider the links separate redundant Layer 2 paths and place some links into a non-forwarding role.

```text
SW1 ===== SW2
  \       \
   ========

STP may block redundant links.
```

With EtherChannel:

```text
SW1 ================= SW2
      Port-Channel
      4 × 1 Gbps
```

The links operate as one logical connection for STP purposes.

This can provide:

- Increased aggregate bandwidth.
- Link redundancy.
- Better utilization of multiple physical links.
- Simplified STP topology.
- Faster recovery when one member fails, assuming the remaining bundle is healthy.

---

# 3. Simple Analogy 🚗

Imagine one road between two cities.

A single road can become congested.

Now imagine four parallel lanes that operate together as one highway.

```text
Lane 1 ─┐
Lane 2 ─┤
Lane 3 ─┤ → One logical highway
Lane 4 ─┘
```

EtherChannel works similarly:

```text
Physical Ethernet links
          ↓
     EtherChannel
          ↓
  Logical Port-Channel
```

---

# 4. EtherChannel vs STP ⭐

This is one of the most important concepts.

### Without EtherChannel

```text
SW1 ===== SW2
SW1 ===== SW2
SW1 ===== SW2
SW1 ===== SW2
```

STP can see multiple parallel Layer 2 paths.

It may block some links to prevent loops.

### With EtherChannel

```text
SW1 ================= SW2
       Port-Channel
```

STP sees the bundle as one logical Layer 2 path.

The physical member links can all participate in forwarding traffic as part of the bundle.

### Key idea 🧠

> **EtherChannel combines links; STP prevents loops.**

---

# 5. EtherChannel Protocols

Cisco switches commonly support two negotiation protocols depending on platform/software:

| Protocol | Description |
|---|---|
| LACP | IEEE 802.1AX standardized link aggregation protocol; formerly associated with 802.3ad terminology |
| PAgP | Cisco proprietary Port Aggregation Protocol |

There is also **static EtherChannel**, where no negotiation protocol is used.

### Important

Do not mix LACP and PAgP on the same EtherChannel.

---

# 6. LACP

**LACP — Link Aggregation Control Protocol** — is the standards-based protocol commonly used for EtherChannel negotiation.

Cisco IOS commonly uses:

```cisco
channel-group 1 mode active
```

or:

```cisco
channel-group 1 mode passive
```

### LACP modes

| Mode | Behavior |
|---|---|
| `active` | Actively sends LACP negotiation messages |
| `passive` | Waits for LACP negotiation |

### Important combinations

```text
active + active   → forms LACP bundle
active + passive  → forms LACP bundle
passive + passive → does NOT form bundle
```

### Easy memory trick

> **Active asks. Passive waits.**

---

# 7. PAgP

**PAgP — Port Aggregation Protocol** — is Cisco proprietary.

Common modes:

```cisco
channel-group 1 mode desirable
```

and:

```cisco
channel-group 1 mode auto
```

### PAgP modes

| Mode | Behavior |
|---|---|
| `desirable` | Actively attempts to negotiate |
| `auto` | Passively waits |

### Important combinations

```text
desirable + desirable → forms

desirable + auto      → forms

auto + auto           → does NOT form
```

### Easy memory trick

> **Desirable asks. Auto waits.**

---

# 8. Static EtherChannel — Mode `on`

EtherChannel can be configured without a negotiation protocol:

```cisco
channel-group 1 mode on
```

Both sides must be intentionally configured consistently.

```text
SW1 → mode on
SW2 → mode on
```

### Important warning ⚠️

Static `on` mode does not perform protocol negotiation to verify that the other side agrees with the bundle.

A mismatch can create forwarding problems.

Use static EtherChannel only when you intentionally understand and control both ends.

---

# 9. EtherChannel Negotiation Matrix ⭐

## LACP

| Side A | Side B | Result |
|---|---|---|
| Active | Active | Forms |
| Active | Passive | Forms |
| Passive | Active | Forms |
| Passive | Passive | Does not form |

## PAgP

| Side A | Side B | Result |
|---|---|---|
| Desirable | Desirable | Forms |
| Desirable | Auto | Forms |
| Auto | Desirable | Forms |
| Auto | Auto | Does not form |

## Static

| Side A | Side B | Result |
|---|---|---|
| On | On | Forms as static bundle |
| On | Active/passive | Mismatch; do not design this way |
| On | Desirable/auto | Mismatch; do not design this way |

### Remember

```text
LACP:
Active + Passive = YES
Passive + Passive = NO

PAgP:
Desirable + Auto = YES
Auto + Auto = NO
```

---

# 10. EtherChannel and Interface Consistency ⭐

All member interfaces must have compatible configuration.

Important attributes can include:

- Speed.
- Duplex where applicable.
- Switchport mode.
- Access VLAN.
- Native VLAN.
- Allowed VLAN list.
- Trunk encapsulation where applicable.
- STP-related configuration where relevant.
- Other platform-specific interface attributes.

Example:

```text
SW1 Gi0/1 → trunk
SW1 Gi0/2 → trunk
SW1 Gi0/3 → access
SW1 Gi0/4 → trunk
```

These should not be casually bundled together.

### Key principle

> **Member ports must have compatible operational characteristics.**

---

# 11. Layer 2 EtherChannel

Layer 2 EtherChannel can operate as:

- An access Port-Channel.
- A trunk Port-Channel.

### Access example

```cisco
interface range gigabitEthernet 0/1-2
 switchport mode access
 switchport access vlan 10
 channel-group 1 mode active
```

Then configure the logical interface as appropriate:

```cisco
interface port-channel 1
 switchport mode access
 switchport access vlan 10
```

### Trunk example

```cisco
interface range gigabitEthernet 0/1-4
 switchport mode trunk
 channel-group 1 mode active
```

Then:

```cisco
interface port-channel 1
 switchport mode trunk
```

---

# 12. Configure EtherChannel Using LACP — Recommended Learning Pattern

A common configuration approach is:

```cisco
configure terminal
interface range gigabitEthernet 0/1-2
 channel-group 1 mode active
exit
interface port-channel 1
 switchport mode trunk
end
```

On the neighboring switch, use a compatible LACP mode:

```cisco
configure terminal
interface range gigabitEthernet 0/1-2
 channel-group 1 mode active
exit
interface port-channel 1
 switchport mode trunk
end
```

Verify:

```cisco
show etherchannel summary
show interfaces port-channel 1
show interfaces trunk
```

---

# 13. Why Configure the Port-Channel?

The Port-Channel is the logical interface.

Think of it as:

```text
Physical interfaces
Gi0/1 ─┐
Gi0/2 ─┤
Gi0/3 ─┤ → Po1
Gi0/4 ─┘
```

The bundle's logical behavior should be clearly defined on `Port-channel 1`.

This makes the design easier to understand and maintain.

---

# 14. EtherChannel Load Balancing

EtherChannel does not normally send every frame across every physical member simultaneously.

The switch uses a **hashing/load-balancing algorithm** to select a member link for a flow.

Possible inputs can include combinations such as:

- Source MAC.
- Destination MAC.
- Source and destination MAC.
- Source IP.
- Destination IP.
- Source and destination IP.
- Layer 4 ports, depending on platform/options.

### Important

The exact available algorithms depend on the Cisco platform and software.

A single conversation/flow may remain on one physical member rather than being split packet-by-packet across every link.

---

# 15. Verify Load-Balancing Method

On many Cisco Catalyst platforms:

```cisco
show etherchannel load-balance
```

This displays the configured EtherChannel hashing method where supported.

The command is useful when investigating why traffic appears concentrated on one member.

---

# 16. Bandwidth Example

Suppose:

```text
4 × 1 Gbps physical links
```

The EtherChannel has an aggregate theoretical capacity of approximately:

```text
4 Gbps
```

But a single flow may still be limited by the selected physical member depending on the hashing method and traffic pattern.

### Important

Do not say:

> “Every single connection gets 4 Gbps.”

That is usually incorrect.

Instead:

> “The bundle provides aggregate capacity across multiple flows.”

---

# 17. EtherChannel and STP

This relationship is critical.

### Without EtherChannel

```text
       SW1
      /   \
     /     \
   SW2=====SW3
```

Multiple paths can cause STP to block redundant links.

### With EtherChannel

```text
       SW1
      /   \
     /     \
   SW2====SW3
   Port-Channel
```

STP sees the Port-Channel as one logical link.

### Key takeaway

> **EtherChannel can allow multiple physical links to forward as one logical STP path.**

---

# 18. EtherChannel + VLAN Trunking

A Port-Channel can itself be a trunk.

Example:

```text
             Po1
SW1 ================= SW2
    VLAN 10/20/30
```

The physical member links carry the bundled traffic.

The logical Port-Channel carries the trunk configuration.

---

# 19. Native VLAN and EtherChannel

For a trunk Port-Channel, ensure native VLAN configuration is intentionally consistent.

Example:

```cisco
interface port-channel 1
 switchport trunk native vlan 99
```

Verify:

```cisco
show interfaces trunk
show interfaces port-channel 1 switchport
```

A mismatch can create unexpected behavior and should be corrected.

---

# 20. Allowed VLANs and EtherChannel

Example:

```cisco
interface port-channel 1
 switchport trunk allowed vlan 10,20,30
```

Verify:

```cisco
show interfaces trunk
```

### Important

The logical Port-Channel should have an intentional trunk configuration, and member interfaces must remain compatible.

---

# 21. Topology 1 — Two-Link LACP

```text
        Gi0/1 ===== Gi0/1
SW1     Gi0/2 ===== Gi0/2     SW2
             \     /
              \___/
                Po1
```

Configuration:

```text
SW1: Gi0/1-2 → LACP active
SW2: Gi0/1-2 → LACP active
```

Expected:

```text
Po1 = bundled
```

---

# 22. Topology 2 — Active + Passive LACP

```text
SW1 ================= SW2
  active          passive
     \              /
       Port-Channel
```

Expected:

```text
LACP bundle forms
```

Because one side actively initiates LACP.

---

# 23. Topology 3 — Passive + Passive

```text
SW1 ================= SW2
 passive          passive
```

Expected:

```text
No LACP bundle
```

Neither side actively starts the negotiation.

---

# 24. Topology 4 — PAgP Desirable + Auto

```text
SW1 ================= SW2
 desirable           auto
```

Expected:

```text
PAgP bundle forms
```

---

# 25. Topology 5 — PAgP Auto + Auto

```text
SW1 ================= SW2
 auto                auto
```

Expected:

```text
No PAgP bundle
```

---

# 26. Topology 6 — Static `On` EtherChannel

```text
SW1 ================= SW2
      mode on
```

Both sides must be intentionally configured for static aggregation.

No LACP/PAgP negotiation occurs.

---

# 27. Topology 7 — Four-Link EtherChannel

```text
SW1 ================= SW2
    \==============/
     \============/
      \==========/
        Po1
```

Example:

```text
Gi0/1
Gi0/2
Gi0/3
Gi0/4
```

all bundled into:

```text
Port-channel 1
```

---

# 28. Topology 8 — EtherChannel Between Access and Distribution

```text
             DIST1
            =======
           //  Po1 \\
          //         \\
       ACC1          ACC2
```

The bundle can carry multiple VLANs when configured as a trunk.

---

# 29. Topology 9 — Redundant EtherChannels

```text
             DIST1
            /     \
         Po1       Po2
          /         \
        ACC1=======ACC2
```

Multiple logical Layer 2 paths can still create loops.

STP remains necessary.

EtherChannel does not eliminate the need for STP.

---

# 30. Topology 10 — EtherChannel + Rapid PVST+

```text
          SW1
         /   \
       Po1   Po2
       /       \
     SW2=======SW3
```

Rapid PVST+ can operate over the resulting logical topology.

Verify:

```cisco
show spanning-tree
show etherchannel summary
```

---

# 31. Topology 11 — One Member Link Failure

```text
SW1 ================= SW2
     Po1
  Gi0/1 ✓
  Gi0/2 ✗
  Gi0/3 ✓
  Gi0/4 ✓
```

The Port-Channel can remain operational using the surviving members.

This is one of the major redundancy benefits of EtherChannel.

---

# 32. Topology 12 — Misconfigured Member

```text
SW1
Gi0/1 → trunk
Gi0/2 → trunk
Gi0/3 → access VLAN 10  🚨

        ↓
      Po1
```

Gi0/3 should not be casually added to the same bundle because its configuration is inconsistent.

---

# 33. Cisco Verification Commands ⭐

## EtherChannel summary

```cisco
show etherchannel summary
```

This is one of the most important commands.

You may see indicators such as:

```text
Po1(SU)
```

and member indicators such as:

```text
(P)
```

The exact output varies by platform.

A commonly encountered interpretation is:

- `S` → Layer 2 Port-Channel.
- `U` → Port-Channel is in use.
- `P` → member port is bundled in the Port-Channel.

Always interpret the complete output from your actual switch.

---

# 34. Show EtherChannel Detail

```cisco
show etherchannel detail
```

Useful for deeper troubleshooting.

---

# 35. Show Specific Channel

```cisco
show etherchannel 1 summary
```

or, depending on platform:

```cisco
show etherchannel 1 port-channel
```

Check the exact supported syntax on your switch.

---

# 36. Show Port-Channel Interface

```cisco
show interfaces port-channel 1
```

Useful for checking:

- Interface status.
- Counters.
- Errors.
- Traffic.
- Speed.
- Operational details.

---

# 37. Show Port-Channel Switchport

```cisco
show interfaces port-channel 1 switchport
```

Useful for checking:

- Administrative mode.
- Operational mode.
- Access VLAN.
- Native VLAN.
- Trunk information.

---

# 38. Show Trunks

```cisco
show interfaces trunk
```

Important when the EtherChannel is being used as a trunk.

---

# 39. Show Running Configuration

```cisco
show running-config interface gigabitEthernet 0/1
show running-config interface port-channel 1
```

Compare both member interfaces and the logical Port-Channel.

---

# 40. Show Interfaces

```cisco
show interfaces gigabitEthernet 0/1
show interfaces gigabitEthernet 0/2
```

Look for:

- Speed.
- Duplex.
- Errors.
- Input/output counters.
- Physical status.

---

# 41. Show Load-Balance Method

```cisco
show etherchannel load-balance
```

Useful when investigating traffic distribution.

---

# 42. Cisco EtherChannel Configuration — LACP

## Step 1 — Select members

```cisco
configure terminal
interface range gigabitEthernet 0/1-2
```

## Step 2 — Configure LACP

```cisco
channel-group 1 mode active
```

## Step 3 — Configure Port-Channel

```cisco
interface port-channel 1
 switchport mode trunk
```

## Step 4 — Verify

```cisco
show etherchannel summary
show interfaces trunk
```

---

# 43. LACP Passive Example

SW1:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode active
```

SW2:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode passive
```

Expected:

```text
Bundle forms
```

---

# 44. PAgP Configuration

Where PAgP is supported:

SW1:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode desirable
```

SW2:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode auto
```

Verify:

```cisco
show etherchannel summary
```

---

# 45. Static EtherChannel Configuration

SW1:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode on
```

SW2:

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode on
```

Verify:

```cisco
show etherchannel summary
```

### Warning

Static `on` mode requires careful consistency because there is no LACP/PAgP negotiation.

---

# 46. EtherChannel with Access Ports

Example:

```cisco
interface range gigabitEthernet 0/1-2
 switchport mode access
 switchport access vlan 10
 channel-group 1 mode active

interface port-channel 1
 switchport mode access
 switchport access vlan 10
```

This creates a Layer 2 access EtherChannel.

---

# 47. EtherChannel with Trunk Ports

Example:

```cisco
interface range gigabitEthernet 0/1-2
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```

Add required trunk settings to the Port-Channel according to the design.

---

# 48. EtherChannel Configuration Principle ⭐

A clean approach is:

```text
1. Identify member interfaces
2. Make member settings compatible
3. Create channel-group
4. Configure Port-Channel
5. Configure VLAN/trunk behavior intentionally
6. Verify bundle
7. Verify traffic
8. Test member failure
```

---

# 49. Practical Lab 1 — Two-Link LACP

## Objective

Create your first EtherChannel.

## Topology

```text
PC1 --- SW1 ================= SW2 --- PC2
          Gi0/1 ===== Gi0/1
          Gi0/2 ===== Gi0/2
                Po1
```

## VLAN

```text
VLAN 10 → USERS
```

## SW1

```cisco
vlan 10
 name USERS

interface range gigabitEthernet 0/1-2
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```

## SW2

```cisco
vlan 10
 name USERS

interface range gigabitEthernet 0/1-2
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```

Verify:

```cisco
show etherchannel summary
show interfaces trunk
```

### Expected

```text
Po1 = bundled
Member ports = bundled
```

---

# 50. Practical Lab 2 — Active + Passive LACP

## SW1

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode active
```

## SW2

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode passive
```

Verify:

```cisco
show etherchannel summary
```

### Expected

The LACP bundle forms because the active side initiates negotiation.

---

# 51. Practical Lab 3 — Passive + Passive Troubleshooting

Configure:

```text
SW1 → passive
SW2 → passive
```

Verify:

```cisco
show etherchannel summary
```

### Expected

The bundle does not form.

### Fix

Change one side to active:

```cisco
channel-group 1 mode active
```

Verify again.

---

# 52. Practical Lab 4 — PAgP Modes

Where PAgP is supported:

```text
SW1 → desirable
SW2 → auto
```

Verify:

```cisco
show etherchannel summary
```

Then test:

```text
SW1 → auto
SW2 → auto
```

Observe the difference.

---

# 53. Practical Lab 5 — Member Link Failure ⭐

Start with a working two- or four-link EtherChannel.

Example:

```text
Po1
├── Gi0/1 ✓
├── Gi0/2 ✓
├── Gi0/3 ✓
└── Gi0/4 ✓
```

Shut down one member in a controlled lab:

```cisco
interface gigabitEthernet 0/1
 shutdown
```

Verify:

```cisco
show etherchannel summary
```

Then restore:

```cisco
no shutdown
```

### Goal

Observe that the Port-Channel can continue using surviving members.

---

# 54. Practical Lab 6 — Four-Link Trunk EtherChannel

## Topology

```text
SW1 ================= SW2
 Gi0/1 ============== Gi0/1
 Gi0/2 ============== Gi0/2
 Gi0/3 ============== Gi0/3
 Gi0/4 ============== Gi0/4
          Po1
```

VLANs:

| VLAN | Purpose |
|---:|---|
| 10 | Users |
| 20 | Servers |
| 30 | Guest |

Configure the four links as LACP members.

Configure Po1 as a trunk and allow only required VLANs:

```cisco
interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

Verify:

```cisco
show etherchannel summary
show interfaces trunk
```

---

# 55. Practical Lab 7 — Load-Balancing Investigation

Verify:

```cisco
show etherchannel load-balance
```

Generate multiple flows between hosts.

Inspect member-interface counters:

```cisco
show interfaces gigabitEthernet 0/1
show interfaces gigabitEthernet 0/2
```

### Goal

Understand that EtherChannel distributes flows according to a hashing algorithm rather than simply sending every packet equally across all links.

---

# 56. Practical Lab 8 — Inconsistent Member Configuration 🔧

Create a controlled mismatch:

```text
Gi0/1 → trunk
Gi0/2 → access VLAN 10
```

Attempt to bundle both.

Verify:

```cisco
show etherchannel summary
show interfaces gigabitEthernet 0/1 switchport
show interfaces gigabitEthernet 0/2 switchport
```

### Task

Identify which interface is inconsistent and correct the configuration.

---

# 57. Practical Lab 9 — EtherChannel + STP

## Topology

```text
             SW1
            /   \
           /     \
        Po1       Po2
         /         \
       SW2=========SW3
```

Verify:

```cisco
show etherchannel summary
show spanning-tree
```

### Questions

1. How does STP see Po1?
2. How does STP see the physical members?
3. Which logical path is forwarding?
4. Why does EtherChannel not eliminate STP?

---

# 58. Practical Lab 10 — Campus Redundancy Challenge 🏫

## Topology

```text
                  DIST1
                 /     \
              Po1       Po2
               /         \
             ACC1=======ACC2
```

Requirements:

- VLAN 10 → Students
- VLAN 20 → Faculty
- VLAN 30 → Administration
- VLAN 99 → Management
- Po1 and Po2 use LACP.
- Trunks carry only required VLANs.
- STP is enabled.
- DIST1 is the intended root for VLAN 10.
- Test member-link failure.

### Verification

```cisco
show etherchannel summary
show interfaces trunk
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree vlan 30
```

---

# 59. Troubleshooting Methodology 🔧

When EtherChannel does not form:

```text
1. Check physical interfaces
        ↓
2. Check speed/duplex
        ↓
3. Check channel-group number
        ↓
4. Check LACP/PAgP/static mode
        ↓
5. Check member configuration consistency
        ↓
6. Check trunk/access mode
        ↓
7. Check VLAN configuration
        ↓
8. Check native VLAN
        ↓
9. Check allowed VLANs
        ↓
10. Check Port-Channel status
        ↓
11. Check STP
        ↓
12. Test traffic
```

---

# 60. Problem — EtherChannel Does Not Form

### Check

```cisco
show etherchannel summary
```

Then:

```cisco
show running-config interface gigabitEthernet 0/1
show running-config interface gigabitEthernet 0/2
```

Compare both sides.

---

# 61. Problem — Passive + Passive

### Symptom

No LACP bundle.

### Cause

Neither side initiates negotiation.

### Fix

Configure at least one side as active:

```cisco
channel-group 1 mode active
```

---

# 62. Problem — Auto + Auto PAgP

### Symptom

No PAgP bundle.

### Cause

Both sides are passive.

### Fix

Use desirable on one side:

```cisco
channel-group 1 mode desirable
```

---

# 63. Problem — LACP vs PAgP Mismatch

Example:

```text
SW1 → LACP active
SW2 → PAgP desirable
```

These are different protocols.

### Fix

Use the same EtherChannel protocol on both ends.

For example:

```text
LACP active ↔ LACP active
```

---

# 64. Problem — Member Configuration Mismatch

Example:

```text
SW1 Gi0/1 → trunk
SW1 Gi0/2 → access
```

### Check

```cisco
show interfaces gigabitEthernet 0/1 switchport
show interfaces gigabitEthernet 0/2 switchport
```

Make the intended member interfaces compatible.

---

# 65. Problem — Native VLAN Mismatch

Check:

```cisco
show interfaces trunk
```

Ensure the Port-Channel trunk configuration is consistent between peers.

---

# 66. Problem — Allowed VLAN Mismatch

Check:

```cisco
show interfaces trunk
```

Ensure the required VLANs are permitted on the logical trunk.

---

# 67. Problem — One Member Keeps Leaving the Bundle

Possible causes:

- Physical errors.
- Incompatible speed/duplex.
- Configuration mismatch.
- Protocol negotiation problem.
- Unsupported combination.
- Hardware/transceiver issue.

Check:

```cisco
show etherchannel summary
show etherchannel detail
show interfaces gigabitEthernet 0/1
```

---

# 68. Problem — Traffic Uses Only One Link

This is not automatically a failure.

Check:

```cisco
show etherchannel load-balance
```

EtherChannel uses hashing and generally distributes **flows**, not every individual packet evenly.

A small number of flows can therefore appear concentrated on one member.

---

# 69. Problem — Port-Channel Down

Check:

```cisco
show interfaces port-channel 1
show etherchannel summary
```

Then inspect all members.

```cisco
show interfaces status
```

Determine whether the problem is:

- Physical.
- Protocol.
- Configuration.
- VLAN/trunk.
- STP.

---

# 70. Windows and Linux Commands

EtherChannel is configured on network switches, so direct Windows/Linux EtherChannel configuration is:

> **Not applicable for a normal endpoint lab.**

Endpoints can validate connectivity:

### Windows

```powershell
ipconfig /all
ping 192.168.10.20
arp -a
```

### Linux

```bash
ip addr
ip route
ip neigh
ping 192.168.10.20
```

For Linux servers using NIC bonding/teaming, the technology and commands are different from Cisco EtherChannel and should not be confused with Cisco switch-side configuration.

---

# 71. Real-World Scenario — Data Center / Campus 🏢

Suppose an access switch needs a high-capacity uplink to a distribution switch.

The organization has four 10-Gbps-capable interfaces.

A possible design is:

```text
ACCESS ================= DISTRIBUTION
       4-member LACP
       Port-Channel
```

Benefits include:

- Aggregate bandwidth.
- Member-link redundancy.
- Simplified STP topology.
- Better utilization of available interfaces.

The engineer should still verify:

```cisco
show etherchannel summary
show interfaces trunk
show spanning-tree
```

---

# 72. Security and Operational Best Practices 🔐

- Prefer LACP when interoperability and standards-based negotiation are desired.
- Configure both sides intentionally.
- Use consistent member-interface configuration.
- Do not mix LACP and PAgP.
- Avoid static `on` mode unless intentionally designed.
- Restrict trunk VLANs.
- Use a consistent native VLAN where required.
- Document Port-Channel membership.
- Monitor member-link failures.
- Verify hashing behavior when troubleshooting traffic distribution.
- Do not assume a single flow will use every physical link.
- Keep STP enabled where the Layer 2 design requires it.

---

# 73. EtherChannel and STP Security

EtherChannel does not make a network immune to Layer 2 attacks or loops.

You should still consider:

```text
STP
BPDU Guard
Root Guard
Loop Guard
Port security
DHCP Snooping
Dynamic ARP Inspection
VLAN segmentation
```

These are separate technologies with different purposes.

---

# 74. Common Beginner Mistakes ❌

- Thinking EtherChannel means one physical cable.
- Thinking every packet is split equally across all links.
- Thinking one TCP connection automatically uses the full bundle capacity.
- Confusing LACP with PAgP.
- Using passive + passive and expecting LACP to form.
- Using auto + auto and expecting PAgP to form.
- Mixing LACP and PAgP.
- Ignoring member configuration consistency.
- Configuring the Port-Channel but forgetting to verify members.
- Thinking EtherChannel replaces STP.
- Ignoring trunk/native/allowed VLAN configuration.
- Assuming all Cisco platforms support every EtherChannel command identically.

---

# 75. Scenario Challenge 🚨

You are configuring two distribution switches.

Topology:

```text
DIST1 ================= DIST2
 Gi0/1 ================ Gi0/1
 Gi0/2 ================ Gi0/2
 Gi0/3 ================ Gi0/3
 Gi0/4 ================ Gi0/4
              Po10
```

Requirements:

- Use LACP.
- Carry VLANs 10, 20, 30, and 99.
- Use VLAN 99 as the intentional native VLAN.
- Use Rapid PVST+.
- Verify that all four members are bundled.

### Your tasks

1. Configure the four member interfaces.
2. Create Port-Channel 10.
3. Configure Po10 as a trunk.
4. Restrict allowed VLANs.
5. Configure the native VLAN.
6. Verify LACP.
7. Verify STP.
8. Shut down one member and verify continued operation.
9. Restore the member.
10. Document the final design.

### Useful commands

```cisco
show etherchannel summary
show etherchannel detail
show interfaces port-channel 10
show interfaces trunk
show spanning-tree
show etherchannel load-balance
```

---

# 76. Practice Exercises ✍️

## Beginner

1. What is EtherChannel?
2. What is a Port-Channel?
3. Why is EtherChannel useful?
4. What is LACP?
5. What is PAgP?
6. What does `mode active` mean?
7. What does `mode passive` mean?

## Intermediate

8. Why does passive + passive LACP fail to form?
9. Why does auto + auto PAgP fail to form?
10. Why does EtherChannel help STP?
11. What is the difference between LACP and PAgP?
12. Why must member interfaces be compatible?
13. Why might one traffic flow use only one physical link?

## Advanced

14. Explain EtherChannel load balancing.
15. Explain how STP treats a Port-Channel.
16. Design a four-link redundant trunk between two distribution switches.
17. Explain the operational risks of static `mode on`.
18. Explain how EtherChannel can improve both bandwidth and availability.

## Troubleshooting

19. LACP passive/passive does not form. Why?
20. One member is suspended. What should you check?
21. EtherChannel is up but VLAN 20 does not work. What do you investigate?
22. A user reports only one link is carrying traffic. Is that automatically a failure?
23. One member has a different trunk configuration. What is the likely result?

---

# 77. Interview Questions 🎤

## Beginner

### Q1. What is EtherChannel?

EtherChannel combines multiple physical Ethernet links into one logical Port-Channel.

### Q2. Why use EtherChannel?

For aggregate bandwidth, redundancy, better utilization of multiple physical links, and a simpler logical STP topology.

### Q3. What is LACP?

LACP is a standards-based link aggregation protocol used to negotiate and maintain link bundles.

### Q4. What is PAgP?

PAgP is Cisco proprietary Port Aggregation Protocol.

---

## Intermediate

### Q5. What are LACP modes?

Active and passive.

### Q6. Which LACP combinations form a bundle?

Active + active and active + passive.

### Q7. Which PAgP combinations form?

Desirable + desirable and desirable + auto.

### Q8. Does EtherChannel eliminate STP?

No. STP is still required wherever the Layer 2 topology needs loop prevention.

---

## Advanced

### Q9. How does EtherChannel distribute traffic?

It normally uses a hashing algorithm based on selected packet fields. Distribution is generally flow-based rather than equal packet-by-packet distribution.

### Q10. Why can one link appear more heavily used than another?

The hashing algorithm may map the available flows unevenly across members, especially when there are only a few flows.

### Q11. What happens if one EtherChannel member fails?

The Port-Channel can continue forwarding using remaining operational members, provided enough members remain and the logical bundle remains viable.

### Q12. Why must member ports have consistent configuration?

Because an EtherChannel represents one logical connection. Incompatible member attributes can prevent bundling or create unstable/unexpected behavior.

---

# 78. Quick Revision ⚡

```text
EtherChannel
    ↓
Multiple physical links
    ↓
One logical Port-Channel

Protocols:
LACP → standards-based
PAgP → Cisco proprietary
On   → static

LACP:
Active + Active   → YES
Active + Passive  → YES
Passive + Passive → NO

PAgP:
Desirable + Desirable → YES
Desirable + Auto      → YES
Auto + Auto           → NO

EtherChannel → combines links
STP          → prevents loops
802.1Q       → VLAN tagging
```

---

# 79. Cheat Sheet 📋

## LACP active

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode active
```

## LACP passive

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode passive
```

## PAgP desirable

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode desirable
```

## PAgP auto

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode auto
```

## Static EtherChannel

```cisco
interface range gigabitEthernet 0/1-2
 channel-group 1 mode on
```

## Configure trunk Port-Channel

```cisco
interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

## Native VLAN

```cisco
interface port-channel 1
 switchport trunk native vlan 99
```

## Verify

```cisco
show etherchannel summary
show etherchannel detail
show interfaces port-channel 1
show interfaces port-channel 1 switchport
show interfaces trunk
show etherchannel load-balance
```

---

# 80. Command Reference

| Command | Purpose |
|---|---|
| `channel-group 1 mode active` | Add interface to LACP active bundle |
| `channel-group 1 mode passive` | Add interface to LACP passive bundle |
| `channel-group 1 mode desirable` | Add interface to PAgP desirable bundle where supported |
| `channel-group 1 mode auto` | Add interface to PAgP auto bundle where supported |
| `channel-group 1 mode on` | Create static EtherChannel |
| `show etherchannel summary` | Main EtherChannel verification |
| `show etherchannel detail` | Detailed EtherChannel information |
| `show interfaces port-channel 1` | Port-Channel interface details |
| `show interfaces port-channel 1 switchport` | Logical switchport details |
| `show interfaces trunk` | Trunk and VLAN forwarding verification |
| `show etherchannel load-balance` | View load-balancing method where supported |
| `show running-config interface Po1` | View Port-Channel configuration |
| `show running-config interface Gi0/1` | View member configuration |

---

# 81. Connection to Previous Topics 🔗

This lesson builds directly on:

- Ethernet Fundamentals
- MAC Addressing
- Switching Fundamentals
- VLANs
- Trunking
- DTP
- VTP
- STP

The most important relationship is:

```text
Multiple physical links
          ↓
    EtherChannel
          ↓
   Port-Channel
          ↓
        STP
```

---

# 82. Connection to Next Topics 🔗

After EtherChannel, the next major Layer 2 topic is **Inter-VLAN Routing**.

You have now learned:

```text
VLAN
 ↓
Trunk
 ↓
DTP / VTP
 ↓
STP
 ↓
EtherChannel
 ↓
Inter-VLAN Routing
```

The next step is learning how devices in different VLANs communicate through a Layer 3 device.

---

# 83. Completion Checklist ✅

- [ ] I understand EtherChannel.
- [ ] I understand Port-Channel.
- [ ] I understand why EtherChannel is useful.
- [ ] I understand LACP.
- [ ] I understand PAgP.
- [ ] I understand static `mode on`.
- [ ] I know LACP active/passive behavior.
- [ ] I know PAgP desirable/auto behavior.
- [ ] I understand the EtherChannel negotiation matrix.
- [ ] I understand member-interface consistency.
- [ ] I understand Layer 2 EtherChannel.
- [ ] I understand trunk EtherChannel.
- [ ] I understand EtherChannel load balancing.
- [ ] I understand EtherChannel and STP.
- [ ] I can configure LACP.
- [ ] I can configure PAgP where supported.
- [ ] I can verify EtherChannel.
- [ ] I can troubleshoot a failed bundle.
- [ ] I can troubleshoot member-link failures.
- [ ] I completed the practical labs.
- [ ] I understand EtherChannel security/operational best practices.
- [ ] I can explain EtherChannel in an interview.

---

# 🏁 Final Takeaway

> **EtherChannel combines multiple physical Ethernet links into one logical Port-Channel, providing aggregate bandwidth and redundancy while simplifying the Layer 2 topology seen by STP.**

Remember:

```text
LACP
Active + Active   → forms
Active + Passive  → forms
Passive + Passive → does not form

PAgP
Desirable + Desirable → forms
Desirable + Auto      → forms
Auto + Auto           → does not form

EtherChannel ≠ STP
EtherChannel → link aggregation
STP          → loop prevention
```

And the professional principle:

> **Build the bundle intentionally, keep member interfaces consistent, verify the logical Port-Channel, and test failure of individual members.**

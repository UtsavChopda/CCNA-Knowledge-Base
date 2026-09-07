# 17. Spanning Tree Protocol (STP)

> **Learning path:** VLANs → Trunking → DTP → VTP → **Spanning Tree Protocol (STP)**

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain why Layer 2 loops are dangerous.
- Explain what STP does and why it exists.
- Understand the root bridge, Bridge ID, and path cost.
- Understand root ports, designated ports, and alternate/non-designated ports.
- Understand STP port states and the classic 802.1D transition process.
- Explain STP timers.
- Understand BPDU messages.
- Explain how STP selects paths and blocks redundant paths.
- Configure and verify STP on Cisco switches.
- Understand Rapid PVST+ and how it differs from classic 802.1D STP.
- Troubleshoot STP and Layer 2 loops.
- Understand STP security features such as PortFast, BPDU Guard, Root Guard, and Loop Guard.
- Design and troubleshoot realistic redundant-switch topologies.

---

# 1. Why Do We Need STP?

Consider this network:

```text
             +---------+
             |         |
            SW1=======SW2
             |         |
             +=========+
```

There are multiple Layer 2 paths between the switches.

At first, redundancy sounds excellent:

> “If one link fails, traffic can use the other link.”

But Ethernet switches can create a serious problem when redundant Layer 2 paths exist:

> **Layer 2 loops.**

STP was designed to prevent these loops while still allowing redundant paths to exist for resilience.

---

# 2. The Layer 2 Loop Problem 🚨

Suppose a broadcast frame enters a switched network:

```text
             SW1
            /   \
           /     \
         SW2-----SW3
```

A broadcast is flooded out multiple ports.

Because Ethernet frames do not contain a normal Layer 2 TTL field like IP packets have at Layer 3, a looping Layer 2 frame can continue circulating.

This can lead to:

- Broadcast storms.
- MAC address instability.
- High CPU utilization.
- Network congestion.
- Duplicate frames.
- Unstable connectivity.
- Complete network outage.

---

# 3. What Is STP?

**Spanning Tree Protocol (STP)** is a Layer 2 loop-prevention mechanism that logically places redundant switch paths into a loop-free topology.

The key idea is:

```text
Physical topology:

       SW1
      /   \
    SW2---SW3

Logical forwarding topology:

       SW1
      /   \
    SW2   SW3

One redundant path is blocked.
```

The physical link still exists, but STP prevents selected ports from forwarding normal Layer 2 traffic.

If the active path fails, STP can recalculate and allow a previously blocked path to participate.

---

# 4. STP's Main Goal

STP provides:

```text
Redundancy
    +
Loop prevention
    +
Automatic path selection
    ↓
Resilient Layer 2 network
```

Without STP:

```text
Redundancy → possible Layer 2 loop
```

With STP:

```text
Redundancy → controlled forwarding topology
```

---

# 5. Simple Real-World Analogy 🛣️

Imagine three cities connected by roads:

```text
City A ----- City B
  \          /
   \        /
     City C
```

Multiple roads are useful for backup.

But imagine traffic-control rules that prevent vehicles from endlessly circulating in a loop.

STP works similarly for Layer 2 paths:

> Keep the redundant roads physically available, but logically block enough paths to prevent a loop.

---

# 6. STP and OSI Layer

STP operates at **Layer 2 — Data Link Layer**.

It works with Ethernet switching and MAC addresses.

```text
Layer 3 → IP routing
Layer 2 → Ethernet switching + STP
Layer 1 → cables/signals
```

STP is therefore not a routing protocol.

---

# 7. BPDU — Bridge Protocol Data Unit

STP switches exchange special control frames called **BPDUs**.

BPDUs allow switches to communicate STP information.

Conceptually:

```text
SW1
 │
 │ BPDU
 ↓
SW2
 │
 │ BPDU
 ↓
SW3
```

BPDUs contain information used in STP calculations, including bridge identity and path information.

### Important

BPDUs are control-plane frames for STP. They are not ordinary user data frames.

---

# 8. Bridge ID (BID) ⭐

STP uses a **Bridge ID** to identify switches and help elect the root bridge.

Conceptually, the Bridge ID includes:

```text
Bridge Priority
      +
Extended System ID / VLAN information
      +
MAC Address
```

The exact encoding and display depend on the STP mode and platform.

### Root election rule

> **The switch with the lowest Bridge ID becomes the root bridge.**

The comparison begins with the bridge priority and then uses the MAC address as a tie-breaker when required.

---

# 9. Bridge Priority

A common default bridge priority is:

```text
32768
```

The actual displayed value can include the VLAN/System ID extension.

You can intentionally influence root election by configuring a lower priority.

Example:

```cisco
Switch(config)# spanning-tree vlan 10 priority 24576
```

Lower priority is preferred.

### Easy memory trick 🧠

```text
STP root election:
LOWER BID wins
LOWER priority is better
```

---

# 10. Root Bridge ⭐

The **root bridge** is the central reference point for a spanning-tree instance.

Every other switch calculates its best path toward the root.

Example:

```text
             Root Bridge
                 SW1
                /   \
               /     \
             SW2-----SW3
```

SW2 and SW3 select paths toward SW1.

### Important

The root bridge is selected independently for each STP instance/VLAN in per-VLAN STP implementations such as Rapid PVST+.

---

# 11. Root Port

On a non-root switch, the **root port** is the port that provides the best path toward the root bridge.

Each non-root switch normally has one root port per spanning-tree instance.

Example:

```text
             SW1
            ROOT
           /    \
         RP      \
        SW2------SW3
                 RP
```

The exact port selection depends on STP path cost and tie-breakers.

### Key rule

> The root bridge does not have a root port.

---

# 12. Designated Port

A **designated port** is the forwarding port selected for a network segment by STP.

A simple conceptual model:

```text
Root Bridge
   ↓
Designated ports
   ↓
Non-root switches
```

Every active Layer 2 segment needs a path toward the root, and STP selects designated ports to provide that forwarding path.

---

# 13. Alternate / Non-Designated Port

When multiple paths exist, STP may place one port into a non-forwarding role to break the loop.

In Rapid STP terminology, this is commonly an **alternate port**.

Example:

```text
             SW1
             ROOT
            /   \
           /     \
         SW2-----SW3
               X
          alternate path
```

The redundant path remains available but does not forward normal traffic while STP considers it redundant.

---

# 14. STP Port Roles Summary

| Role | Meaning |
|---|---|
| Root | Reference switch for the spanning tree |
| Root Port | Best path toward root on a non-root switch |
| Designated Port | Forwarding port selected for a segment |
| Alternate Port | Backup path that is not currently forwarding in Rapid STP |

> Terminology differs slightly between classic STP and Rapid STP. Learn the concepts and verify the exact output on your platform.

---

# 15. STP Port States — Classic 802.1D

Classic STP uses these states:

```text
Blocking
   ↓
Listening
   ↓
Learning
   ↓
Forwarding
```

A port can also be administratively disabled.

### Blocking

The port does not forward normal user frames or learn MAC addresses. It can receive STP control information.

### Listening

The switch prepares for forwarding and participates in STP calculations, but does not yet learn MAC addresses as a normal forwarding port.

### Learning

The switch begins learning MAC addresses but does not yet forward normal user frames.

### Forwarding

The port forwards normal traffic and learns MAC addresses.

---

# 16. STP Timers

Classic 802.1D STP uses important timers:

| Timer | Common default | Purpose |
|---|---:|---|
| Hello | 2 seconds | BPDU transmission interval |
| Forward Delay | 15 seconds | Time used for listening/learning transitions |
| Max Age | 20 seconds | Determines when received information is considered old |

These values are commonly taught for classic 802.1D STP.

### Important

Do not manually change STP timers casually. Modern Rapid STP mechanisms provide much faster convergence than classic 802.1D.

---

# 17. Classic STP Convergence

A simplified classic STP transition is:

```text
Blocking
   ↓
Listening
   ↓
Learning
   ↓
Forwarding
```

This process can take significantly longer than modern rapid-convergence mechanisms.

That is one reason Rapid STP became important.

---

# 18. Rapid STP

**Rapid Spanning Tree Protocol (RSTP)** is defined by IEEE 802.1w and provides faster convergence than classic 802.1D STP.

Cisco commonly implements per-VLAN rapid STP as **Rapid PVST+**.

Example:

```cisco
Switch(config)# spanning-tree mode rapid-pvst
```

Verify:

```cisco
show spanning-tree
```

---

# 19. Rapid PVST+

**Rapid PVST+** provides a separate spanning-tree instance for each VLAN.

Example:

```text
VLAN 10 → STP instance
VLAN 20 → STP instance
VLAN 30 → STP instance
```

This allows different VLANs to potentially use different forwarding paths.

### Important

Rapid PVST+ is a Cisco implementation based on RSTP concepts and provides per-VLAN spanning trees.

---

# 20. PVST+ vs Rapid PVST+

| Feature | PVST+ | Rapid PVST+ |
|---|---|---|
| Basis | 802.1D concepts | 802.1w/RSTP concepts |
| STP instance | Per VLAN | Per VLAN |
| Convergence | Slower | Faster |
| Cisco | Yes | Yes |
| Common learning focus | Legacy/classic behavior | Modern Cisco per-VLAN STP |

---

# 21. STP Path Cost

STP uses path cost to select the best path toward the root.

Generally:

> **Lower total path cost is preferred.**

The exact default cost values depend on the STP cost method and interface speed/platform.

Example concept:

```text
                 SW1 ROOT
                /        \
          cost 4          cost 19
              /            \
            SW2----------SW3
                cost 4
```

A switch compares the total path cost toward the root and chooses the best available path.

---

# 22. STP Tie-Breakers

If multiple paths have equal cost, STP uses additional information to choose a preferred path.

A useful simplified order is:

1. Lowest root bridge ID.
2. Lowest root path cost.
3. Lowest sender bridge ID.
4. Lowest sender port ID.

Exact selection behavior depends on the specific STP role/decision being made and platform implementation.

### Exam tip 🧠

> **Lowest BID and lowest cost are your first concepts to master; then learn the detailed tie-breakers.**

---

# 23. Example — Root Bridge Election

```text
SW1 → priority 32768, MAC ...AA
SW2 → priority 32768, MAC ...BB
SW3 → priority 24576, MAC ...CC
```

SW3 has the lowest priority.

Therefore:

```text
SW3 = Root Bridge
```

The MAC address is not the deciding factor when a lower priority already wins.

---

# 24. Example — Equal Priority

```text
SW1 → priority 32768, MAC ...0011
SW2 → priority 32768, MAC ...0022
```

With equal priority and other relevant factors equal:

```text
Lower MAC address → wins root election
```

Therefore SW1 would win in this simplified example.

---

# 25. Topology 1 — Simple Two-Switch Network

```text
PC1 --- SW1 ================= SW2 --- PC2
```

There is only one Layer 2 path.

### STP behavior

STP has no redundant path to block.

Both switch ports can forward if otherwise operational.

---

# 26. Topology 2 — Triangle

```text
             SW1
            /   \
           /     \
         SW2-----SW3
```

This is one of the most important STP topologies.

There are three Layer 2 paths forming a loop.

STP blocks one logical path.

---

# 27. Topology 3 — Square

```text
       SW1 -------- SW2
        |            |
        |            |
       SW4 -------- SW3
```

Multiple loops can exist.

STP calculates a loop-free forwarding topology and places selected ports into non-forwarding roles.

---

# 28. Topology 4 — Redundant Distribution Links

```text
          DIST1 ===== DIST2
           /  \       /  \
          /    \     /    \
        ACC1   ACC2 ACC3   ACC4
```

Redundancy is desirable, but STP is required wherever the Layer 2 topology contains loops.

---

# 29. Topology 5 — Access Layer Redundancy

```text
             DIST1
            /     \
           /       \
        ACC1=======ACC2
```

The ACC1-ACC2 path can create a loop.

STP determines which path should forward.

---

# 30. Topology 6 — Multiple VLANs with Rapid PVST+

```text
        SW1 ================= SW2
             VLAN 10/20/30
```

With Rapid PVST+:

```text
VLAN 10 → STP instance 10
VLAN 20 → STP instance 20
VLAN 30 → STP instance 30
```

Different VLANs can have different root/forwarding decisions.

---

# 31. Topology 7 — STP Load Distribution

A common design can intentionally use different root bridges for different VLANs.

```text
VLAN 10 → SW1 root
VLAN 20 → SW2 root
```

This can distribute traffic across redundant paths while preserving loop prevention.

---

# 32. Topology 8 — Layer 2 Loop from an Unmanaged Switch

```text
             +---------+
             | Switch  |
             +---------+
              |       |
              +-------+
```

If an unmanaged switch connects two access ports together, it can create a Layer 2 loop.

STP should detect the redundant path in a compatible topology, but edge-port design and protection features are still important.

---

# 33. Topology 9 — Accidental Cable Loop

```text
SW1 Gi0/1 -------- SW2 Gi0/1
SW1 Gi0/2 -------- SW2 Gi0/2
```

Two physical links between the same switches create redundancy.

STP normally allows one path to forward while placing the other into a non-forwarding role.

> If both links are intentionally bundled using EtherChannel, the design is different; EtherChannel is covered later.

---

# 34. Topology 10 — STP + EtherChannel Preview

```text
SW1 ================= SW2
      multiple links
       as one bundle
```

With EtherChannel, multiple physical links can operate as one logical port-channel.

STP sees the logical bundle rather than treating each member link as an independent Layer 2 path.

This reduces some STP topology complexity and allows multiple physical links to forward as part of the same logical connection.

EtherChannel will be covered separately.

---

# 35. Cisco STP Mode Configuration

Check the current mode:

```cisco
show spanning-tree summary
```

Configure Rapid PVST+ where supported:

```cisco
configure terminal
spanning-tree mode rapid-pvst
end
```

Verify:

```cisco
show spanning-tree summary
```

---

# 36. Verify the Root Bridge

Use:

```cisco
show spanning-tree
```

Look for:

- Root ID
- Bridge ID
- Root path cost
- Root port
- Port roles
- Port states

You can also inspect a specific VLAN:

```cisco
show spanning-tree vlan 10
```

---

# 37. Configure Root Primary

Cisco IOS may provide a convenient command such as:

```cisco
spanning-tree vlan 10 root primary
```

This attempts to make the switch the primary root for the specified VLAN using an appropriate priority adjustment.

Verify:

```cisco
show spanning-tree vlan 10
```

### Important

Do not assume the command guarantees the final role under every topology. Always verify the resulting root bridge.

---

# 38. Configure Root Secondary

Example:

```cisco
spanning-tree vlan 10 root secondary
```

This provides a switch with a priority intended to make it a backup candidate for the root.

Verify:

```cisco
show spanning-tree vlan 10
```

---

# 39. Configure Explicit Priority

Example:

```cisco
spanning-tree vlan 10 priority 24576
```

For another switch:

```cisco
spanning-tree vlan 10 priority 28672
```

Lower priority is preferred.

---

# 40. Configure STP Port Cost

Example:

```cisco
interface gigabitEthernet 0/1
 spanning-tree vlan 10 cost 10
```

This changes the STP cost for VLAN 10 on the interface where supported.

Verify:

```cisco
show spanning-tree vlan 10
```

Use cost changes carefully. Root placement and topology design should normally be understood before manipulating individual path costs.

---

# 41. Configure Port Priority

STP can also use port priority as a tie-breaker.

Example:

```cisco
interface gigabitEthernet 0/1
 spanning-tree vlan 10 port-priority 64
```

Lower port priority is preferred when the relevant tie-breaker is reached.

Verify:

```cisco
show spanning-tree vlan 10
```

---

# 42. PortFast

**PortFast** allows an edge port to move quickly toward forwarding rather than going through normal legacy STP transition delays.

It is intended for ports connected to end devices, not switch-to-switch links.

Example:

```cisco
interface gigabitEthernet 0/2
 spanning-tree portfast
```

A common modern syntax is:

```cisco
spanning-tree portfast edge
```

where supported.

### Critical warning ⚠️

> **Do not enable PortFast on a normal switch-to-switch trunk just to make it come up faster.**

PortFast should be used on genuine edge ports according to the platform's recommended configuration.

---

# 43. BPDU Guard 🔐

BPDU Guard protects an edge/PortFast port if STP BPDUs are received unexpectedly.

Example:

```cisco
interface gigabitEthernet 0/2
 spanning-tree portfast
 spanning-tree bpduguard enable
```

If a BPDU is received, the port can be placed into an error-disabled state depending on platform behavior/configuration.

### Why?

A user should not normally connect another switch to an endpoint port.

BPDU Guard helps detect this kind of violation.

---

# 44. Global BPDU Guard

Some environments configure BPDU Guard as the default behavior for PortFast/edge ports:

```cisco
spanning-tree portfast edge bpduguard default
```

Syntax varies by platform and software.

Verify:

```cisco
show spanning-tree summary
```

---

# 45. Root Guard

**Root Guard** prevents a port from becoming a path toward an unexpected superior root bridge.

Example:

```cisco
interface gigabitEthernet 0/3
 spanning-tree guard root
```

It is useful where the network administrator wants to prevent a downstream switch from influencing root-bridge selection through a particular interface.

### Important

Root Guard is not a replacement for BPDU Guard.

---

# 46. Loop Guard

**Loop Guard** helps protect against certain situations where BPDUs stop arriving on a non-designated port even though the underlying topology may still be capable of creating a loop.

Example:

```cisco
interface gigabitEthernet 0/4
 spanning-tree guard loop
```

It can place a port into a loop-inconsistent state rather than allowing it to transition incorrectly into forwarding.

### Key idea

```text
BPDU Guard → unexpected BPDU on edge port
Root Guard  → unexpected superior root information
Loop Guard  → protects against certain unidirectional BPDU-loss scenarios
```

---

# 47. UDLD and STP

**Unidirectional Link Detection (UDLD)** is not an STP feature, but it can complement STP by detecting certain unidirectional link failures.

Example verification:

```cisco
show udld
```

The exact commands and support vary by platform.

---

# 48. STP Verification Command Reference ⭐

## Main command

```cisco
show spanning-tree
```

## Specific VLAN

```cisco
show spanning-tree vlan 10
```

## Summary

```cisco
show spanning-tree summary
```

## Interface-specific STP information

```cisco
show spanning-tree interface gigabitEthernet 0/1 detail
```

## Interface status

```cisco
show interfaces status
```

## Trunks

```cisco
show interfaces trunk
```

## MAC table

```cisco
show mac address-table
```

## CDP

```cisco
show cdp neighbors
```

## LLDP

```cisco
show lldp neighbors
```

---

# 49. Practical Lab 1 — Observe STP on Two Switches

## Objective

Learn how STP appears on a simple topology.

## Topology

```text
PC1 --- SW1 ================= SW2 --- PC2
```

Verify:

```cisco
show spanning-tree
```

Then:

```cisco
show spanning-tree vlan 1
```

Record:

| Item | SW1 | SW2 |
|---|---|---|
| Root bridge? | | |
| Root port? | | |
| Designated port? | | |
| Port state | | |
| Root path cost | | |

### Goal

Become comfortable reading STP output.

---

# 50. Practical Lab 2 — Triangle STP Lab ⭐

## Topology

```text
             SW1
            /   \
           /     \
         SW2-----SW3
```

Connect the three switches in a triangle.

Verify:

```cisco
show spanning-tree
```

### Questions

1. Which switch is root?
2. Which ports are root ports?
3. Which ports are designated?
4. Which port is blocking/alternate?
5. Why is one path not forwarding?

### Goal

Understand STP physically by observing a redundant path being blocked.

---

# 51. Practical Lab 3 — Choose the Root Bridge

## Objective

Intentionally control root election.

Topology:

```text
          SW1
         /   \
       SW2---SW3
```

On SW1:

```cisco
spanning-tree vlan 10 priority 24576
```

On SW2:

```cisco
spanning-tree vlan 10 priority 28672
```

Verify all switches:

```cisco
show spanning-tree vlan 10
```

### Expected result

SW1 should be preferred as root if the relevant competing configuration does not produce a lower Bridge ID elsewhere.

---

# 52. Practical Lab 4 — Root Primary / Secondary

On the intended primary switch:

```cisco
spanning-tree vlan 10 root primary
```

On the backup:

```cisco
spanning-tree vlan 10 root secondary
```

Verify:

```cisco
show spanning-tree vlan 10
```

### Goal

Understand planned root and backup-root design.

---

# 53. Practical Lab 5 — Link Failure and Recovery

## Topology

```text
             SW1
            /   \
           /     \
         SW2-----SW3
```

1. Verify STP.
2. Identify the blocked/alternate path.
3. Shut down one active forwarding link.
4. Observe STP convergence.
5. Verify the previously redundant path.

Example:

```cisco
interface gigabitEthernet 0/1
 shutdown
```

Then verify:

```cisco
show spanning-tree
```

Restore:

```cisco
no shutdown
```

### Goal

Understand that STP is not only about blocking; it also provides redundancy and recovery.

---

# 54. Practical Lab 6 — Rapid PVST+

Check current mode:

```cisco
show spanning-tree summary
```

Configure where supported:

```cisco
configure terminal
spanning-tree mode rapid-pvst
end
```

Verify:

```cisco
show spanning-tree summary
```

Then repeat the triangle lab.

### Goal

Compare the conceptual behavior of classic STP and Rapid PVST+.

---

# 55. Practical Lab 7 — PortFast + BPDU Guard

## Topology

```text
PC1 --- SW1
```

Configure the endpoint port:

```cisco
interface gigabitEthernet 0/2
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
```

Verify:

```cisco
show spanning-tree interface gigabitEthernet 0/2 detail
```

### Challenge

Temporarily connect a test switch instead of a PC and observe the behavior in a controlled lab.

> Never perform a BPDU Guard violation test on a production port.

---

# 56. Practical Lab 8 — Root Guard

Create a small redundant topology and configure:

```cisco
interface gigabitEthernet 0/3
 spanning-tree guard root
```

Then attempt to introduce a superior STP bridge on the protected side in a controlled lab.

Verify:

```cisco
show spanning-tree inconsistentports
```

where supported.

### Goal

Understand how Root Guard protects root placement.

---

# 57. Practical Lab 9 — Loop Guard

On an appropriate redundant link in a controlled lab:

```cisco
interface gigabitEthernet 0/4
 spanning-tree guard loop
```

Verify:

```cisco
show spanning-tree inconsistentports
```

where supported.

### Goal

Understand the reason for Loop Guard rather than memorizing the command.

---

# 58. Practical Lab 10 — Full Campus STP Challenge 🏫

## Topology

```text
                 DIST1
                /     \
               /       \
             ACC1=====ACC2
               \       /
                \     /
                 DIST2
```

VLANs:

| VLAN | Purpose |
|---:|---|
| 10 | Students |
| 20 | Faculty |
| 30 | Administration |
| 99 | Management |

### Requirements

- VLAN 10 root → DIST1
- VLAN 20 root → DIST2
- Redundant links must remain available.
- User-facing ports should be edge/PortFast ports.
- Protect edge ports with BPDU Guard.
- Protect selected infrastructure boundaries with appropriate STP guards.

### Verification

```cisco
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree summary
show interfaces trunk
```

### Goal

Build a design where VLANs use redundancy without creating forwarding loops.

---

# 59. Troubleshooting Methodology 🔧

When STP causes unexpected behavior:

```text
1. Check physical links
        ↓
2. Check interface status
        ↓
3. Check STP mode
        ↓
4. Identify root bridge
        ↓
5. Identify root ports
        ↓
6. Identify designated/alternate ports
        ↓
7. Check path costs
        ↓
8. Check port priorities
        ↓
9. Check PortFast/edge settings
        ↓
10. Check STP guards
        ↓
11. Check VLAN-specific STP
        ↓
12. Check trunks and allowed VLANs
        ↓
13. Check MAC-table instability
```

---

# 60. Problem — Unexpected Root Bridge

### Symptom

The wrong switch becomes root.

### Check

```cisco
show spanning-tree vlan 10
```

Look at:

- Root ID
- Bridge ID
- Priority
- MAC address

### Possible fix

Configure an intentional lower priority on the desired root:

```cisco
spanning-tree vlan 10 priority 24576
```

Then verify.

---

# 61. Problem — Expected Port Is Blocked

### Check

```cisco
show spanning-tree vlan 10
```

Determine:

- Root path cost.
- Port role.
- Port priority.
- Neighbor bridge ID.

Do not simply force the port into forwarding without understanding why STP selected another path.

---

# 62. Problem — Users Lose Connectivity After a Link Failure

### Check

```cisco
show spanning-tree vlan 10
show interfaces status
show interfaces trunk
```

Determine whether:

- STP reconverged.
- The backup path is available.
- VLAN 10 is allowed on the backup trunk.
- The interface is physically operational.

---

# 63. Problem — Broadcast Storm 🚨

### Symptoms

- Network becomes extremely slow.
- Switch CPU increases.
- Broadcast traffic becomes excessive.
- MAC addresses move between ports.

### Check

```cisco
show spanning-tree
show mac address-table
show interfaces counters errors
```

Look for:

- Loops.
- Unexpected switch connections.
- STP disabled or bypassed.
- Incorrect PortFast use.
- EtherChannel misconfiguration.

---

# 64. Problem — Port Err-Disabled After BPDU Guard

### Check

```cisco
show interfaces status err-disabled
show interfaces gigabitEthernet 0/2 status
```

Depending on platform/configuration, recovery may require:

```cisco
interface gigabitEthernet 0/2
 shutdown
 no shutdown
```

But first determine **why** the BPDU arrived.

Do not simply re-enable the port without fixing the underlying topology/security violation.

---

# 65. Problem — Native VLAN / Trunk Issue

STP can be correct while VLAN connectivity is wrong.

Check:

```cisco
show interfaces trunk
show spanning-tree vlan 10
show vlan brief
```

Remember:

> STP prevents loops; it does not guarantee that the VLAN configuration is correct.

---

# 66. MAC Address Flapping

A common symptom of a Layer 2 loop is a MAC address appearing on different switch ports repeatedly.

Example concept:

```text
MAC AAAA.BBBB.CCCC
     ↓
Gi0/1
     ↓
Gi0/2
     ↓
Gi0/1
     ↓
Gi0/2
```

This can indicate a loop, redundant path problem, or another topology/configuration issue.

Check:

```cisco
show mac address-table dynamic
```

Then correlate the MAC with STP and physical topology.

---

# 67. Windows and Linux Commands

STP is a switch-side Layer 2 protocol.

Therefore direct STP configuration from Windows/Linux endpoints is:

> **Not applicable.**

Endpoints can still help verify the impact:

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

Use Cisco switch commands to diagnose STP itself.

---

# 68. Real-World Scenario — Enterprise Campus 🏢

Imagine:

```text
                 DIST1
                /     \
               /       \
             ACC1=====ACC2
               \       /
                \     /
                 DIST2
```

VLAN 10 carries users.

VLAN 20 carries faculty.

VLAN 30 carries administration.

A professional design might intentionally select:

```text
VLAN 10 root → DIST1
VLAN 20 root → DIST2
```

This can use redundant paths more effectively while STP maintains loop-free forwarding.

Edge ports can use:

```text
PortFast/edge
BPDU Guard
```

Infrastructure links can use appropriate STP protections based on topology.

---

# 69. Security Best Practices 🔐

### Edge ports

Use PortFast/edge only where the port connects to an end device.

### BPDU Guard

Protect edge ports against unexpected BPDUs.

### Root Guard

Protect selected boundaries from unexpected superior root information.

### Loop Guard

Protect selected redundant links from certain BPDU-loss scenarios.

### Explicit STP design

Choose root bridges intentionally instead of allowing an arbitrary switch to win.

### Do not disable STP casually

Removing STP from a redundant Layer 2 topology can create severe outages.

---

# 70. Common Beginner Mistakes ❌

- Thinking STP is a routing protocol.
- Thinking STP removes physical redundancy.
- Thinking a blocked STP port is physically disconnected.
- Forgetting that the root bridge has no root port.
- Assuming the lowest MAC always becomes root.
- Ignoring bridge priority.
- Enabling PortFast on switch-to-switch links.
- Using BPDU Guard without understanding the edge-port role.
- Confusing Root Guard and BPDU Guard.
- Confusing Loop Guard with UDLD.
- Troubleshooting STP without checking trunks/VLANs.
- Manually changing timers unnecessarily.
- Assuming all Cisco switches use exactly the same STP defaults.
- Forgetting that per-VLAN STP can produce different root bridges for different VLANs.

---

# 71. STP vs EtherChannel

These technologies solve related but different problems.

| Technology | Main purpose |
|---|---|
| STP | Prevent Layer 2 loops |
| EtherChannel | Combine multiple physical links into one logical bundle |

Example:

```text
Without EtherChannel:
SW1 ===== SW2
SW1 ===== SW2

STP may block one path.
```

With EtherChannel:

```text
SW1 ======== SW2
   Port-Channel
```

The physical links operate as one logical connection for STP purposes.

---

# 72. STP vs Routing

A Layer 2 switch uses STP to control redundant Layer 2 paths.

A router uses routing protocols and the routing table to select Layer 3 paths.

```text
Layer 2
Ethernet
MAC
STP

Layer 3
IP
Routing table
OSPF/static routes/etc.
```

Do not mix the concepts.

---

# 73. Scenario Challenge 🚨

You are the network engineer for a college.

The network has:

```text
             DIST1
            /     \
           /       \
        ACC1=======ACC2
           \       /
            \     /
             DIST2
```

Requirements:

- VLAN 10 → Students
- VLAN 20 → Faculty
- VLAN 30 → Administration
- VLAN 99 → Management
- DIST1 should be root for VLAN 10.
- DIST2 should be root for VLAN 20.
- Access ports must be protected.
- Redundant links must remain available.

### Your tasks

1. Select the STP mode.
2. Configure root priorities.
3. Configure edge ports.
4. Enable BPDU Guard on appropriate edge ports.
5. Decide where Root Guard might be appropriate.
6. Decide where Loop Guard might be appropriate.
7. Verify every VLAN.
8. Shut down one forwarding link and observe recovery.
9. Document the final STP topology.

### Verification commands

```cisco
show spanning-tree summary
show spanning-tree vlan 10
show spanning-tree vlan 20
show spanning-tree vlan 30
show interfaces trunk
show spanning-tree inconsistentports
```

---

# 74. Practice Exercises ✍️

## Beginner

1. What problem does STP solve?
2. At which OSI layer does STP operate?
3. What is a BPDU?
4. What is a root bridge?
5. What is a root port?
6. What is a designated port?
7. Why can Layer 2 loops be dangerous?

## Intermediate

8. How does STP elect the root bridge?
9. Why is lower bridge priority preferred?
10. What is path cost?
11. Explain blocking, listening, learning, and forwarding.
12. What is Rapid STP?
13. What is Rapid PVST+?
14. Why can different VLANs have different STP roots?

## Advanced

15. Explain STP tie-breakers.
16. Design a campus network with separate roots for VLANs 10 and 20.
17. Explain how EtherChannel changes the way STP sees multiple physical links.
18. Explain the difference between Root Guard, BPDU Guard, and Loop Guard.
19. Explain how a unidirectional failure can create STP-related risk.

## Troubleshooting

20. The wrong switch is root. What do you check?
21. A port is unexpectedly blocking. What do you investigate?
22. A port became err-disabled after BPDU Guard. What does that tell you?
23. Users report a broadcast storm. What Layer 2 evidence do you collect?
24. VLAN 10 works but VLAN 20 does not. How do you separate an STP problem from a VLAN/trunk problem?

---

# 75. Interview Questions 🎤

## Beginner

### Q1. What is STP?

STP is a Layer 2 loop-prevention mechanism that creates a loop-free logical forwarding topology while preserving redundant physical paths.

### Q2. Why is STP required?

Redundant Layer 2 paths can create loops, broadcast storms, duplicate frames, and MAC instability.

### Q3. What is the root bridge?

The switch with the lowest Bridge ID for a spanning-tree instance.

### Q4. What is a root port?

The best path toward the root bridge on a non-root switch.

---

## Intermediate

### Q5. Does the root bridge have a root port?

No.

### Q6. What is a BPDU?

A Bridge Protocol Data Unit is an STP control frame used by switches to exchange spanning-tree information.

### Q7. What is the difference between STP and RSTP?

RSTP provides faster convergence and improved port-role/state behavior compared with classic 802.1D STP.

### Q8. What is Rapid PVST+?

A Cisco per-VLAN implementation based on Rapid STP concepts.

---

## Advanced

### Q9. How is the root bridge selected?

The switch with the lowest Bridge ID wins, with bridge priority considered before the MAC address when applicable.

### Q10. Why might an administrator configure different roots for different VLANs?

To intentionally distribute Layer 2 traffic across redundant paths while maintaining loop prevention.

### Q11. What does BPDU Guard do?

It protects edge/PortFast ports by reacting when unexpected BPDUs are received.

### Q12. What is Root Guard?

It prevents a protected interface from accepting a superior BPDU that could cause an unexpected switch to become the root path.

### Q13. What is Loop Guard?

It helps protect against certain conditions where expected BPDUs stop arriving and a port could otherwise transition incorrectly into forwarding.

---

# 76. Quick Revision ⚡

```text
STP = Spanning Tree Protocol
Layer 2

Purpose:
Prevent Layer 2 loops

Root election:
Lowest Bridge ID wins

Non-root switch:
Best path → Root Port

Segment:
Forwarding path → Designated Port

Redundant path:
Alternate/non-forwarding role

Classic 802.1D states:
Blocking → Listening → Learning → Forwarding

Common classic timers:
Hello = 2s
Forward Delay = 15s
Max Age = 20s

Rapid STP:
Faster convergence

Rapid PVST+:
Per-VLAN Rapid STP implementation

Security:
PortFast/Edge
BPDU Guard
Root Guard
Loop Guard
```

---

# 77. Cheat Sheet 📋

## Show STP

```cisco
show spanning-tree
```

## Show VLAN-specific STP

```cisco
show spanning-tree vlan 10
```

## Show summary

```cisco
show spanning-tree summary
```

## Rapid PVST+

```cisco
spanning-tree mode rapid-pvst
```

## Root primary

```cisco
spanning-tree vlan 10 root primary
```

## Root secondary

```cisco
spanning-tree vlan 10 root secondary
```

## Explicit priority

```cisco
spanning-tree vlan 10 priority 24576
```

## STP cost

```cisco
interface gigabitEthernet 0/1
 spanning-tree vlan 10 cost 10
```

## Port priority

```cisco
interface gigabitEthernet 0/1
 spanning-tree vlan 10 port-priority 64
```

## PortFast

```cisco
interface gigabitEthernet 0/2
 spanning-tree portfast
```

## BPDU Guard

```cisco
interface gigabitEthernet 0/2
 spanning-tree bpduguard enable
```

## Root Guard

```cisco
interface gigabitEthernet 0/3
 spanning-tree guard root
```

## Loop Guard

```cisco
interface gigabitEthernet 0/4
 spanning-tree guard loop
```

## Inconsistent ports

```cisco
show spanning-tree inconsistentports
```

---

# 78. Command Reference

| Command | Purpose |
|---|---|
| `show spanning-tree` | Full STP information |
| `show spanning-tree vlan 10` | STP information for VLAN 10 |
| `show spanning-tree summary` | STP mode and summary |
| `show spanning-tree interface Gi0/1 detail` | Detailed interface STP information |
| `spanning-tree mode rapid-pvst` | Select Rapid PVST+ where supported |
| `spanning-tree vlan 10 root primary` | Prefer switch as root |
| `spanning-tree vlan 10 root secondary` | Configure intended backup root |
| `spanning-tree vlan 10 priority 24576` | Set bridge priority |
| `spanning-tree vlan 10 cost 10` | Set VLAN-specific STP path cost |
| `spanning-tree vlan 10 port-priority 64` | Set STP port priority |
| `spanning-tree portfast` | Enable edge behavior on an appropriate endpoint port |
| `spanning-tree bpduguard enable` | Protect edge port from unexpected BPDUs |
| `spanning-tree guard root` | Enable Root Guard |
| `spanning-tree guard loop` | Enable Loop Guard |
| `show spanning-tree inconsistentports` | Display STP-inconsistent ports where supported |

---

# 79. Completion Checklist ✅

- [ ] I understand why Layer 2 loops are dangerous.
- [ ] I understand what STP does.
- [ ] I know STP operates at Layer 2.
- [ ] I understand BPDUs.
- [ ] I understand Bridge ID.
- [ ] I understand root bridge election.
- [ ] I understand bridge priority.
- [ ] I understand root ports.
- [ ] I understand designated ports.
- [ ] I understand alternate/non-forwarding paths.
- [ ] I understand classic STP states.
- [ ] I understand classic STP timers.
- [ ] I understand Rapid STP.
- [ ] I understand Rapid PVST+.
- [ ] I understand STP path cost.
- [ ] I understand STP tie-breakers.
- [ ] I can intentionally select a root bridge.
- [ ] I can verify STP using Cisco IOS commands.
- [ ] I understand PortFast.
- [ ] I understand BPDU Guard.
- [ ] I understand Root Guard.
- [ ] I understand Loop Guard.
- [ ] I can troubleshoot a Layer 2 loop.
- [ ] I completed the STP labs.
- [ ] I can explain STP in an interview.

---

# 80. Connection to Previous Topics 🔗

This lesson builds directly on:

- VLAN Fundamentals
- Trunking
- DTP
- VTP
- Ethernet and MAC Addressing
- Switching
- Collision and Broadcast Domains

The key relationship is:

```text
Multiple VLANs
     ↓
Trunks
     ↓
Multiple Layer 2 paths
     ↓
Potential loops
     ↓
STP
```

---

# 81. Connection to Next Topics 🔗

After STP, the next major Layer 2 topic is **EtherChannel**.

You will learn how multiple physical links can be bundled into one logical connection:

```text
Multiple physical links
          ↓
      EtherChannel
          ↓
    Logical Port-Channel
          ↓
     Better utilization
     + redundancy
```

This connects directly to STP because STP treats the logical EtherChannel differently from independent parallel links.

---

# 🏁 Final Takeaway

> **STP prevents Layer 2 loops by creating a loop-free logical forwarding topology while keeping redundant physical paths available for resilience.**

Remember the core flow:

```text
STP
 ↓
Elect Root Bridge
 ↓
Calculate Best Paths
 ↓
Select Root/Designated Ports
 ↓
Block Redundant Paths
 ↓
Forward Without Loops
 ↓
Recover When Topology Changes
```

And remember the security principle:

> **Design the root intentionally, protect edge ports, and never disable STP casually on a redundant Layer 2 network.**

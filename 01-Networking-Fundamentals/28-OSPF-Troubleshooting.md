# 🐛 28 — OSPF Troubleshooting

> **Welcome to the OSPF War Room.** 🚨🧠🔎
>
> A network is down. Users are complaining. Monitoring is red. Someone says: **“OSPF is broken!”**
>
> Your job is not to panic. Your job is to collect evidence, isolate the failure, fix the root cause, and prove that the network is healthy again.

---

# 🚨 What You Already Know

By now, the OSPF story should look like this:

```text
🔌 Interface
   ↓
📡 OSPF Hello
   ↓
🤝 Neighbor
   ↓
📢 LSAs
   ↓
🗺️ LSDB
   ↓
🌳 SPF
   ↓
📏 Cost
   ↓
🏆 Best Path
   ↓
🛣️ Routing Table
   ↓
🚀 Forwarding
```

When something fails, troubleshoot this chain **from the bottom up**.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🔎 Build a repeatable OSPF troubleshooting process
- 🤝 Diagnose missing OSPF neighbors
- 🧩 Understand and troubleshoot OSPF neighbor states
- ⏱️ Troubleshoot Hello/dead timer mismatches
- 🏢 Troubleshoot area mismatches
- 🚫 Find passive-interface mistakes
- 🌐 Find IP/subnet problems
- 📡 Troubleshoot network-type mismatches
- 📏 Diagnose unexpected OSPF costs
- 📢 Troubleshoot missing LSAs
- 🗺️ Troubleshoot LSDB inconsistencies
- 🛣️ Troubleshoot missing routes
- 🏆 Troubleshoot wrong path selection
- 📦 Understand MTU-related adjacency problems
- ⭐ Troubleshoot DR/BDR behavior
- 🌉 Troubleshoot multi-area OSPF
- 🧪 Use Cisco IOS show/debug commands safely
- 🏢 Work through realistic NOC-style incidents
- 👑 Complete a final OSPF troubleshooting war room

---

# 🧠 1. The Golden Rule

Never start with:

```text
❌ “What command fixes OSPF?”
```

Start with:

```text
❓ What exactly is failing?
❓ Where is it failing?
❓ What should I expect?
❓ What evidence proves the failure?
```

Professional troubleshooting:

```text
🚨 Symptom
   ↓
🔎 Evidence
   ↓
🧠 Hypothesis
   ↓
🧪 Test
   ↓
🎯 Root Cause
   ↓
🔧 Fix
   ↓
✅ Verify
   ↓
📋 Document
```

---

# 🗺️ 2. OSPF Troubleshooting Layers

Use this model every time:

```text
L1 🔌 Physical
   ↓
L2 🌐 IP / Interface
   ↓
L3 🤝 OSPF Neighbor
   ↓
L4 📢 LSA / LSDB
   ↓
L5 🌳 SPF / Cost
   ↓
L6 🛣️ Routing Table
   ↓
L7 🚀 End-to-End Application
```

If Layer 1 is broken, do not waste 20 minutes reading LSAs.

🔥 **Troubleshoot the lowest broken layer first.**

---

# 🧰 3. Your Core OSPF Toolkit

These commands should become muscle memory:

```cisco
show ip interface brief
show ip ospf
show ip ospf neighbor
show ip ospf interface
show ip ospf database
show ip route ospf
show ip route <destination>
show ip protocols
show running-config
```

Useful additional commands depending on platform/version:

```cisco
show ip ospf interface brief
show ip ospf database router
show ip ospf database network
show ip ospf database summary
show ip ospf database external
show ip ospf database self-originate
```

⚠️ Exact output and command availability can vary by Cisco IOS/IOS XE release and platform.

---

# 🔎 4. First Question — What Is the Symptom?

Different symptoms suggest different starting points.

| Symptom | Start here |
|---|---|
| No neighbor | Interface + OSPF interface parameters |
| Neighbor stuck | Neighbor state |
| FULL but route missing | LSDB + route advertisement |
| Wrong route | Cost + route selection |
| Inter-area route missing | ABR + Type 3 + Area 0 |
| Adjacency constantly resets | Link stability + timers + logs |
| DR/BDR unexpected | Network type + priority |
| Ping fails but route exists | ARP/forwarding/interface/ACL/etc. |

---

# 🟢 5. Check 1 — Interface Status

Start with:

```cisco
show ip interface brief
```

You want:

```text
Status: up
Protocol: up
```

Example:

```text
GigabitEthernet0/1    10.0.0.1    YES manual    up    up
```

If you see:

```text
administratively down
```

fix the interface state first:

```cisco
interface GigabitEthernet0/1
 no shutdown
```

---

# 🟢 6. Check 2 — IP Addressing

Inspect:

```cisco
show ip interface brief
show running-config interface GigabitEthernet0/1
```

Example:

```text
R1 = 10.0.0.1/30
R2 = 10.0.0.2/30
```

Good.

But:

```text
R1 = 10.0.0.1/30
R2 = 10.0.1.2/30
```

🚨 They are not in the same /30 transit subnet.

---

# 🤝 7. Check 3 — Is There a Neighbor?

```cisco
show ip ospf neighbor
```

Example healthy output concept:

```text
Neighbor ID     State     Interface
2.2.2.2         FULL      Gi0/1
```

If the neighbor is absent:

```text
🛑 Stop.
Do not troubleshoot routes yet.
```

First troubleshoot adjacency formation.

---

# 🧠 8. OSPF Neighbor States

Know the state machine:

```text
Down
 ↓
Init
 ↓
2-Way
 ↓
ExStart
 ↓
Exchange
 ↓
Loading
 ↓
Full
```

Each state gives you a clue.

---

# 🔴 9. State: DOWN

Meaning:

> No valid Hello relationship has been established from this neighbor perspective.

Check:

```text
🔌 Link
🌐 IP addressing
📡 OSPF enabled
🏢 Area
📡 Network type
🚫 Passive interface
⏱️ Timers
🛡️ Authentication if configured
```

Commands:

```cisco
show ip ospf interface
show ip ospf neighbor
show ip interface brief
```

---

# 🟠 10. State: INIT

INIT means the router has received a Hello from the neighbor, but the Hello does not yet show the local router as a recognized neighbor.

Think:

```text
R1 → “Hello!”
R2 → “I heard you.”
R2's Hello does not yet list R1 as a neighbor.
```

Investigate:

```text
📡 Bidirectional Hello communication
🌐 Connectivity
⚙️ Interface/network type
🛡️ Filtering/security
```

---

# 🟡 11. State: 2-WAY

2-Way means bidirectional Hello communication has been established.

On broadcast networks, not every OSPF router necessarily forms FULL adjacency with every other router because DR/BDR behavior affects adjacency formation.

So:

```text
2-WAY ≠ automatically broken
```

🔥 This is a classic beginner trap.

---

# 🟠 12. State: EXSTART

ExStart is where neighbors negotiate database exchange parameters such as master/slave relationship and begin preparing for database synchronization.

If stuck here, think:

```text
📦 MTU
🧩 Network type
⚙️ Interface parameters
```

A common lab issue is an MTU mismatch.

---

# 🟠 13. State: EXCHANGE

Routers are exchanging Database Description information.

If the relationship is stuck here, investigate:

```text
📦 MTU
📢 Database exchange
🧩 Network type
⚙️ OSPF compatibility
```

---

# 🟡 14. State: LOADING

Routers are requesting additional LSAs needed to synchronize their databases.

Think:

```text
“I know some of the map, but I still need more pieces.” 🧩
```

Check:

```cisco
show ip ospf neighbor
show ip ospf database
```

---

# 🟢 15. State: FULL

FULL means the routers have completed OSPF database synchronization for that adjacency.

But remember:

```text
FULL
 ≠
Every possible route must exist
```

You still need to verify:

```text
📢 LSAs
🗺️ LSDB
🛣️ Routing table
```

---

# ⏱️ 16. Timer Mismatch

OSPF Hellos and dead intervals must be compatible for neighbors to form.

Check:

```cisco
show ip ospf interface
```

Look for:

```text
Hello interval
Dead interval
```

Example problem:

```text
R1 Hello = 10 sec
R2 Hello = 5 sec
```

Potential result:

```text
❌ Adjacency fails
```

---

# 🧪 17. Timer Mismatch Lab

Topology:

```text
R1 ───────── R2
```

Configure OSPF normally.

Then deliberately change the Hello/dead timers on one side.

Observe:

```cisco
show ip ospf neighbor
show ip ospf interface
```

Mission:

```text
1. Identify the mismatch.
2. Predict the symptom.
3. Fix it.
4. Verify FULL.
```

---

# 🏢 18. Area Mismatch

Example:

```text
R1 interface → Area 0
R2 interface → Area 10
```

If they are supposed to form an adjacency across that link:

```text
🚨 Problem
```

Check:

```cisco
show ip ospf interface
```

Remember:

```text
Transit link neighbors
        ↓
Must participate in compatible OSPF areas
```

---

# 🧪 19. Area Mismatch Lab

Build:

```text
R1 ───────── R2
```

Configure:

```text
R1 → Area 0
R2 → Area 10
```

Observe:

```cisco
show ip ospf neighbor
```

Fix the area.

Verify:

```text
FULL
```

---

# 🚫 20. Passive Interface Disaster

Topology:

```text
R1 ───────── R2
```

If you configure:

```cisco
router ospf 1
 passive-interface GigabitEthernet0/1
```

on R1 where G0/1 is the R1-R2 transit link:

```text
❌ Hello packets suppressed
❌ Neighbor fails
```

Check:

```cisco
show ip ospf interface GigabitEthernet0/1
```

---

# 🧪 21. Passive Interface Lab

Create:

```text
Users ─ R1 ─ R2
```

Configure:

```cisco
passive-interface default
no passive-interface GigabitEthernet0/1
```

Then deliberately make the router-facing interface passive.

Troubleshoot it.

🎯 Goal: learn to recognize a configuration mistake from evidence.

---

# 📦 22. MTU Mismatch — The Classic ExStart Problem

Suppose:

```text
R1 MTU = 1500
R2 MTU = 1400
```

The OSPF adjacency may become stuck during database exchange.

A common symptom is:

```text
EXSTART
EXCHANGE
```

Check interface MTU:

```cisco
show interfaces GigabitEthernet0/1
```

Look for:

```text
MTU
```

---

# 🧪 23. MTU Troubleshooting Lab

Topology:

```text
R1 ───────── R2
```

Set different MTUs in a lab if the platform permits.

Observe:

```cisco
show ip ospf neighbor
```

Then:

```cisco
show interfaces GigabitEthernet0/1
```

🎯 Identify the mismatch and restore compatible values.

⚠️ Do not change MTU blindly in production; MTU affects more than OSPF.

---

# 🌐 24. Network Type Mismatch

OSPF network type affects behavior such as:

```text
🤝 Adjacency
🏆 DR/BDR
📢 LSA behavior
⏱️ Timers
```

Common types include:

```text
Broadcast
Point-to-Point
NBMA
Point-to-Multipoint
```

Check:

```cisco
show ip ospf interface
```

---

# 🧪 25. Network-Type Lab

Build:

```text
R1 ───────── R2
```

Inspect:

```cisco
show ip ospf interface
```

Change the OSPF network type in a controlled lab.

Observe changes in:

```text
DR/BDR behavior
Neighbor relationships
OSPF interface output
```

---

# ⭐ 26. DR/BDR Troubleshooting

Broadcast segment:

```text
        R1
         |
R2 ───── SW ───── R3
         |
        R4
```

A DR and BDR may be elected.

Check:

```cisco
show ip ospf neighbor
show ip ospf interface
```

Look for:

```text
FULL/DR
FULL/BDR
2WAY/DROTHER
```

---

# ⚠️ 27. DR/BDR Election Is Not Always “Highest IP Wins”

The selection uses:

```text
1️⃣ OSPF interface priority
2️⃣ Router ID as tie-breaker
```

A higher priority wins over a lower priority.

If priority ties:

```text
Higher Router ID wins
```

Remember:

```text
Priority first
Router ID second
```

---

# 🧪 28. DR/BDR Lab

Build:

```text
          R1
           |
R2 ─────── SW ───── R3
```

Set different OSPF interface priorities.

Verify:

```cisco
show ip ospf neighbor
```

Questions:

```text
Who is DR?
Who is BDR?
Why?
```

Then test whether changing priority affects an already-established election without restarting/reforming the segment.

---

# 🪪 29. Router ID Problems

Router IDs should be predictable.

Check:

```cisco
show ip ospf
```

If the Router ID is not what you expect, investigate:

```text
🎯 Manual router-id
🔁 OSPF process restart
🔌 Interface addresses
🔄 Loopbacks
```

If you deliberately change a Router ID in a lab, a process restart may be required for the new ID to become active.

---

# 🧪 30. Router ID Lab

Configure:

```text
R1 = 1.1.1.1
R2 = 2.2.2.2
```

Verify.

Then intentionally configure an unexpected Router ID.

Use:

```cisco
show ip ospf
```

Identify the problem and correct it.

---

# 📢 31. FULL Neighbor — But No Route

This is one of the most important troubleshooting scenarios.

You see:

```text
R1 ↔ R2 = FULL
```

But:

```text
10.20.20.0/24
```

is missing from R1's routing table.

Do not say:

> “OSPF neighbor is broken.”

The adjacency is healthy.

Move upward:

```text
🤝 Neighbor = healthy
 ↓
📢 LSA = investigate
 ↓
🗺️ LSDB = investigate
 ↓
🛣️ Route = investigate
```

---

# 🔎 32. Missing Network Advertisement

Check:

```cisco
show ip protocols
show ip ospf interface
show ip ospf database
```

Questions:

```text
❓ Is the interface participating in OSPF?
❓ Is the network statement correct?
❓ Is the interface passive intentionally?
❓ Is the interface address correct?
```

---

# 📢 33. Missing LSA

Suppose R2 should advertise a connected network.

Check:

```cisco
show ip ospf database router
```

You want to determine whether the expected topology information exists.

If the LSA is absent:

```text
Investigate advertisement/configuration.
```

If the LSA exists:

```text
Investigate SPF/route selection.
```

---

# 🗺️ 34. LSDB Comparison

Compare routers:

```cisco
show ip ospf database
```

Ask:

```text
Are expected LSAs present?
Are they from the expected router?
Are they in the expected area?
Are sequence/age values reasonable?
```

For a healthy synchronized area, routers should have consistent relevant LSDB information for that area.

---

# 🧠 35. LSDB vs Routing Table

Remember:

```text
🗺️ LSDB
“What topology information do I know?”

🛣️ Routing table
“What routes did the routing process install?”
```

Therefore:

```text
LSA exists
      ↓
Does NOT guarantee
      ↓
Route installed
```

Route installation depends on additional path and route-selection rules.

---

# 📏 36. Wrong OSPF Cost

Topology:

```text
        R2
       /  \
      /    \
    R1      R4
      \    /
       \  /
        R3
```

Expected:

```text
Upper = 20
Lower = 50
```

But R1 uses lower.

Check:

```cisco
show ip ospf interface
show ip route <destination>
```

Investigate:

```text
📏 Interface costs
🎚️ Reference bandwidth
🧩 Actual topology
🏠 Route type
```

---

# 🧪 37. Wrong-Cost Lab

Build the dual-path topology.

1. Predict preferred path.
2. Verify.
3. Change cost.
4. Verify again.
5. Explain why the path changed.

Document:

```text
Old cost:
New cost:
Old path:
New path:
Reason:
```

---

# 🌉 38. Multi-Area Route Missing

Topology:

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

If Area 10 cannot reach a network in Area 20:

Check:

```text
🤝 Adjacencies
⭐ Area 0
🏢 ABRs
📢 Type 3 LSAs
🗺️ LSDB
🛣️ O IA routes
```

Commands:

```cisco
show ip ospf
show ip ospf neighbor
show ip ospf database summary
show ip route ospf
```

---

# 🧪 39. Multi-Area Troubleshooting Lab

Build:

```text
Area 10
   |
  ABR1
   |
Area 0
   |
  ABR2
   |
Area 20
```

Break one of:

```text
❌ Area assignment
❌ Area 0 transit
❌ Network advertisement
❌ ABR interface
```

Your job:

```text
Find → Explain → Fix → Verify
```

---

# 🌐 40. External Route Troubleshooting

Topology:

```text
OSPF Core ─ ASBR ─ 🌐 External Network
```

If an external route is missing, investigate:

```text
🏢 ASBR
📢 Redistribution/external advertisement
🗺️ External LSAs
🛣️ Route selection
```

Useful command where supported:

```cisco
show ip ospf database external
```

External routing is a deeper topic, but the troubleshooting framework is the same.

---

# 🔄 41. Route Flapping

Symptom:

```text
Route appears
 ↓
Route disappears
 ↓
Route appears again
 ↓
😵 Users complain
```

Possible causes:

```text
🔌 Physical instability
⚡ Interface errors
📡 Neighbor resets
⏱️ Timer problems
📢 Repeated topology changes
```

Check:

```cisco
show ip ospf neighbor
show interfaces
show logging
show ip ospf
```

---

# 🧪 42. Flapping Lab

Create a redundant topology.

Then repeatedly shut/no-shut a transit interface in a lab.

Observe:

```text
🤝 Neighbor state
📢 LSA changes
🛣️ Route changes
```

Goal:

> Understand the chain reaction of a topology change.

---

# 📊 43. Interface Errors Matter

OSPF is a routing protocol, but routing protocols depend on the underlying link.

Check:

```cisco
show interfaces GigabitEthernet0/1
```

Look for:

```text
CRC
input errors
output errors
drops
duplex/speed issues
```

A routing symptom may actually have a Layer 1/2 root cause.

---

# 🧪 44. Physical-Layer OSPF Lab

Topology:

```text
R1 ───── SW ───── R2
```

Introduce a physical/link issue if your lab platform supports it.

Use:

```cisco
show ip interface brief
show interfaces
show ip ospf neighbor
```

Explain which symptom appears first.

---

# 🛡️ 45. Authentication Mismatch

If OSPF authentication is configured, incompatible authentication settings can prevent adjacency.

Check the interface and OSPF configuration:

```cisco
show running-config interface <interface>
show ip ospf interface <interface>
```

Potential causes:

```text
🔐 Authentication type mismatch
🔑 Key mismatch
⚙️ Incorrect configuration
```

Only investigate this when authentication is actually part of the design.

---

# 🧪 46. Authentication Troubleshooting Lab

In a controlled lab:

```text
R1 ───────── R2
```

Configure compatible authentication.

Then intentionally create a mismatch.

Observe neighbor behavior.

Restore compatibility and verify:

```text
FULL
```

---

# 🔥 47. OSPF Troubleshooting Decision Tree

```text
                 🚨 OSPF Problem
                       |
                Interface up/up?
                  /          \
                NO            YES
                |              |
             Fix L1/L2     Neighbor FULL?
                              /      \
                            NO        YES
                            |          |
                     Check adjacency  Route present?
                     parameters       /       \
                                    NO         YES
                                    |            |
                              Check LSDB      Wrong path?
                              / LSA           /      \
                                            YES       NO
                                             |          |
                                          Cost/type   Test forwarding
                                          / topology
```

🔥 Memorize the **logic**, not the drawing.

---

# 🗺️ 48. Topology 1 — Two-Router Adjacency

```text
R1 ───────── R2
```

Use for:

```text
Timers
Area
MTU
Network type
```

---

# 🗺️ 49. Topology 2 — Triangle

```text
       R2
      /  \
     /    \
   R1 ─── R3
```

Use for:

```text
Cost
SPF
Redundancy
```

---

# 🗺️ 50. Topology 3 — Broadcast Segment

```text
       R1
        |
R2 ─── SW ─── R3
        |
       R4
```

Use for:

```text
DR/BDR
2-Way
Network type
```

---

# 🗺️ 51. Topology 4 — Hub and Spoke

```text
       R2
        |
R3 ─── R1 ─── R4
        |
       R5
```

Use for:

```text
Neighbor failures
```

---

# 🗺️ 52. Topology 5 — Dual Path

```text
        R2
       /  \
      /    \
    R1      R4
      \    /
       \  /
        R3
```

Use for:

```text
Wrong path
Cost
ECMP
```

---

# 🗺️ 53. Topology 6 — Area 0 + Area 10

```text
Area 10 ─ ABR ─ Area 0
```

Use for:

```text
O IA
Type 3
ABR
```

---

# 🗺️ 54. Topology 7 — Three Areas

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Use for:

```text
Inter-area failures
```

---

# 🗺️ 55. Topology 8 — Dual ABR

```text
         Area 0
       /        \
    ABR-1      ABR-2
      \          /
       \ Area 10 /
```

Use for:

```text
Redundant area connectivity
```

---

# 🗺️ 56. Topology 9 — Campus

```text
              CORE
            /      \
        ABR-1      ABR-2
         /            \
     Building A     Building B
```

Use for:

```text
Enterprise troubleshooting
```

---

# 🗺️ 57. Topology 10 — Branch Network

```text
Branch-A ─┐
Branch-B ─┼── HQ
Branch-C ─┘
```

Use for:

```text
WAN failures
```

---

# 🗺️ 58. Topology 11 — Hospital

```text
             Core-1 ===== Core-2
              /              \
         Clinical          Admin
              \              /
                Data Center
```

Use for:

```text
High availability
Critical routes
```

---

# 🗺️ 59. Topology 12 — External Edge

```text
Area 0 ─ ASBR ─ 🌐 ISP
```

Use for:

```text
External route troubleshooting
```

---

# 🧪 60. Lab 1 — OSPF From Scratch

Start with:

```text
R1 ─ R2 ─ R3
```

Build the entire configuration from a blank topology.

Verify:

```cisco
show ip ospf neighbor
show ip route ospf
```

Then intentionally break one parameter.

---

# 🧪 61. Lab 2 — No Neighbor Challenge

Create:

```text
R1 ─ R2
```

Break:

```text
Area
```

Find it without looking at the answer.

---

# 🧪 62. Lab 3 — Timer Mismatch Challenge

Break:

```text
Hello/dead timer
```

Use:

```cisco
show ip ospf interface
```

Fix it.

---

# 🧪 63. Lab 4 — Passive Interface Challenge

Break:

```text
Router-facing interface → passive
```

Identify why the neighbor disappears.

---

# 🧪 64. Lab 5 — MTU Challenge

Create an MTU mismatch.

Expected investigation:

```text
Neighbor state
 ↓
ExStart/Exchange
 ↓
Interface MTU
```

---

# 🧪 65. Lab 6 — DR/BDR Challenge

Build a broadcast segment with three or more routers.

Change interface priorities.

Predict the DR/BDR.

Verify.

---

# 🧪 66. Lab 7 — Missing Route Challenge

Keep the neighbor FULL.

Remove a network advertisement.

Goal:

```text
FULL neighbor
+
Missing route
```

Find the actual problem.

---

# 🧪 67. Lab 8 — Wrong Cost Challenge

Create two paths.

Make the less desirable path cheaper.

Observe the route.

Then correct the cost.

---

# 🧪 68. Lab 9 — LSDB Detective

Remove a required advertisement.

Use:

```cisco
show ip ospf database
```

Determine:

```text
What LSA is missing?
Who should originate it?
Why is it missing?
```

---

# 🧪 69. Lab 10 — Multi-Area Failure

Build:

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Break an area assignment.

Find:

```text
Neighbor symptom
LSA symptom
Route symptom
```

---

# 🧪 70. Lab 11 — Route Flapping

Create a redundant network.

Repeatedly interrupt a transit link in the lab.

Observe:

```text
Neighbor changes
LSA changes
Route changes
```

---

# 🧪 71. Lab 12 — Full OSPF NOC Incident

Scenario:

```text
🚨 Users in Branch A cannot reach the Data Center.
```

You receive no configuration hints.

Your workflow:

```text
1. Confirm symptom
2. Identify affected path
3. Check interface
4. Check neighbor
5. Check LSDB
6. Check route
7. Test forwarding
8. Fix
9. Verify
10. Document
```

---

# 🧪 72. Lab 13 — Multiple Simultaneous Faults

Introduce three faults:

```text
❌ Wrong area
❌ Passive interface
❌ Wrong cost
```

You must find all three.

🔥 No rebuilding the lab.

---

# 🧪 73. Lab 14 — Final OSPF War Room 👑

Topology:

```text
                      AREA 0
             R1 ===== R2 ===== R3
              \       |       /
               \      |      /
              Area 10       Area 20
                 |             |
               LAN-A         LAN-B
```

Faults are hidden.

Possible faults:

```text
🔴 Interface shutdown
🔴 Wrong IP
🔴 Wrong area
🔴 Passive interface
🔴 Timer mismatch
🔴 MTU mismatch
🔴 Wrong network type
🔴 Wrong cost
🔴 Missing advertisement
```

Your mission:

> Find the faults using the minimum number of commands possible.

---

# 🧠 74. Command Efficiency Challenge

Beginner approach:

```text
show running-config
show running-config
show running-config
...
```

Engineer approach:

```text
Symptom
 ↓
Best diagnostic command
 ↓
Evidence
 ↓
Next command
```

Example:

```text
Neighbor missing
     ↓
show ip ospf neighbor
     ↓
No neighbor
     ↓
show ip ospf interface
     ↓
Passive interface found
```

🔥 Fewer commands. Better reasoning.

---

# 🏢 75. NOC Scenario — Branch Offline

Ticket:

```text
INC-1024

“Branch users cannot access HQ servers.”
```

You check:

```cisco
show ip interface brief
```

WAN interface is:

```text
up/up
```

Next:

```cisco
show ip ospf neighbor
```

No neighbor.

Now investigate:

```text
Area
Timers
Network type
Passive interface
Authentication
IP addressing
```

Do not jump to route troubleshooting yet.

---

# 🏥 76. NOC Scenario — Hospital Core

Ticket:

```text
“Clinical application traffic is taking the backup path.”
```

Neighbor is FULL.

Route exists.

Now the problem is no longer basic adjacency.

Investigate:

```text
📏 Cost
🎚️ Reference bandwidth
🧩 Topology
🏠 Route type
🛣️ Next hops
```

Your final report should explain **why** the backup path won.

---

# 🏦 77. NOC Scenario — Bank Route Missing

Ticket:

```text
“Branch 4 cannot reach Data Center subnet.”
```

Neighbor status:

```text
FULL
```

Your investigation:

```cisco
show ip ospf database
show ip route ospf
show ip protocols
```

Find whether the destination is actually being advertised.

---

# 🧠 78. Troubleshooting Matrix

| Symptom | Likely investigation |
|---|---|
| No neighbor | IP, area, timers, passive, network type |
| INIT | Bidirectional Hello communication |
| 2-WAY | Check DR/BDR context before calling it a failure |
| EXSTART | MTU/network type/parameter compatibility |
| EXCHANGE | Database exchange/MTU/network type |
| LOADING | LSA requests/database synchronization |
| FULL + no route | Advertisement/LSDB/route selection |
| Wrong path | Cost/topology/route type |
| `O IA` missing | ABR/Type 3/Area 0 |
| Flapping | Physical stability/timers/topology changes |
| Unexpected DR | Priority/Router ID/election behavior |

---

# 🧪 79. Troubleshooting Documentation Template

Use this in your lab notes:

```text
Incident:
Date:
Topology:
Affected Router:
Affected Interface:
User Symptom:

Initial Evidence:

Neighbor State:

Relevant OSPF Configuration:

LSDB Evidence:

Routing Table Evidence:

Root Cause:

Fix:

Verification:

Rollback Plan:

Lessons Learned:
```

This turns random lab practice into a professional portfolio artifact.

---

# 🧠 80. Root Cause vs Symptom

Example:

```text
Symptom:
❌ Route missing

Root cause:
❌ Network statement failed to match interface
```

Another:

```text
Symptom:
❌ Neighbor stuck in ExStart

Root cause:
❌ MTU mismatch
```

Another:

```text
Symptom:
❌ Traffic uses backup link

Root cause:
❌ OSPF cost design makes backup path preferable
```

🔥 Always report the root cause, not just the symptom.

---

# 🛡️ 81. Safe Use of Debug Commands

Cisco provides powerful debugging tools.

Examples include platform/version-dependent commands such as:

```cisco
debug ip ospf adj
debug ip ospf events
```

⚠️ Debugging can consume significant CPU/resources and generate large amounts of output.

Professional rule:

```text
🧪 Lab → experiment freely
🏢 Production → use carefully, during controlled troubleshooting
```

If debugging is enabled, know how to disable it:

```cisco
undebug all
```

or:

```cisco
no debug all
```

Platform/version support can vary.

---

# 🧠 82. Show Commands Before Debug

Prefer non-disruptive evidence first:

```cisco
show ip ospf neighbor
show ip ospf interface
show ip ospf database
show ip route
show interfaces
show logging
```

Then use debugging when necessary and appropriate.

---

# 🎮 83. OSPF Detective — Case #1

You see:

```text
Neighbor: none
Interface: up/up
```

Question:

> What are your next three checks?

A strong answer:

```text
1. show ip ospf interface
2. Verify area/timers/network type/passive status
3. Verify IP addressing and OSPF participation
```

---

# 🎮 84. OSPF Detective — Case #2

You see:

```text
Neighbor: EXSTART
```

What should immediately enter your mind?

```text
📦 MTU
🧩 Network type
⚙️ Parameter compatibility
```

---

# 🎮 85. OSPF Detective — Case #3

You see:

```text
Neighbor: FULL
Route: missing
```

Do not restart OSPF.

Investigate:

```text
📢 Advertisement
🗺️ LSDB
🛣️ Route selection
```

---

# 🎮 86. OSPF Detective — Case #4

You see:

```text
Route = O
Expected = O IA
```

Question:

> What should you investigate?

```text
🏢 Area membership
🌉 ABR
📢 Type 3
🗺️ LSDB
```

---

# 🎮 87. OSPF Detective — Case #5

Two paths:

```text
Path A = 20
Path B = 50
```

But traffic uses Path B.

Do not immediately declare OSPF broken.

Investigate:

```text
🏠 Route type
🎯 Prefix specificity
🛣️ Other routing sources
📏 Actual OSPF costs
🚀 Forwarding behavior
```

---

# 🎤 88. Interview — Beginner

### Q1. What is the first thing you check when an OSPF neighbor is missing?

Start with interface status, IP addressing, and OSPF interface participation.

### Q2. Which command shows OSPF neighbors?

```cisco
show ip ospf neighbor
```

### Q3. What does FULL mean?

The OSPF adjacency has completed the database synchronization process.

### Q4. What is a passive interface?

An OSPF interface that does not form normal neighbor relationships while its connected network can still be advertised.

### Q5. What is a common ExStart troubleshooting item?

MTU mismatch is a classic cause to investigate.

---

# 🎤 89. Interview — Intermediate

### Q6. What can cause OSPF adjacency failure?

Examples include incorrect IP addressing, area mismatch, timer mismatch, network-type mismatch, passive interface, authentication mismatch, MTU-related problems, and underlying connectivity failures.

### Q7. What command shows OSPF interface parameters?

```cisco
show ip ospf interface
```

### Q8. How do you investigate a missing route when the neighbor is FULL?

Inspect OSPF advertisements/LSDB and then route-selection conditions.

### Q9. What does `O IA` mean?

An inter-area OSPF route.

### Q10. Why can a 2-Way neighbor state be normal?

On broadcast networks, DR/BDR behavior means not every router forms a FULL adjacency with every other router.

---

# 🎤 90. Interview — Advanced

### Q11. Why can MTU mismatch affect OSPF?

OSPF database exchange depends on compatible interface characteristics, and MTU mismatch can prevent successful adjacency progression.

### Q12. How do you troubleshoot an unexpected DR?

Check OSPF interface priority and Router ID, then account for election behavior and whether the adjacency/election was already established.

### Q13. Why can a FULL adjacency exist while a route is missing?

Adjacency synchronization does not guarantee that every desired network is correctly advertised or installed in the RIB.

### Q14. What is the difference between troubleshooting LSDB and routing table?

LSDB troubleshooting asks whether the topology information is present and correct; routing-table troubleshooting asks why a route was or was not selected/installed.

### Q15. Why should debug commands be used carefully?

They can generate significant output and resource usage, especially on production devices.

---

# 🎤 91. Interview — Troubleshooting Round

### Q16. Neighbor stuck in INIT. What do you investigate?

Bidirectional Hello communication and the parameters affecting it.

### Q17. Neighbor stuck in ExStart. What do you check?

MTU, network type, and OSPF compatibility parameters.

### Q18. Neighbor FULL but no `O IA` route. What do you check?

ABR, Area 0 connectivity, Type 3 LSAs, destination advertisement, and route selection.

### Q19. OSPF route uses an unexpected path. What do you check?

Actual interface costs, reference bandwidth, topology, route type, prefix specificity, and competing routes.

### Q20. What is your general troubleshooting methodology?

```text
Symptom → Evidence → Hypothesis → Test → Root Cause → Fix → Verify → Document
```

---

# ⚡ 92. Quick Revision

```text
🚨 NO NEIGHBOR
→ Interface
→ IP
→ OSPF
→ Area
→ Timers
→ Network type
→ Passive
→ Authentication

🟠 EXSTART
→ MTU
→ Network type
→ Compatibility

🟡 2-WAY
→ Check DR/BDR context

🟢 FULL
→ Adjacency healthy
→ But still verify routes

❌ FULL + NO ROUTE
→ Advertisement
→ LSA
→ LSDB
→ Route selection

❌ WRONG PATH
→ Cost
→ Reference bandwidth
→ Route type
→ Prefix specificity

🌉 NO O IA
→ ABR
→ Area 0
→ Type 3
→ Advertisement
```

---

# 🧾 93. OSPF Troubleshooting Command Cheat Sheet

| Purpose | Command |
|---|---|
| Interface status | `show ip interface brief` |
| Interface details | `show interfaces` |
| OSPF process | `show ip ospf` |
| OSPF neighbors | `show ip ospf neighbor` |
| OSPF interface parameters | `show ip ospf interface` |
| LSDB | `show ip ospf database` |
| Router LSAs | `show ip ospf database router` |
| Network LSAs | `show ip ospf database network` |
| Summary LSAs | `show ip ospf database summary` |
| External LSAs | `show ip ospf database external` |
| OSPF routes | `show ip route ospf` |
| Specific route | `show ip route <destination>` |
| Protocol configuration | `show ip protocols` |
| Running configuration | `show running-config` |
| Logs | `show logging` |
| Debug adjacency* | `debug ip ospf adj` |
| Stop debugging | `undebug all` |

`*` Availability/output varies by IOS/IOS XE/platform.

---

# 🧭 94. The 10-Command Troubleshooting Arsenal

If you are a beginner, master these first:

```cisco
show ip interface brief
show interfaces
show ip ospf
show ip ospf neighbor
show ip ospf interface
show ip ospf database
show ip route ospf
show ip route <destination>
show ip protocols
show logging
```

🔥 You do not need 100 commands to become good at troubleshooting.

You need to know:

> **Which command answers which question.**

---

# 🧪 95. Final Practical Exam

Build:

```text
                       AREA 0
             R1 ===== R2 ===== R3
              \       |       /
               \      |      /
              Area 10       Area 20
                 |             |
               LAN-A         LAN-B
```

The instructor introduces five hidden failures.

Possible failures:

```text
1️⃣ Wrong area
2️⃣ Passive transit interface
3️⃣ MTU mismatch
4️⃣ Wrong OSPF cost
5️⃣ Missing network advertisement
```

### Rules

```text
❌ No topology rebuild
❌ No random reload
❌ No random commands
❌ No copying configuration

✅ Observe
✅ Hypothesize
✅ Test
✅ Fix
✅ Verify
```

### Deliverable

```text
Incident report
Root causes
Commands used
Evidence
Fixes
Verification
Lessons learned
```

---

# 👑 96. Final Boss — OSPF War Room

You are now the senior NOC engineer.

### Ticket

> **“Multiple departments report intermittent connectivity. Routing appears unstable. Some OSPF neighbors are not FULL.”**

Topology:

```text
                 CORE-1 ===== CORE-2
                /   |  \       /   \
               /    |   \     /     \
          Area 10   |    Area 0    Area 20
             |      |             |
           LAN-A   LAN-B        LAN-C
```

Hidden faults:

```text
🔴 One wrong area
🔴 One timer mismatch
🔴 One passive interface
🔴 One MTU mismatch
🔴 One incorrect cost
🔴 One missing network advertisement
```

Your mission:

```text
🚨 Identify symptoms
 ↓
🔎 Gather evidence
 ↓
🧠 Map each symptom to a layer
 ↓
🧪 Test hypotheses
 ↓
🎯 Identify root causes
 ↓
🔧 Fix one issue at a time
 ↓
🤝 Verify neighbors
 ↓
📢 Verify LSDB
 ↓
🛣️ Verify routes
 ↓
🚀 Verify end-to-end connectivity
 ↓
📋 Write incident report
```

🏆 **Pass condition:** You can explain every fault without saying “I just changed commands until it worked.”

---

# 🧠 97. The Professional Engineer Mindset

A junior engineer says:

> “OSPF isn't working.”

A stronger engineer says:

> “R1's Gi0/1 is up/up, but no neighbor is present. OSPF is enabled, the interface is passive, and the expected transit link therefore sends no Hello packets.”

The second statement is better because it contains:

```text
📍 Location
🔎 Evidence
🧠 Cause
🎯 Explanation
```

That is how you should communicate incidents in a NOC/SOC/enterprise environment.

---

# 📋 98. Incident Report Example

```text
Incident: Branch-A OSPF adjacency failure

Symptom:
Branch-A could not reach HQ server subnet.

Evidence:
WAN interface was up/up.
No OSPF neighbor was present.

Investigation:
show ip ospf interface revealed the WAN interface was passive.

Root Cause:
Router-facing interface was incorrectly configured as passive.

Fix:
Removed passive status from the WAN interface.

Verification:
Neighbor reached FULL.
Expected OSPF route appeared.
End-to-end ping succeeded.

Lesson:
Use passive interfaces only on intended non-neighbor-facing interfaces.
```

This is portfolio-quality troubleshooting documentation. 📋🔥

---

# 🏁 99. Completion Checklist

### Fundamentals

- [ ] I know the OSPF troubleshooting layers
- [ ] I can identify the actual symptom
- [ ] I can build a troubleshooting hypothesis

### Neighbor Troubleshooting

- [ ] DOWN
- [ ] INIT
- [ ] 2-WAY
- [ ] EXSTART
- [ ] EXCHANGE
- [ ] LOADING
- [ ] FULL

### Configuration Problems

- [ ] IP mismatch
- [ ] Area mismatch
- [ ] Timer mismatch
- [ ] Passive interface
- [ ] Network type
- [ ] MTU
- [ ] Router ID
- [ ] Authentication

### Route Problems

- [ ] Missing advertisement
- [ ] Missing LSA
- [ ] LSDB issue
- [ ] Missing route
- [ ] Wrong route
- [ ] Wrong cost
- [ ] Multi-area route issue
- [ ] External route issue

### Practical Labs

- [ ] Lab 1 — From Scratch
- [ ] Lab 2 — No Neighbor
- [ ] Lab 3 — Timer Mismatch
- [ ] Lab 4 — Passive Interface
- [ ] Lab 5 — MTU
- [ ] Lab 6 — DR/BDR
- [ ] Lab 7 — Missing Route
- [ ] Lab 8 — Wrong Cost
- [ ] Lab 9 — LSDB Detective
- [ ] Lab 10 — Multi-Area Failure
- [ ] Lab 11 — Route Flapping
- [ ] Lab 12 — NOC Incident
- [ ] Lab 13 — Multiple Faults
- [ ] Lab 14 — Final War Room

### Professional Skills

- [ ] I troubleshoot layer by layer
- [ ] I use evidence before making changes
- [ ] I can explain root cause
- [ ] I document incidents
- [ ] I can verify a fix
- [ ] I know when debug commands are risky

---

# 🔗 100. Connect the Dots

### ⬅️ Previous — Module 27

```text
⚙️ Configure OSPF
 ↓
🪪 Router ID
 ↓
📢 Network statements
 ↓
🚫 Passive interfaces
 ↓
📏 Cost
 ↓
🌉 Areas
```

### 👉 This Module

```text
🚨 Symptom
 ↓
🔎 Evidence
 ↓
🤝 Neighbor
 ↓
📢 LSA
 ↓
🗺️ LSDB
 ↓
🌳 SPF
 ↓
📏 Cost
 ↓
🛣️ Route
 ↓
🚀 Forwarding
```

### ➡️ Next — Module 29

# 🌐 29 — OSPF Advanced Concepts & Design

We will build on this troubleshooting foundation with deeper design concepts and advanced operational thinking.

---

# 🌟 Final Takeaway

When someone says:

> **“OSPF is broken.”**

Your brain should automatically answer:

```text
🤔 What is the exact symptom?
       ↓
🔌 Is the interface healthy?
       ↓
🌐 Is IP connectivity correct?
       ↓
🤝 Is there an OSPF neighbor?
       ↓
📡 What neighbor state?
       ↓
📢 Are the expected LSAs present?
       ↓
🗺️ Is the LSDB correct?
       ↓
🌳 What should SPF calculate?
       ↓
📏 Are costs correct?
       ↓
🛣️ Is the route installed?
       ↓
🚀 Does forwarding actually work?
```

🔥 **Do not troubleshoot by luck. Troubleshoot by logic.**

The goal is not to become the person who knows the most commands.

The goal is to become the person who can look at a broken network and calmly answer:

> **“I know where the failure is, I know why it happened, and I know how to prove that I fixed it.”** 🧠🔎🌐🏆
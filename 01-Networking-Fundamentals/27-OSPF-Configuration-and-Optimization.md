# ⚙️ 27 — OSPF Configuration & Optimization

> **Enough reading the map. Today we build the OSPF network.** 🧑‍💻🌐🔥

You already understand:

```text
🤝 Neighbors
📢 LSAs
🗺️ LSDB
🌳 SPF
📏 Cost
🏆 Route Selection
⭐ Areas
```

Now we move into the engineer's chair:

```text
🧑‍💻 CONFIGURE
      ↓
🔎 VERIFY
      ↓
🧪 TEST
      ↓
🐛 TROUBLESHOOT
      ↓
⚙️ OPTIMIZE
      ↓
📋 DOCUMENT
```

This module is deliberately practical. You should spend significant time in Packet Tracer, IOS CLI, and troubleshooting scenarios rather than simply reading.

---

# 🗺️ OSPF Journey

```text
23 — OSPF Fundamentals
        ↓
24 — Neighbor Adjacency & Network Types
        ↓
25 — LSAs & Link-State Database
        ↓
26 — SPF, Route Selection & Areas
        ↓
👉 27 — OSPF Configuration & Optimization
        ↓
28 — OSPF Troubleshooting
```

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- ✅ Configure single-area OSPF
- ✅ Understand OSPF process IDs
- ✅ Configure OSPF using network statements
- ✅ Understand wildcard masks
- ✅ Configure OSPF directly under interfaces where supported
- ✅ Configure router IDs
- ✅ Configure passive interfaces
- ✅ Configure OSPF cost
- ✅ Configure reference bandwidth
- ✅ Configure multi-area OSPF
- ✅ Verify OSPF configuration
- ✅ Verify neighbors and routes
- ✅ Inspect LSAs after configuration
- ✅ Understand common OSPF configuration mistakes
- ✅ Build redundant OSPF networks
- ✅ Optimize an OSPF design safely
- ✅ Troubleshoot using evidence instead of guessing

---

# 🧠 1. Before Typing Commands — Understand the Job

When configuring OSPF, you are telling the router:

```text
🗣️ “Participate in OSPF.”
📡 “Form neighbors on these interfaces.”
📢 “Advertise these networks.”
🪪 “Use this Router ID.”
🚫 “Do not form neighbors on user-facing interfaces.”
📏 “Prefer this path.”
```

A professional configuration starts with design, not typing.

---

# 🧰 2. The Basic OSPF Configuration Model

Classic Cisco IOS configuration:

```cisco
router ospf 1
 router-id 1.1.1.1
 network 10.0.0.0 0.0.0.255 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

Break it down:

```text
router ospf 1
     ↓
Create/enter OSPF process 1

router-id 1.1.1.1
     ↓
Set the OSPF Router ID

network ... area 0
     ↓
Select matching interfaces and place them into Area 0
```

---

# 🔢 3. What Is the OSPF Process ID?

Example:

```cisco
router ospf 1
```

The `1` is the local OSPF process ID.

On Cisco IOS, the process ID is locally significant.

That means:

```text
R1: router ospf 1
R2: router ospf 99
```

can still form an OSPF adjacency, assuming the actual OSPF parameters are compatible.

🔥 Important:

```text
Process ID ≠ Area ID
```

Do not confuse them.

---

# ⭐ 4. Router ID

Every OSPF router needs a Router ID.

Think of it as:

```text
🪪 OSPF identity card
```

Example:

```cisco
router ospf 1
 router-id 1.1.1.1
```

Verify:

```cisco
show ip ospf
```

You may see:

```text
Routing Process "ospf 1" with ID 1.1.1.1
```

---

# 🧠 5. Router ID Selection — Practical Model

When manually configured, the router ID takes priority.

If not manually configured, OSPF can select from eligible interface addresses according to Cisco IOS OSPF rules, with loopback interfaces preferred over physical interfaces when appropriate.

For a professional lab or production design:

```text
🎯 Explicit Router ID
        ↓
🧠 Predictable OSPF identity
        ↓
🔎 Easier troubleshooting
        ↓
📋 Easier documentation
```

Best practice:

> Use a stable, documented Router ID rather than leaving identity selection to chance.

---

# 🔁 6. Changing Router ID

If OSPF is already running and you change the Router ID, the new ID may not take effect immediately.

A common lab approach is:

```cisco
clear ip ospf process
```

⚠️ This restarts the OSPF process and can temporarily disrupt OSPF adjacencies and routes.

In production, treat this as a controlled change.

---

# 🎯 7. Network Statements

Example:

```cisco
router ospf 1
 network 10.10.10.0 0.0.0.255 area 0
```

The network statement identifies interfaces whose IP addresses match the network/wildcard expression.

It does **not** simply mean:

> “Create this network.”

Instead, think:

> **“Find interfaces matching this address pattern and enable OSPF participation on them in this area.”**

🔥 This is a very important beginner distinction.

---

# 🧩 8. Wildcard Masks

A wildcard mask is not a subnet mask.

Example:

```text
Subnet mask:
255.255.255.0

Wildcard:
0.0.0.255
```

Mental shortcut for common masks:

| Subnet mask | Wildcard |
|---|---|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.0.0 | 0.0.255.255 |
| 255.0.0.0 | 0.255.255.255 |
| 255.255.255.252 | 0.0.0.3 |

Conceptually:

```text
Wildcard 0 = must match
Wildcard 1 = can vary
```

---

# 🧠 9. Wildcard Example

Suppose the interface is:

```text
192.168.10.1/24
```

Use:

```cisco
network 192.168.10.0 0.0.0.255 area 0
```

The interface matches.

Another interface:

```text
192.168.20.1/24
```

does not match that statement.

---

# 🎯 10. Exact Interface Matching

You can use a host wildcard to match one IP address:

```cisco
network 10.0.0.1 0.0.0.0 area 0
```

This matches the interface with exactly:

```text
10.0.0.1
```

This can make configuration very explicit.

---

# 🧪 11. Wildcard Practice

Determine the wildcard mask:

```text
10.10.10.0/24 → ?
172.16.0.0/16 → ?
192.168.1.0/24 → ?
10.0.0.0/30 → ?
```

Answers:

```text
10.10.10.0/24 → 0.0.0.255
172.16.0.0/16 → 0.0.255.255
192.168.1.0/24 → 0.0.0.255
10.0.0.0/30 → 0.0.0.3
```

🔥 Do these without a calculator until the pattern becomes automatic.

---

# ⚙️ 12. OSPF Configuration — Basic Two-Router Lab

Topology:

```text
LAN-A                         LAN-B
192.168.10.0/24              192.168.20.0/24
    │                              │
   R1 ===== 10.0.0.0/30 ===== R2
```

Addressing:

| Device | Interface | IP |
|---|---|---|
| R1 | G0/0 | 192.168.10.1/24 |
| R1 | G0/1 | 10.0.0.1/30 |
| R2 | G0/0 | 10.0.0.2/30 |
| R2 | G0/1 | 192.168.20.1/24 |

### R1

```cisco
router ospf 1
 router-id 1.1.1.1
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

### R2

```cisco
router ospf 1
 router-id 2.2.2.2
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.20.0 0.0.0.255 area 0
```

Verify:

```cisco
show ip ospf neighbor
show ip route ospf
show ip ospf database
```

---

# 🟢 13. What Should You See?

On R1:

```text
R2 = FULL
```

And a route similar to:

```text
O 192.168.20.0/24 via 10.0.0.2
```

On R2:

```text
O 192.168.10.0/24 via 10.0.0.1
```

If you do not see this:

```text
STOP 🛑
 ↓
Do not continue configuring random commands.
 ↓
Troubleshoot the foundation.
```

---

# 🔎 14. Verification Command #1 — Neighbors

```cisco
show ip ospf neighbor
```

Look for:

```text
Neighbor ID
State
Dead Time
Address
Interface
```

Most important beginner check:

```text
FULL
```

But remember from previous modules:

> FULL adjacency does not automatically mean every expected route exists.

---

# 🔎 15. Verification Command #2 — OSPF Interfaces

```cisco
show ip ospf interface
```

Useful information includes:

```text
Area
Process
Cost
Network type
Hello/dead timers
State
Neighbor count
```

This is one of your most powerful OSPF troubleshooting commands.

---

# 🔎 16. Verification Command #3 — OSPF Process

```cisco
show ip ospf
```

Look for:

```text
Process ID
Router ID
Areas
SPF statistics
LSA information
Reference bandwidth
```

---

# 🔎 17. Verification Command #4 — Routing Table

```cisco
show ip route ospf
```

Look for:

```text
O
O IA
O E1
O E2
O N1
O N2
```

The exact route types visible depend on the topology.

---

# 🔎 18. Verification Command #5 — LSDB

```cisco
show ip ospf database
```

Use this when the route table tells you **what** happened but you need to understand **why**.

---

# 🧠 19. The Verification Ladder

Use this order:

```text
1️⃣ Interface up?
       ↓
2️⃣ Correct IP?
       ↓
3️⃣ OSPF enabled?
       ↓
4️⃣ Neighbor FULL?
       ↓
5️⃣ LSA present?
       ↓
6️⃣ Route installed?
       ↓
7️⃣ Ping/traceroute works?
```

This simple ladder will save you enormous troubleshooting time.

---

# 🚫 20. Passive Interfaces

Imagine this topology:

```text
             R1
          /  |  \
         /   |   \
       R2   R3   Users
```

You may want OSPF adjacency on router-facing links.

But on the user LAN:

```text
Users ─── R1
```

there is no reason for R1 to form OSPF neighbor relationships with ordinary user devices.

Use:

```cisco
router ospf 1
 passive-interface GigabitEthernet0/0
```

The interface's network can still be advertised while OSPF Hello packets are suppressed on that interface.

---

# 🛡️ 21. Passive Interface Best Practice

A common professional pattern is:

```cisco
router ospf 1
 passive-interface default
 no passive-interface GigabitEthernet0/1
 no passive-interface GigabitEthernet0/2
```

Meaning:

```text
🚫 All interfaces passive by default
        ↓
✅ Only router-facing links form OSPF neighbors
```

This reduces accidental adjacency formation and improves operational/security hygiene.

---

# 🧪 22. Passive Interface Lab

Topology:

```text
Users ─ R1 ───── R2
```

Goal:

```text
Users LAN advertised
R1-R2 adjacency formed
No OSPF neighbor on Users LAN
```

Configure:

```cisco
router ospf 1
 passive-interface GigabitEthernet0/0
```

Verify:

```cisco
show ip ospf interface GigabitEthernet0/0
show ip ospf neighbor
```

---

# 🪪 23. Loopback Interface for Router ID

A loopback provides a stable logical interface.

Example:

```cisco
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
```

Then:

```cisco
router ospf 1
 router-id 1.1.1.1
```

Why is this useful?

```text
Physical link fails
      ↓
Loopback may remain up
      ↓
Stable identity
```

⚠️ A loopback address does not automatically become the Router ID merely because it exists if a manual Router ID is configured.

---

# 📏 24. Changing OSPF Cost

Example:

```cisco
interface GigabitEthernet0/1
 ip ospf cost 50
```

Verify:

```cisco
show ip ospf interface GigabitEthernet0/1
```

Use this when you intentionally want to influence path selection.

---

# ⚙️ 25. Cost Optimization Example

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

Suppose:

```text
R1 → R2 → R4 = 20
R1 → R3 → R4 = 60
```

R1 prefers:

```text
R1 → R2 → R4
```

Now change the R1-R2 interface cost:

```cisco
interface GigabitEthernet0/1
 ip ospf cost 100
```

The path may change.

🔥 Always predict before changing it.

---

# 🧠 26. Reference Bandwidth

OSPF cost depends on the reference bandwidth and interface bandwidth when cost is automatically calculated.

Configure consistently:

```cisco
router ospf 1
 auto-cost reference-bandwidth 100000
```

Then verify:

```cisco
show ip ospf
```

⚠️ Use the same reference bandwidth throughout a common OSPF domain/design.

---

# 🚨 27. Why High-Speed Interfaces Need Attention

A default reference bandwidth can cause several high-speed interfaces to receive identical calculated costs.

For example, if the reference is too low relative to modern links:

```text
1G
10G
40G
100G
```

may not receive useful differentiation from the automatic formula.

Professional engineers deliberately define a suitable reference bandwidth.

---

# 🧪 28. Lab — Cost Engineering

Build:

```text
          R2
         /  \
        /    \
      R1      R4
        \    /
         \  /
          R3
```

### Phase A

Make upper path cheaper.

### Phase B

Make lower path cheaper.

### Phase C

Make both equal.

For each phase record:

```text
R1 route:
Next hop:
Total cost:
Why?
```

---

# 🌉 29. Multi-Area OSPF Configuration

Topology:

```text
LAN-A
  │
 R1
  │ Area 10
 ABR
  │
Area 0
  │
 R3
  │
LAN-B
```

Example addressing:

```text
R1 LAN: 10.10.10.0/24
R1-ABR: 10.0.10.0/30
ABR-R3: 10.0.0.0/30
R3 LAN: 10.20.20.0/24
```

---

# ⚙️ 30. Multi-Area Router Configuration

### R1 — Area 10

```cisco
router ospf 1
 router-id 1.1.1.1
 network 10.10.10.0 0.0.0.255 area 10
 network 10.0.10.0 0.0.0.3 area 10
```

### ABR

```cisco
router ospf 1
 router-id 2.2.2.2
 network 10.0.10.0 0.0.0.3 area 10
 network 10.0.0.0 0.0.0.3 area 0
```

### R3 — Area 0

```cisco
router ospf 1
 router-id 3.3.3.3
 network 10.0.0.0 0.0.0.3 area 0
 network 10.20.20.0 0.0.0.255 area 0
```

🎯 Verify the inter-area route.

---

# 🧪 31. Multi-Area Lab — Find `O IA`

On R3:

```cisco
show ip route ospf
```

You should investigate the route toward:

```text
10.10.10.0/24
```

Expected category:

```text
O IA
```

Then inspect:

```cisco
show ip ospf database summary
```

🔥 Connect:

```text
Area 10
 ↓
ABR
 ↓
Type 3
 ↓
Area 0
 ↓
O IA
```

---

# 🗺️ 32. Topology 1 — Basic Single-Area

```text
LAN ─ R1 ─ R2 ─ LAN
```

Purpose:

```text
Basic configuration
```

---

# 🗺️ 33. Topology 2 — Triangle

```text
       R2
      /  \
     /    \
   R1 ─── R3
```

Purpose:

```text
Redundancy
Cost
SPF
```

---

# 🗺️ 34. Topology 3 — Dual Core

```text
        R2 ===== R3
       /           \
      R1             R4
       \           /
        R5 ===== R6
```

Purpose:

```text
Resilience
Multiple paths
```

---

# 🗺️ 35. Topology 4 — Hub and Spoke

```text
        R2
         |
R3 ───── R1 ───── R4
         |
        R5
```

Purpose:

```text
Branch architecture
```

---

# 🗺️ 36. Topology 5 — Area 0 + Area 10

```text
Area 10       Area 0
R1 ── ABR ───────── R3
```

Purpose:

```text
ABR
Inter-area
```

---

# 🗺️ 37. Topology 6 — Three Areas

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Purpose:

```text
Backbone
Type 3
O IA
```

---

# 🗺️ 38. Topology 7 — Dual ABR

```text
             Area 0
           /        \
        ABR-1      ABR-2
          |          |
       Area 10     Area 10
```

Purpose:

```text
Redundant area connectivity
```

---

# 🗺️ 39. Topology 8 — Campus

```text
                  CORE
                 Area 0
                /      \
             ABR-1    ABR-2
              /          \
        Building-A    Building-B
          Area 10       Area 20
```

---

# 🗺️ 40. Topology 9 — Branch Network

```text
Branch-A ─┐
Branch-B ─┼── HQ Area 0 ── DC
Branch-C ─┘
```

---

# 🗺️ 41. Topology 10 — Hospital

```text
             Core
           /      \
      Clinical    Admin
          |          |
       Servers    Users
           \      /
           Data Center
```

---

# 🗺️ 42. Topology 11 — Data Center Underlay

```text
          Spine-1
         /       \
      Leaf-1    Leaf-2
         \       /
          Spine-2
```

OSPF can serve as an underlay in appropriate designs.

---

# 🗺️ 43. Topology 12 — ISP Edge

```text
Enterprise
    │
   ASBR
    │
   OSPF
    │
   Core
    │
    🌐
```

Use this as an external-routing preview.

---

# 🧪 44. Practical Lab 1 — Configure Basic OSPF

Build:

```text
PC1 ─ SW1 ─ R1 ─ R2 ─ SW2 ─ PC2
```

Tasks:

- Configure IP addressing
- Configure OSPF Area 0
- Set Router IDs
- Verify adjacency
- Verify routes
- Ping PC1 → PC2

Commands:

```cisco
show ip ospf neighbor
show ip route ospf
show ip ospf
```

---

# 🧪 45. Practical Lab 2 — Network Statement Challenge

Configure OSPF using network statements only.

Requirement:

```text
Advertise LAN + transit network
```

Then verify:

```cisco
show ip protocols
show ip ospf interface
```

🎯 Explain which interfaces matched each network statement.

---

# 🧪 46. Practical Lab 3 — Wildcard Mask Challenge

Given:

```text
172.16.10.0/24
172.16.20.0/24
10.0.0.0/30
```

Write the OSPF statements without looking at the answer.

---

# 🧪 47. Practical Lab 4 — Router ID Lab

Configure:

```text
R1 → 1.1.1.1
R2 → 2.2.2.2
R3 → 3.3.3.3
```

Verify:

```cisco
show ip ospf
show ip ospf neighbor
```

Then change one Router ID in a controlled lab.

Observe the effect of restarting the OSPF process.

---

# 🧪 48. Practical Lab 5 — Passive Interface

Topology:

```text
Users ─ R1 ─ R2 ─ Users
```

Goal:

```text
User LAN advertised
No user-side OSPF adjacency
Router-to-router adjacency works
```

Use:

```cisco
passive-interface default
no passive-interface GigabitEthernet0/1
```

---

# 🧪 49. Practical Lab 6 — Path Manipulation

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

Tasks:

1. Verify initial path.
2. Change interface cost.
3. Verify new path.
4. Restore cost.
5. Document the result.

---

# 🧪 50. Practical Lab 7 — Reference Bandwidth

Build a network with interfaces of different speeds if your lab platform supports them.

Inspect:

```cisco
show ip ospf interface
```

Then configure a consistent reference bandwidth.

Document:

```text
Before:
After:
Why changed:
Operational impact:
```

---

# 🧪 51. Practical Lab 8 — Multi-Area OSPF

Build:

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Tasks:

- Configure all routers
- Verify all adjacencies
- Find `O IA` routes
- Inspect Type 3 LSAs
- Ping across areas

---

# 🧪 52. Practical Lab 9 — Three-Area Campus

Build:

```text
             AREA 0
           /        \
        ABR-1      ABR-2
         /            \
     Area 10        Area 20
```

Requirements:

```text
4+ routers
3 areas
4+ LANs
Redundant path
```

Document all Router IDs and areas.

---

# 🧪 53. Practical Lab 10 — OSPF Configuration From Scratch

Start with a blank Packet Tracer topology.

Build:

```text
PC-A ─ R1 ─ R2 ─ R3 ─ PC-B
       \           /
        ───────────
```

Do everything yourself:

```text
📐 Design
📋 Addressing
⚙️ Configuration
🔎 Verification
🧪 Testing
📋 Documentation
```

No copy-paste solution.

---

# 🧪 54. Practical Lab 11 — Configuration Audit 🔍

You receive a working OSPF network.

Your task is not to configure it.

Instead, audit it.

Check:

```text
🪪 Router IDs
📡 Neighbor links
🚫 Passive interfaces
📏 Costs
⭐ Areas
📢 Advertised networks
```

Produce an audit report.

---

# 🧪 55. Practical Lab 12 — Optimization Challenge ⚙️

Management says:

> “Voice traffic should prefer the primary core path.”

You cannot redesign the entire topology.

Use OSPF cost intentionally.

Deliver:

```text
Before path
After path
Changed cost
Reason
Verification
Rollback command/config
```

---

# 🧪 56. Practical Lab 13 — Failure + Recovery

Build:

```text
          R2
         /  \
        /    \
      R1      R4
        \    /
         \  /
          R3
```

1. Select primary path.
2. Shut primary link.
3. Verify alternate path.
4. Restore link.
5. Verify reconvergence.

Record approximate observations from:

```cisco
show ip ospf neighbor
show ip route
```

---

# 🧪 57. Practical Lab 14 — Final Configuration War Room 👑

Build an enterprise network:

```text
                         AREA 0
                 ┌──────── CORE ────────┐
                 │                      │
               ABR-1                  ABR-2
                /                        \
           Area 10                    Area 20
          /       \                  /       \
      Branch-A  Branch-B          Branch-C  Data Center
```

Requirements:

```text
⭐ Area 0
🟩 Area 10
🟨 Area 20
🏢 Multiple ABRs
🔁 Redundancy
📏 Cost engineering
🚫 Passive interfaces
🪪 Documented Router IDs
```

Then intentionally break:

```text
❌ Area ID
❌ Wildcard
❌ Router ID
❌ Passive interface
❌ Cost
```

Fix each one.

🔥 This is your configuration capstone.

---

# 🚨 58. Common Mistake — Wrong Wildcard

Incorrect:

```cisco
network 192.168.10.0 255.255.255.0 area 0
```

Classic Cisco OSPF network statements expect a wildcard mask in this form.

Correct:

```cisco
network 192.168.10.0 0.0.0.255 area 0
```

---

# 🚨 59. Common Mistake — Wrong Area

Example:

```text
R1 → Area 0
R2 → Area 10
```

If the same transit link is expected to form the adjacency, this is a problem.

Verify:

```cisco
show ip ospf interface
```

---

# 🚨 60. Common Mistake — OSPF Not Enabled on Interface

You configured:

```cisco
router ospf 1
network 10.10.10.0 0.0.0.255 area 0
```

But the interface actually has:

```text
10.20.20.1/24
```

No match.

Result:

```text
❌ No OSPF participation on that interface
```

---

# 🚨 61. Common Mistake — Wrong Process ID Assumption

Do not troubleshoot an adjacency simply because:

```text
R1 → OSPF 1
R2 → OSPF 2
```

The process ID is locally significant on Cisco IOS.

Check actual compatibility parameters instead.

---

# 🚨 62. Common Mistake — Forgetting Passive Interface Design

This can lead to unnecessary OSPF Hellos on user-facing interfaces.

Better:

```cisco
router ospf 1
 passive-interface default
 no passive-interface <router-facing-interface>
```

---

# 🚨 63. Common Mistake — Changing Cost Without a Design

Bad approach:

```text
“Ping is slow → change cost.”
```

Better:

```text
Measure
 ↓
Understand current path
 ↓
Identify desired path
 ↓
Calculate costs
 ↓
Change intentionally
 ↓
Verify
 ↓
Document
```

---

# 🐛 64. Troubleshooting — No Neighbor

Use:

```cisco
show ip ospf interface
show ip ospf neighbor
show ip interface brief
```

Check:

```text
🔌 Interface up?
🌐 Correct IP/subnet?
🧩 OSPF enabled?
🏢 Same area?
⏱️ Same timers?
🪪 Network type compatible?
🚫 Passive interface?
🛡️ Authentication if configured?
```

---

# 🐛 65. Troubleshooting — Neighbor Stuck in INIT

Think:

```text
R1 sends Hello
       ↓
R2 sees it
       ↓
But R1 is not seeing its own Router ID in R2's Hello
```

Investigate:

```text
🔌 Connectivity
📡 Multicast/Hello reception
⚙️ Interface/network type
🛡️ Filtering/security
```

---

# 🐛 66. Troubleshooting — Neighbor FULL but Route Missing

Check:

```cisco
show ip ospf database
show ip route ospf
show ip protocols
```

Ask:

```text
📢 Is the network advertised?
🗺️ Is the LSA present?
🏢 Is it in the expected area?
🏆 Is another route preferred?
```

---

# 🐛 67. Troubleshooting — Wrong Path

Use:

```cisco
show ip ospf interface
show ip route <destination>
show ip ospf database
```

Check:

```text
📏 Cost
⚙️ Reference bandwidth
🧩 Topology
🏠 Route type
🎯 Prefix specificity
```

---

# 🧠 68. The OSPF Configuration Lifecycle

```text
📐 DESIGN
   ↓
🪪 Router IDs
   ↓
🏢 Areas
   ↓
📡 Interface participation
   ↓
🚫 Passive interfaces
   ↓
🤝 Neighbors
   ↓
📢 LSAs
   ↓
🗺️ LSDB
   ↓
🌳 SPF
   ↓
🛣️ Routes
   ↓
🧪 Verification
   ↓
⚙️ Optimization
```

This should become your mental checklist whenever you build OSPF.

---

# 🏢 69. Real-World Scenario — Enterprise Campus

Company network:

```text
                     CORE
                   AREA 0
                 /        \
             ABR-1       ABR-2
              /             \
       Engineering        Finance
         Area 10            Area 20
```

Requirements:

```text
Engineering LANs
Finance LANs
Server LAN
Voice LAN
Management LAN
```

Engineering should prefer one core path.

Finance should have a redundant path.

Management interfaces should not form OSPF adjacencies.

Your design should use:

```text
🪪 Router IDs
⭐ Areas
🚫 Passive interfaces
📏 Costs
🔁 Redundancy
```

---

# 🏥 70. Real-World Scenario — Hospital Network

```text
              Core-1 ===== Core-2
              /   \       /   \
       Clinical   Admin   DC   Backup
```

Design requirements:

```text
🩺 Clinical systems → reliable primary path
🧑‍💼 Admin → normal path
🗄️ Data center → redundant path
👥 User VLANs → passive interfaces
```

Challenge:

> How would you configure OSPF so user-facing interfaces advertise networks without becoming neighbor links?

Expected concept:

```text
passive-interface
```

---

# 🏦 71. Real-World Scenario — Bank Branches

```text
Branch 1 ─┐
Branch 2 ─┼── HQ / Area 0 ── Data Center
Branch 3 ─┘
```

Requirements:

- Stable Router IDs
- Area 0 backbone
- Branch area design
- Redundant WAN links
- Documented costs
- Passive customer/user LANs
- Monitoring/verification

Build this in Packet Tracer.

---

# 🎮 72. Configuration Detective Game

You receive:

```cisco
router ospf 1
 network 10.10.10.0 255.255.255.0 area 0
```

Question:

> What is wrong?

🟥 The second address is a subnet mask rather than the expected wildcard-mask format for a Cisco OSPF `network` statement.

---

# 🎮 73. Configuration Detective — Round 2

```cisco
router ospf 10
 router-id 1.1.1.1
```

Neighbor router:

```cisco
router ospf 20
 router-id 2.2.2.2
```

Question:

> Is the different process ID automatically the problem?

🟢 No.

The process ID is locally significant on Cisco IOS.

---

# 🎮 74. Configuration Detective — Round 3

```cisco
router ospf 1
 passive-interface GigabitEthernet0/1
```

But G0/1 is the router-to-router link.

Question:

> Why might the neighbor disappear?

🟢 Because OSPF Hellos are suppressed on that passive interface, preventing normal adjacency formation there.

---

# 🎮 75. Configuration Detective — Round 4

Routing table:

```text
O 10.20.20.0/24 via 10.0.0.2
```

Manager says:

> “Make traffic use the other path.”

Do not immediately change configuration.

First investigate:

```text
Current cost
Alternate cost
Reference bandwidth
Route type
Next hop
```

Then make a controlled change.

---

# 🧪 76. Practice — Write the Configuration

Given:

```text
R1
LAN: 192.168.10.0/24
Transit: 10.0.0.0/30
Router ID: 1.1.1.1
Area: 0
```

Write:

```cisco
router ospf 1
 router-id 1.1.1.1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
```

Then explain every line in your own words.

---

# 🧪 77. Practice — Design the Wildcard

Calculate the wildcard for:

```text
172.16.10.0/24
172.16.20.0/24
10.10.0.0/16
10.0.0.0/30
```

Then write the network statements.

---

# 🧪 78. Practice — Predict Before Verify

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

Costs:

```text
R1-R2 = 10
R2-R4 = 10
R1-R3 = 20
R3-R4 = 20
```

Question:

> Which path should R1 prefer?

Calculate first.

---

# 🧪 79. Practice — Multi-Area Prediction

Topology:

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Question:

> If R1 in Area 10 learns a network located in Area 20, what broad OSPF route category should you expect?

Answer:

```text
O IA
```

Then identify the Type 3/ABR relationship.

---

# 🎤 80. Interview Questions — Beginner

### Q1. What command starts an OSPF process?

```cisco
router ospf <process-id>
```

### Q2. What is a Router ID?

A unique OSPF router identifier used by the OSPF process.

### Q3. What is the OSPF process ID on Cisco IOS?

A locally significant identifier for the OSPF process.

### Q4. What is a wildcard mask?

A matching mask used by classic Cisco OSPF network statements.

### Q5. Why use passive interfaces?

To advertise a connected network while suppressing OSPF neighbor formation on that interface.

---

# 🎤 81. Interview Questions — Intermediate

### Q6. How do you verify OSPF neighbors?

```cisco
show ip ospf neighbor
```

### Q7. How do you verify OSPF routes?

```cisco
show ip route ospf
```

### Q8. How do you inspect the LSDB?

```cisco
show ip ospf database
```

### Q9. How do you change interface OSPF cost?

```cisco
interface <interface>
 ip ospf cost <value>
```

### Q10. Why should reference bandwidth be consistent?

To avoid inconsistent automatic OSPF cost calculations across the OSPF design.

---

# 🎤 82. Interview Questions — Advanced

### Q11. Does the OSPF process ID need to match between neighbors on Cisco IOS?

No. It is locally significant.

### Q12. What happens when you make an interface passive?

OSPF does not send normal Hellos for neighbor formation on that interface, while the connected network can still be advertised.

### Q13. Why use a manually configured Router ID?

Predictability, stable identity, easier troubleshooting, and better documentation.

### Q14. What can changing OSPF cost accomplish?

It can influence path selection by changing the metric used to compare paths.

### Q15. What is an ABR?

A router participating in multiple OSPF areas and performing area-border functions.

---

# 🎤 83. Interview Questions — Troubleshooting

### Q16. OSPF neighbor is missing. What do you check first?

Interface state, addressing, OSPF participation, area, timers, network type, passive status, and other compatibility parameters.

### Q17. Route is missing but neighbor is FULL. What next?

Inspect the LSDB, expected LSA, advertisement, area, and route-selection eligibility.

### Q18. Route uses the wrong path. What do you inspect?

OSPF cost, reference bandwidth, topology, route type, prefix specificity, and next hops.

### Q19. Why is a wildcard mask important?

It determines which interface IP addresses match the OSPF network statement.

### Q20. What command shows OSPF interface cost?

```cisco
show ip ospf interface
```

---

# ⚡ 84. Quick Revision Card

```text
⚙️ router ospf <id>
= enter OSPF process

🪪 router-id X.X.X.X
= set Router ID

📢 network X.X.X.X W.W.W.W area N
= match interfaces and enable OSPF participation

🚫 passive-interface
= advertise network, suppress neighbor formation

📏 ip ospf cost <value>
= manually set interface cost

🎚️ auto-cost reference-bandwidth <Mbps>
= adjust automatic cost reference

🔎 show ip ospf
= process details

🤝 show ip ospf neighbor
= neighbors

📢 show ip ospf database
= LSDB

🛣️ show ip route ospf
= OSPF routes

🌉 O IA
= inter-area route
```

---

# 🧾 85. Master OSPF Configuration Template

```cisco
!
! Interface addressing/configuration first
!
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface GigabitEthernet0/1
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
!
interface GigabitEthernet0/1
 ip ospf cost 10
!
```

⚠️ Adapt the template to the actual topology. Never blindly paste production configuration.

---

# 🧭 86. Professional OSPF Build Checklist

```text
📐 1. Draw topology
📋 2. Build addressing table
🪪 3. Plan Router IDs
⭐ 4. Plan areas
⚙️ 5. Configure interfaces
📡 6. Enable OSPF
🚫 7. Configure passive interfaces
🤝 8. Verify neighbors
📢 9. Verify LSDB
🛣️ 10. Verify routes
🧪 11. Test end-to-end
📏 12. Validate costs
⚙️ 13. Optimize if required
📋 14. Document
```

---

# 🐛 87. Professional Troubleshooting Flow

```text
🚨 Problem
   ↓
🔌 Interface status
   ↓
🌐 IP addressing
   ↓
⚙️ OSPF enabled?
   ↓
🏢 Area correct?
   ↓
⏱️ Timers/network type?
   ↓
🚫 Passive?
   ↓
🤝 Neighbor?
   ↓
📢 LSA?
   ↓
🗺️ LSDB?
   ↓
📏 Cost?
   ↓
🌳 SPF?
   ↓
🛣️ Route?
   ↓
🚀 End-to-end forwarding
```

---

# 🏆 88. Final Boss — OSPF Network Engineer Challenge

You are given:

```text
                 AREA 0
             R1 ===== R2
            /  \     /  \
           /    \   /    \
       Area 10   \ /   Area 20
          R3 ----- R4
           |       |
          LAN     LAN
```

Your manager gives you these requirements:

```text
1. All routers must use stable Router IDs.
2. User LANs must not form OSPF adjacencies.
3. All required networks must be advertised.
4. Primary traffic should use R1-R2.
5. Backup traffic should use the alternate path.
6. Area 10 and Area 20 must communicate through Area 0.
7. All configuration must be documented.
```

### Your mission

```text
📐 Design addressing
🪪 Assign Router IDs
⭐ Assign areas
⚙️ Configure OSPF
🚫 Configure passive interfaces
📏 Engineer costs
🤝 Verify neighbors
📢 Verify LSAs
🗺️ Verify LSDB
🛣️ Verify routes
💥 Break primary path
🔁 Verify recovery
📋 Document everything
```

### Bonus challenge

Deliberately introduce:

```text
❌ Wrong wildcard
❌ Wrong area
❌ Passive router link
❌ Incorrect cost
```

Then troubleshoot each issue without rebuilding the topology.

👑 **If you can complete this from a blank topology, you are no longer just memorizing OSPF commands. You are configuring OSPF.**

---

# 🏁 89. Completion Checklist

### Configuration

- [ ] I can start an OSPF process
- [ ] I understand process ID
- [ ] I can configure Router ID
- [ ] I understand wildcard masks
- [ ] I can configure network statements
- [ ] I can configure passive interfaces
- [ ] I can configure OSPF cost
- [ ] I understand reference bandwidth
- [ ] I can configure multi-area OSPF

### Verification

- [ ] `show ip ospf`
- [ ] `show ip ospf neighbor`
- [ ] `show ip ospf interface`
- [ ] `show ip ospf database`
- [ ] `show ip route ospf`
- [ ] `show ip protocols`

### Practical

- [ ] Basic OSPF lab
- [ ] Wildcard lab
- [ ] Router ID lab
- [ ] Passive interface lab
- [ ] Cost lab
- [ ] Reference bandwidth lab
- [ ] Multi-area lab
- [ ] Campus lab
- [ ] Failure/recovery lab
- [ ] Configuration audit
- [ ] Optimization challenge
- [ ] Final configuration war room

### Troubleshooting

- [ ] I can troubleshoot missing neighbors
- [ ] I can troubleshoot missing routes
- [ ] I can troubleshoot wrong paths
- [ ] I can inspect the LSDB
- [ ] I can verify costs
- [ ] I can troubleshoot area mistakes

---

# 🔗 90. Connect the Dots

### ⬅️ Previous — Module 26

```text
🗺️ LSDB
 ↓
🌳 SPF
 ↓
📏 Cost
 ↓
🏆 Route Selection
 ↓
⭐ Areas
```

### 👉 This Module

```text
🧑‍💻 Configuration
 ↓
🪪 Router ID
 ↓
📢 Network statements
 ↓
🚫 Passive interfaces
 ↓
📏 Cost
 ↓
🌉 Multi-area
 ↓
🔎 Verification
 ↓
⚙️ Optimization
```

### ➡️ Next — Module 28

# 🐛 28 — OSPF Troubleshooting

The next module will turn you into the person everyone calls when someone says:

> **“OSPF is broken.”** 😈

We will systematically attack:

```text
❌ No adjacency
❌ INIT
❌ 2-WAY problems
❌ EXSTART/EXCHANGE
❌ MTU mismatch
❌ Timer mismatch
❌ Area mismatch
❌ Passive interface
❌ Network type mismatch
❌ Missing routes
❌ Wrong path
❌ Missing LSAs
❌ DR/BDR problems
❌ Multi-area failures
❌ Route-selection mysteries
```

---

# 🌟 Final Takeaway

OSPF configuration is not about memorizing:

```cisco
router ospf 1
```

It is about understanding the complete lifecycle:

```text
📐 DESIGN
   ↓
🪪 IDENTITY
   ↓
⭐ AREA
   ↓
📡 INTERFACE PARTICIPATION
   ↓
🚫 PASSIVE CONTROL
   ↓
🤝 ADJACENCY
   ↓
📢 LSA
   ↓
🗺️ LSDB
   ↓
🌳 SPF
   ↓
📏 COST
   ↓
🏆 PATH
   ↓
🛣️ ROUTE
   ↓
🚀 FORWARDING
   ↓
⚙️ OPTIMIZATION
```

> 🔥 **Configure with a plan. Verify with evidence. Troubleshoot layer by layer. Optimize only when you understand the current behavior.**

That is the difference between **typing Cisco commands** and **engineering a network.** 🌐🧠⚙️🏆
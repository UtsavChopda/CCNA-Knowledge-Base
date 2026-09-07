# 🧠 26 — OSPF SPF, Route Selection & Areas

> **The LSDB is the map. SPF is the brain that reads the map.** 🗺️🧠🏆

You have already learned how OSPF discovers neighbors, forms adjacencies, exchanges LSAs, and builds the LSDB.

Now we answer the question every network engineer eventually asks:

# ❓ “How does OSPF actually choose the path?”

The answer takes us through:

```text
📢 LSAs
   ↓
🗺️ LSDB
   ↓
🌳 SPF calculation
   ↓
📏 OSPF cost
   ↓
🏆 Best path
   ↓
🛣️ Routing table
   ↓
🚀 Packet forwarding
```

And then we scale that thinking using **OSPF areas**.

---

# 🗺️ OSPF Learning Journey

```text
23 — OSPF Fundamentals
        ↓
24 — Neighbor Adjacency & Network Types
        ↓
25 — LSAs & Link-State Database
        ↓
👉 26 — SPF, Route Selection & Areas
        ↓
27 — OSPF Configuration & Optimization
        ↓
28 — OSPF Troubleshooting
```

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain SPF in simple terms
- 🌳 Understand Dijkstra's shortest-path concept
- 📏 Explain OSPF cost
- 🏆 Understand how OSPF selects paths
- 🔢 Understand administrative distance vs metric
- 🧩 Understand equal-cost paths
- ⚖️ Explain ECMP conceptually
- 🗺️ Understand OSPF areas
- ⭐ Understand Area 0
- 🏢 Understand ABR and ASBR roles in multi-area OSPF
- 🌉 Understand intra-area vs inter-area routing
- 🧭 Understand route selection across areas
- 📢 Connect LSA types to route calculation
- 🔎 Verify SPF and route decisions on Cisco IOS
- 🐛 Troubleshoot unexpected OSPF paths
- 🧪 Build and test multi-area OSPF labs

---

# 🔥 1. The Big Question: How Does OSPF Choose a Path?

Imagine this network:

```text
             R2
            /  \
           /    \
        10      5
         /        \
       R1 ─── 20 ─── R3
```

R1 wants to reach R3.

There are two obvious paths:

```text
Path A:
R1 → R2 → R3
Cost = 10 + 5 = 15

Path B:
R1 → R3
Cost = 20
```

OSPF thinks:

```text
15 < 20
```

🏆 Therefore, Path A wins.

That is the simplified idea behind SPF.

---

# 🧠 2. What Is SPF?

**SPF = Shortest Path First.**

OSPF uses the **Shortest Path First algorithm**, commonly associated with **Dijkstra's algorithm**, to calculate paths from the topology information in the LSDB.

Think of the LSDB as:

```text
🗺️ A city map
```

And SPF as:

```text
🧠 GPS route calculation
```

The router asks:

> “From me, what is the best path to every known destination based on OSPF's topology and cost rules?”

---

# 🧩 3. LSDB → SPF → Routes

This relationship must become automatic in your mind:

```text
📢 LSAs
 ↓
🗺️ LSDB
 ↓
🌳 SPF calculation
 ↓
📏 Costs
 ↓
🏆 Preferred paths
 ↓
🛣️ RIB / routing table
```

If you understand this pipeline, OSPF becomes much easier.

---

# 🌳 4. SPF as a Tree

The router performing SPF becomes the root of its calculation.

Example:

```text
              R2
             /  \
            /    \
          R1      R3
                  |
                  R4
```

If R1 performs SPF, conceptually it builds a shortest-path tree rooted at R1:

```text
             R1 🌳
               |
              R2
               |
              R3
               |
              R4
```

The exact internal representation is more detailed, but the learning model is:

> **Start from yourself and calculate the lowest-cost paths outward.**

---

# 🏆 5. Why Does OSPF Need SPF?

Because knowing topology is not the same as knowing the best route.

```text
LSDB:
“What exists?”

SPF:
“What is the best path?”

Routing table:
“What route will I use?”
```

This is a very important distinction.

---

# 📏 6. OSPF Cost

OSPF uses **cost** as its primary path-selection metric.

Conceptually:

```text
OSPF cost
= value assigned to an interface/link
```

A path's total cost is based on the costs of the links used by that path.

Example:

```text
R1 ──10── R2 ──10── R3
```

Total path cost:

```text
10 + 10 = 20
```

Another path:

```text
R1 ──30── R3
```

Cost:

```text
30
```

Therefore:

```text
20 < 30
```

🏆 R1 prefers R1 → R2 → R3.

---

# ⚙️ 7. OSPF Cost Formula

A traditional Cisco OSPF cost calculation is based on:

```text
Reference Bandwidth
────────────────────
Interface Bandwidth
```

Historically, the reference bandwidth has commonly been:

```text
100 Mbps
```

So a simplified traditional example is:

```text
100 Mbps / 10 Mbps = cost 10
```

⚠️ Modern high-speed networks require careful reference-bandwidth planning because a default reference bandwidth can produce the same integer cost for many interfaces at 100 Mbps and above.

Cisco IOS allows the reference bandwidth to be adjusted under the OSPF process.

Example:

```cisco
router ospf 1
 auto-cost reference-bandwidth 100000
```

The value is expressed in Mbps.

🔥 Professional rule:

> If changing reference bandwidth in a real network, keep the design consistent across the OSPF domain.

---

# 🎚️ 8. Manually Setting OSPF Cost

You can explicitly configure an interface cost.

Example:

```cisco
interface GigabitEthernet0/0
 ip ospf cost 50
```

Verify:

```cisco
show ip ospf interface GigabitEthernet0/0
```

This is useful for controlled path engineering in lab and production designs.

---

# 🛣️ 9. Path Cost Example

Topology:

```text
        10             10
R1 ───────── R2 ───────── R4
 \                         /
  \          50           /
   ───────────────────────
```

Path 1:

```text
R1 → R2 → R4
10 + 10 = 20
```

Path 2:

```text
R1 → R4
50
```

OSPF chooses:

```text
20
```

🏆 Lowest total cost wins.

---

# ⚖️ 10. Administrative Distance vs OSPF Cost

Do not mix these up.

| Concept | Question it answers |
|---|---|
| Administrative Distance | Which routing source should be trusted? |
| Metric / Cost | Which path within that routing source is better? |

Cisco's default administrative distance for OSPF is commonly:

```text
110
```

OSPF cost is something else entirely.

Example:

```text
OSPF route
AD = 110
Cost = 30
```

These numbers have different jobs.

---

# 🧠 11. Longest Prefix Match Still Matters

Suppose the routing table contains:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Destination:

```text
10.10.10.50
```

The most specific matching route is:

```text
10.10.10.0/24
```

OSPF path selection does not replace the fundamental IP forwarding rule of longest-prefix matching.

---

# 🟢 12. Equal-Cost Paths

What if two paths have the same OSPF cost?

```text
           10
      ┌──────────┐
      │          │
R1 ───R2         R3
      │          │
      └──────────┘
           10
```

If two valid paths have equal cost, OSPF can install multiple equal-cost routes, subject to platform capabilities and configuration.

This is called:

> **ECMP — Equal-Cost Multi-Path**

Benefits:

```text
⚡ More forwarding options
⚖️ Load sharing
🛡️ Better resilience
```

---

# 🧪 13. ECMP Example

```text
             R2
            /  \
          10    10
          /      \
        R1        R4
          \      /
          10    10
            \  /
             R3
```

If R1 has two equal-cost ways to R3, the routing system may install both.

Verify using:

```cisco
show ip route
```

Look for multiple next-hop entries where applicable.

---

# 🔥 14. OSPF Route Selection — The Mental Model

Use this sequence:

```text
1️⃣ Is the destination reachable?
        ↓
2️⃣ Is the route known through OSPF?
        ↓
3️⃣ What type of OSPF route is it?
        ↓
4️⃣ Is there a more specific prefix?
        ↓
5️⃣ What path cost applies?
        ↓
6️⃣ Are there equal-cost paths?
        ↓
7️⃣ Is the route installed in the RIB?
```

For deeper multi-area behavior, also ask:

```text
🏠 Intra-area?
🌉 Inter-area?
🌐 External?
```

---

# 🏢 15. Why OSPF Uses Areas

Imagine one massive company with:

```text
10,000 routers
```

A single flat OSPF topology could become unnecessarily large and operationally complex.

OSPF areas help divide the topology.

Think of an area like a city district:

```text
🏙️ Entire network
 ├── 🟦 Area 0
 ├── 🟩 Area 10
 ├── 🟨 Area 20
 └── 🟥 Area 30
```

Each area has its own internal topology database.

---

# ⭐ 16. Area 0 — The Backbone

In a traditional multi-area OSPF design, **Area 0** is the backbone area.

Conceptually:

```text
Area 10
   │
   │
 Area 0
   │
   │
Area 20
```

Inter-area OSPF communication is designed around the backbone.

🔥 Memory:

```text
Area 0 = OSPF backbone
```

---

# 🌉 17. Multi-Area OSPF

Example:

```text
           Area 10
          R1 ─── R2
                |
                |
               ABR
                |
===============================
             Area 0
===============================
                |
               ABR
                |
          Area 20
          R3 ─── R4
```

The ABRs connect the areas.

---

# 🏢 18. What Is an ABR?

**ABR = Area Border Router.**

It has OSPF interfaces participating in multiple areas.

Example:

```text
Area 10
   │
  ABR
   │
Area 0
```

An ABR can:

- maintain topology information for connected areas
- participate in the backbone
- originate inter-area summary information as appropriate
- help provide reachability between areas

---

# 🧩 19. Area-Specific LSDB

This concept from the previous module becomes critical now.

```text
Area 10 LSDB
     🗺️

Area 0 LSDB
     🗺️

Area 20 LSDB
     🗺️
```

They are not one giant identical database.

Instead:

```text
🏠 Area-local topology
        ↓
🗺️ Area-specific LSDB
        ↓
🌉 Inter-area information through ABRs
```

---

# 🟦 20. Intra-Area Routes

A destination inside the same OSPF area is an **intra-area** route.

Typical Cisco route code:

```text
O
```

Example:

```text
R1 and Network X
both in Area 10
```

R1 can calculate the route using its Area 10 topology information.

---

# 🟨 21. Inter-Area Routes

A destination in another OSPF area is an **inter-area** route.

Typical Cisco route code:

```text
O IA
```

Example:

```text
Area 10
  R1
   |
  ABR
   |
Area 0
   |
  ABR
   |
Area 20
  R4
```

R1 reaching a network in Area 20 is an inter-area routing scenario.

---

# 🌉 22. Type 3 + ABR + O IA

Connect the concepts:

```text
Network in Area 10
        ↓
      ABR
        ↓
📢 Type 3 Summary LSA
        ↓
Area 0 / other area
        ↓
🛣️ O IA route
```

So when you see:

```text
O IA
```

your brain should immediately think:

```text
🌉 Another OSPF area
📢 Type 3
🏢 ABR
```

---

# 🌐 23. External Routes

External routes are introduced from outside the OSPF domain.

Example:

```text
       🌐 ISP
         │
        ASBR
         │
        OSPF
```

Common route codes include:

```text
O E1
O E2
```

and NSSA-related routes can appear as:

```text
O N1
O N2
```

External route selection is a deeper topic, but understand the categories now.

---

# 🧠 24. OSPF Route-Type Hierarchy

For CCNA-level reasoning, remember the broad preference concept:

```text
🏠 Intra-area
      ↓
🌉 Inter-area
      ↓
🌐 External
```

OSPF route-type preference rules matter before simply comparing arbitrary metrics across route categories.

⚠️ Do not treat an external route's metric as automatically comparable to an intra-area route's metric.

---

# 🗺️ 25. Topology 1 — Single Router

```text
PC ─── R1
```

Not much routing happens yet.

Purpose:

```text
Understand the OSPF process
```

---

# 🗺️ 26. Topology 2 — Two Routers

```text
LAN-A ─ R1 ───── R2 ─ LAN-B
```

Use this for:

```text
🤝 Neighbor
📢 LSA
🗺️ LSDB
🏆 SPF
```

---

# 🗺️ 27. Topology 3 — Triangle

```text
        R2
       /  \
      /    \
    R1 ─── R3
```

Use it to learn:

```text
📏 Cost comparison
🏆 Best path
🔁 Redundancy
```

---

# 🗺️ 28. Topology 4 — Four-Router Chain

```text
R1 ─ R2 ─ R3 ─ R4
```

Use it for:

```text
🌳 SPF tree
📢 LSA flooding
🛣️ Multi-hop routing
```

---

# 🗺️ 29. Topology 5 — Dual Path

```text
          R2
         /  \
        /    \
      R1      R4
        \    /
         \  /
          R3
```

Change costs and observe path selection.

---

# 🗺️ 30. Topology 6 — Equal Cost

```text
          R2
         /  \
       10    10
       /      \
     R1        R4
       \      /
       10    10
         \  /
          R3
```

Study ECMP.

---

# 🗺️ 31. Topology 7 — Area 0 + Area 10

```text
Area 10          Area 0
R1 ─── R2 ─────── ABR ───── R4
```

Study:

```text
🏠 Intra-area
🌉 Inter-area
```

---

# 🗺️ 32. Topology 8 — Three Areas

```text
Area 10       Area 0       Area 20
 R1 ─ ABR ───────── ABR ─ R4
```

Study backbone communication.

---

# 🗺️ 33. Topology 9 — Multi-Area Campus

```text
               CORE / AREA 0
             /              \
         ABR-1              ABR-2
         /                     \
   Area 10                   Area 20
   Access                    Access
```

This resembles enterprise segmentation.

---

# 🗺️ 34. Topology 10 — Data Center Style

```text
             Spine-1
            /      \
         Leaf-1   Leaf-2
            \      /
             Spine-2
```

OSPF can be used as an underlay routing protocol in suitable designs.

The purpose here is to connect SPF thinking to modern network architecture.

---

# 🗺️ 35. Topology 11 — Branch + HQ

```text
Branch-A ─┐
          ├── WAN ── HQ / Area 0 ── DC
Branch-B ─┘
```

Study:

```text
🏢 Branch routing
🌉 Area boundaries
🔁 Redundancy
```

---

# 🗺️ 36. Topology 12 — Hospital Network

```text
             Core
          /        \
     Clinical     Admin
       Area         Area
         \          /
          Data Center
```

Study:

```text
🩺 Critical paths
🛡️ Redundancy
🌉 Area design
```

---

# 🗺️ 37. Topology 13 — Dual ABR Design

```text
          ABR-1
         /     \
Area 10       Area 0
         \     /
          ABR-2
```

Use this to discuss redundant area connectivity and design considerations.

---

# 🗺️ 38. Topology 14 — External Connectivity

```text
Area 10 ─ ABR ─ Area 0 ─ ASBR ─ 🌐 ISP
```

Study the difference between:

```text
🌉 inter-area
vs
🌐 external
```

---

# 🧪 39. Practical Lab 1 — Basic SPF Observation

Topology:

```text
LAN-A ─ R1 ─ R2 ─ R3 ─ LAN-B
```

Configure OSPF.

Verify:

```cisco
show ip ospf neighbor
show ip route ospf
show ip ospf database
```

🎯 Explain how the route was calculated from the LSDB.

---

# 🧪 40. Practical Lab 2 — Change Interface Cost

Start with:

```text
R1 ─ R2 ─ R3
```

Then configure:

```cisco
interface GigabitEthernet0/1
 ip ospf cost 100
```

Verify:

```cisco
show ip ospf interface
show ip route
```

🎯 Observe how changing cost can influence the selected path.

---

# 🧪 41. Practical Lab 3 — Two Paths, Different Costs

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

Configure unequal costs.

Predict the preferred path **before** running `show ip route`.

Then verify.

🔥 Prediction first. Verification second.

---

# 🧪 42. Practical Lab 4 — Equal-Cost Paths

Make two paths have equal total OSPF cost.

Verify:

```cisco
show ip route
```

Investigate whether multiple paths are installed on your platform.

Document:

```text
Destination:
Path 1:
Path 2:
Cost:
Observed next hops:
```

---

# 🧪 43. Practical Lab 5 — Reference Bandwidth

Inspect current OSPF settings:

```cisco
show ip ospf
```

Configure a consistent lab reference bandwidth:

```cisco
router ospf 1
 auto-cost reference-bandwidth 100000
```

Then inspect interface costs.

⚠️ Use consistent values across participating routers.

---

# 🧪 44. Practical Lab 6 — Area 0 + Area 10

Build:

```text
LAN-A ─ R1 ─ ABR ─ R3 ─ LAN-B
       Area 10     Area 0
```

Verify:

```cisco
show ip ospf
show ip ospf neighbor
show ip route ospf
show ip ospf database
```

🎯 Find an `O IA` route.

---

# 🧪 45. Practical Lab 7 — Three-Area OSPF

Build:

```text
Area 10 ─ ABR ─ Area 0 ─ ABR ─ Area 20
```

Give every area at least one LAN.

Tasks:

- Configure OSPF
- Verify neighbors
- Verify routes
- Identify intra-area routes
- Identify inter-area routes
- Inspect Type 3 LSAs

---

# 🧪 46. Practical Lab 8 — Cost Engineering Challenge

Start with:

```text
          R2
         /  \
        /    \
      R1      R4
        \    /
         \  /
          R3
```

Goal:

> Make traffic from R1 to R4 prefer the lower path.

Then deliberately make the upper path cheaper.

Observe the route change.

This teaches **path engineering**, not command memorization.

---

# 🧪 47. Practical Lab 9 — Break the Preferred Path 💥

Build redundant paths.

```text
          R2
         /  \
        /    \
      R1      R4
        \    /
         \  /
          R3
```

1. Make upper path preferred.
2. Verify route.
3. Shut an interface.
4. Observe convergence.
5. Inspect OSPF database.
6. Verify new route.

Commands:

```cisco
show ip ospf neighbor
show ip ospf database
show ip route
```

---

# 🧪 48. Practical Lab 10 — Route Type Detective

Build multi-area OSPF.

Find examples of:

```text
O
O IA
```

For each route, document:

```text
Destination:
Route code:
Origin area:
Current router area:
Likely LSA type:
ABR involved?:
```

---

# 🧪 49. Practical Lab 11 — LSDB → SPF → Route

Choose one destination.

Perform the investigation in this exact order:

```cisco
show ip ospf database
show ip ospf database router
show ip route <destination>
```

Then write:

```text
1. What topology information exists?
2. Which path is available?
3. What is its cost?
4. Why did OSPF choose it?
5. What appears in the routing table?
```

---

# 🧪 50. Practical Lab 12 — Area Boundary Investigation

Build:

```text
Area 10
 R1
  |
 ABR
  |
Area 0
  |
 R3
```

Find:

```text
🧩 Area membership
🗺️ Area-specific LSDB
📢 Type 3 information
🛣️ O IA route
```

---

# 🧪 51. Practical Lab 13 — Enterprise Campus

Topology:

```text
                    CORE
                  Area 0
                 /      \
             ABR-1      ABR-2
              /            \
         Building A     Building B
          Area 10         Area 20
```

Configure at least:

```text
2 ABRs
3 areas
4+ routers
4+ LANs
```

Tasks:

- Verify all neighbors
- Verify area membership
- Inspect LSDB
- Identify Type 3 LSAs
- Identify `O IA` routes
- Break a core link
- Verify alternate routing

---

# 🧪 52. Practical Lab 14 — Final SPF War Room 👑

You receive this network:

```text
                 R2
                /  \
               /    \
             R1      R4
              |      |
             ABR----ABR
               \    /
                Area 0
```

The instructor changes:

```text
🔴 Interface cost
🔴 Area assignment
🔴 Link availability
🔴 Network advertisement
```

Your mission is to identify exactly why the route changes.

Rules:

```text
❌ No random commands
❌ No “reload and see”
❌ No guessing

✅ Predict
✅ Verify
✅ Explain
```

---

# 🐛 53. Troubleshooting — OSPF Chooses the “Wrong” Path

First ask:

```text
❓ Is the path actually lower cost?
❓ Is the route more specific?
❓ Is it intra-area/inter-area/external?
❓ Are there multiple equal-cost paths?
❓ Is the interface cost what I think it is?
```

Commands:

```cisco
show ip ospf interface
show ip route
show ip route <destination>
show ip ospf database
```

---

# 🚨 54. Troubleshooting — Cost Looks Unexpected

Check:

```cisco
show ip ospf interface
show ip ospf
```

Investigate:

```text
⚙️ Interface bandwidth
⚙️ Reference bandwidth
⚙️ Manually configured cost
⚙️ Platform behavior
```

Do not assume:

```text
GigabitEthernet = automatically the cost you memorized
```

Always verify the actual device.

---

# 🚨 55. Troubleshooting — `O IA` Route Missing

Ask:

```text
1️⃣ Is the neighbor relationship working?
2️⃣ Is the destination network actually advertised?
3️⃣ Is the ABR connected to both areas?
4️⃣ Is Area 0 present/connected as required by the design?
5️⃣ Is the Type 3 LSA present?
6️⃣ Is another route preferred?
```

Commands:

```cisco
show ip ospf neighbor
show ip ospf database summary
show ip route
show ip ospf
```

---

# 🚨 56. Troubleshooting — Area Mismatch

Example:

```text
R1 → Area 10
R2 → Area 20
```

If they are expected to form an adjacency over the same OSPF link, this is a problem.

Check:

```cisco
show ip ospf interface
show running-config
show ip ospf neighbor
```

---

# 🚨 57. Troubleshooting — One Side Uses Different Cost

Suppose:

```text
R1 cost = 10
R2 cost = 100
```

Remember that OSPF's interface cost is directional from the perspective of each interface.

A mismatch does not necessarily prevent adjacency, but it can produce unexpected path calculations.

🔥 This is why engineers verify both sides rather than assuming symmetry.

---

# 🧠 58. SPF vs Routing Table

Do not say:

> “OSPF just looks at the routing table and chooses a route.”

The conceptual flow is:

```text
LSAs
 ↓
LSDB
 ↓
SPF
 ↓
OSPF calculated routes
 ↓
RIB selection
 ↓
FIB forwarding
```

The routing table is an **output**, not the source of OSPF topology knowledge.

---

# 🧩 59. Routing Table vs LSDB vs CEF/FIB

| Database/Table | Main question |
|---|---|
| Neighbor table | Who are my OSPF neighbors? |
| LSDB | What does my OSPF topology look like? |
| RIB/routing table | Which routes are installed? |
| FIB | How will packets be forwarded? |
| ARP/neighbor cache | What Layer 2 information maps to a next hop? |

Professional troubleshooting means knowing which table answers which question.

---

# 🔎 60. Useful Cisco Commands

### OSPF process

```cisco
show ip ospf
```

### Neighbors

```cisco
show ip ospf neighbor
```

### Database

```cisco
show ip ospf database
```

### Router LSAs

```cisco
show ip ospf database router
```

### Summary LSAs

```cisco
show ip ospf database summary
```

### Interfaces

```cisco
show ip ospf interface
```

### Routing table

```cisco
show ip route
```

### OSPF routes only

```cisco
show ip route ospf
```

### Specific destination

```cisco
show ip route <destination>
```

### Protocol configuration

```cisco
show ip protocols
```

---

# 💻 61. Windows / Linux Relevance

OSPF SPF calculation itself happens on the router, so there is no equivalent Windows/Linux host command that shows the router's OSPF LSDB.

### Windows validation

```powershell
ipconfig /all
route print
ping <destination>
tracert <destination>
```

### Linux validation

```bash
ip addr
ip route
ping <destination>
traceroute <destination>
```

Use these to validate the **end-to-end result** after investigating OSPF on the router.

---

# 🏢 62. Real-World Scenario — Headquarters + Branches

Imagine:

```text
                  HQ
                Area 0
              /       \
         ABR-1       ABR-2
          /             \
   Branch-A             Branch-B
    Area 10               Area 20
```

Suppose Branch-A traffic suddenly uses a different path.

A professional engineer asks:

```text
Did the topology change?
Did interface cost change?
Did a link fail?
Did the route type change?
Did an ABR path change?
Did another route become preferred?
```

Then investigates:

```text
LSDB → SPF → route table → forwarding
```

---

# 🏥 63. Real-World Scenario — Hospital Redundancy

```text
             Core-1
            /      \
      Clinical     Admin
          \          /
             Core-2
                |
            Data Center
```

A link fails between Core-1 and a distribution router.

OSPF can provide an alternate route if the topology and costs permit it.

The NOC engineer checks:

```cisco
show ip ospf neighbor
show ip ospf database
show ip route ospf
```

The goal is not just:

> “Ping works.”

The goal is:

> **“I understand why the network selected this path.”**

---

# 🎮 64. SPF Detective Game — Round 1

You have:

```text
R1 → R2 = 10
R2 → R3 = 10
R1 → R3 = 30
```

Question:

> Which path should R1 prefer?

Calculate:

```text
R1 → R2 → R3
10 + 10 = 20

R1 → R3
30
```

🏆 Answer: via R2.

---

# 🎮 65. SPF Detective Game — Round 2

Now:

```text
R1 → R2 = 10
R2 → R3 = 10
R1 → R3 = 20
```

Question:

> What changes?

Answer:

```text
Path A = 20
Path B = 20
```

🎯 Equal-cost paths may be installed as ECMP depending on platform/configuration.

---

# 🎮 66. SPF Detective Game — Round 3

Routing table shows:

```text
O IA 10.20.0.0/24
```

What should your brain immediately ask?

```text
🌉 Which other area contains the destination?
🏢 Which ABR originated the inter-area information?
📢 Where is the Type 3 LSA?
🗺️ What does the LSDB show?
```

---

# 🎮 67. SPF Detective Game — Round 4

You see:

```text
O 10.10.10.0/24
```

Question:

> Is this necessarily a Type 3 route?

❌ No.

`O` generally indicates an intra-area OSPF route.

For an inter-area route, look for:

```text
O IA
```

---

# 🧠 68. The Four Questions of OSPF Path Selection

Whenever you see an OSPF route, ask:

```text
1️⃣ WHERE?
Which area?

2️⃣ WHAT?
Which route type?

3️⃣ HOW MUCH?
What is the cost?

4️⃣ WHICH WAY?
Which next hop/path wins?
```

This turns OSPF troubleshooting into a repeatable process.

---

# 🧪 69. Scenario Challenge — The Network Engineer Interview

Your manager says:

> “R1 is reaching the server, but traffic is using the backup link.”

Topology:

```text
             Primary
        R1 ─────────── R2
         \             /
          \           /
           ─── R3 ────
              Backup
```

Your task:

### Step 1
Verify OSPF neighbors.

### Step 2
Check interface costs.

### Step 3
Check LSDB.

### Step 4
Check route selection.

### Step 5
Check whether the primary path is actually lower cost.

### Step 6
Explain the decision.

No guessing.

---

# 🏆 70. Scenario Challenge — Area 10 Cannot Reach Area 20

Topology:

```text
Area 10
   |
  ABR
   |
 Area 0
   |
  ABR
   |
Area 20
```

Symptoms:

```text
❌ O IA route missing
❌ Ping fails
```

Investigate:

```text
🤝 Neighbor status
🗺️ LSDB
📢 Type 3 LSAs
🏢 ABR area membership
⭐ Area 0 connectivity
🛣️ Routing table
```

Write the troubleshooting sequence before touching the configuration.

---

# 🧠 71. Production Mindset

A beginner asks:

> “What command fixes OSPF?”

A network engineer asks:

> “What information is missing, where should it exist, and which protocol process is responsible for creating it?”

Use this mindset:

```text
Symptom
 ↓
Evidence
 ↓
Hypothesis
 ↓
Verification
 ↓
Root cause
 ↓
Change
 ↓
Verification
 ↓
Documentation
```

🔥 This is the troubleshooting discipline you will use throughout the rest of the repository.

---

# 🎤 72. Interview Questions — Beginner

### Q1. What is SPF?

Shortest Path First, the path calculation process OSPF uses from link-state information.

### Q2. What algorithm is OSPF associated with?

Dijkstra's shortest-path algorithm.

### Q3. What metric does OSPF use?

Cost.

### Q4. What is Area 0?

The OSPF backbone area in a multi-area design.

### Q5. What does `O IA` mean?

An OSPF inter-area route.

---

# 🎤 73. Interview Questions — Intermediate

### Q6. What is an ABR?

A router participating in multiple OSPF areas and performing area-border functions.

### Q7. Why are areas used?

To create logical boundaries that help scale OSPF and limit the amount of detailed topology information required within each area.

### Q8. What is OSPF cost?

The metric used by OSPF to compare paths.

### Q9. What is ECMP?

Equal-Cost Multi-Path: use of multiple equal-cost paths when supported and permitted.

### Q10. What is the difference between `O` and `O IA`?

`O` represents an intra-area OSPF route; `O IA` represents an inter-area OSPF route.

---

# 🎤 74. Interview Questions — Advanced

### Q11. Why is Area 0 important?

It provides the backbone structure for inter-area OSPF communication in standard multi-area designs.

### Q12. Can a router be both ABR and ASBR?

Yes.

### Q13. Why should reference bandwidth be consistent?

Inconsistent reference bandwidth can result in inconsistent OSPF cost calculations and therefore inconsistent path decisions.

### Q14. Does lower OSPF cost always mean the route is installed?

Not necessarily. Route specificity, route type, competing routing sources, and other selection rules can affect the final RIB decision.

### Q15. Does a FULL neighbor relationship guarantee every route is present?

No. Adjacency health and route advertisement/selection are related but distinct troubleshooting layers.

---

# 🎤 75. Interview Questions — Troubleshooting

### Q16. OSPF neighbor is FULL but a route is missing. What do you check?

Check whether the network is advertised, the expected LSA exists, area membership is correct, and the route is eligible for installation.

### Q17. OSPF uses the backup link. What do you check?

Check interface costs, reference bandwidth, topology, route type, and actual installed next hops.

### Q18. Why can changing interface cost change the route?

Because OSPF uses cost to calculate and compare paths.

### Q19. How do you inspect the OSPF database?

```cisco
show ip ospf database
```

### Q20. How do you verify OSPF routes?

```cisco
show ip route ospf
```

---

# ⚡ 76. Quick Revision

```text
🧠 SPF
= Shortest Path First

🌳 OSPF
= link-state path calculation

📏 Cost
= OSPF metric

🏆 Lower valid path cost
= preferred path

⚖️ Equal cost
= ECMP may be possible

⭐ Area 0
= backbone

🏢 ABR
= connects OSPF areas

🏠 O
= intra-area

🌉 O IA
= inter-area

🌐 O E1/E2
= external

📢 Type 3
= inter-area summary information

🗺️ LSDB
→ SPF
→ route calculation
→ RIB
→ FIB
```

---

# 🧾 77. Command Cheat Sheet

| Task | Command |
|---|---|
| OSPF process | `show ip ospf` |
| Neighbors | `show ip ospf neighbor` |
| OSPF database | `show ip ospf database` |
| Router LSAs | `show ip ospf database router` |
| Summary LSAs | `show ip ospf database summary` |
| Interface OSPF info | `show ip ospf interface` |
| OSPF routes | `show ip route ospf` |
| Full routing table | `show ip route` |
| Specific route | `show ip route <destination>` |
| Protocols | `show ip protocols` |
| Set interface cost | `ip ospf cost <value>` |
| Reference bandwidth | `auto-cost reference-bandwidth <Mbps>` |

---

# 🧭 78. Professional Troubleshooting Flow

```text
🚨 Unexpected route
       ↓
🤝 Neighbor healthy?
       ↓
📢 LSA present?
       ↓
🗺️ LSDB correct?
       ↓
🏠 / 🌉 / 🌐 Route type?
       ↓
📏 Cost correct?
       ↓
🧠 SPF result expected?
       ↓
🛣️ RIB selection?
       ↓
🚀 FIB forwarding?
```

Do not jump directly to configuration changes.

---

# 📚 79. Practice Exercises

## 🟢 Beginner

1. Define SPF.
2. Define OSPF cost.
3. Explain Area 0.
4. Explain ABR.
5. Explain `O` vs `O IA`.

## 🟡 Intermediate

6. Calculate the cost of three candidate paths.
7. Create an equal-cost topology.
8. Identify an ABR from a topology.
9. Explain Type 3 → `O IA`.
10. Explain why two areas do not have identical LSDBs.

## 🔴 Advanced

11. Design a three-area campus.
12. Engineer primary and backup paths using cost.
13. Troubleshoot a missing inter-area route.
14. Explain an unexpected ECMP result.
15. Reconstruct the SPF decision from LSDB information.

---

# 🧠 80. Build Your Own OSPF Design

Design a company network with:

```text
🏢 HQ
🏢 Branch A
🏢 Branch B
🏥 Data Center
🌐 Internet Edge
```

Requirements:

- Area 0 backbone
- At least two non-backbone areas
- Redundant links
- One ABR serving each branch area
- One ASBR/edge concept
- At least one equal-cost path
- Document OSPF costs
- Document expected route types

Deliverables:

```text
📐 Topology
📋 Addressing table
⚙️ Configuration
🔎 Verification
🧠 Path-selection explanation
🐛 Troubleshooting plan
```

---

# 🏁 81. Completion Checklist

### SPF

- [ ] I can explain SPF
- [ ] I understand the shortest-path concept
- [ ] I can calculate simple OSPF costs
- [ ] I understand path cost
- [ ] I understand ECMP

### Areas

- [ ] I understand Area 0
- [ ] I understand ABR
- [ ] I understand intra-area routing
- [ ] I understand inter-area routing
- [ ] I understand `O IA`
- [ ] I understand Type 3's role

### Verification

- [ ] `show ip ospf`
- [ ] `show ip ospf neighbor`
- [ ] `show ip ospf database`
- [ ] `show ip ospf database summary`
- [ ] `show ip ospf interface`
- [ ] `show ip route ospf`
- [ ] `show ip route <destination>`

### Labs

- [ ] Lab 1
- [ ] Lab 2
- [ ] Lab 3
- [ ] Lab 4
- [ ] Lab 5
- [ ] Lab 6
- [ ] Lab 7
- [ ] Lab 8
- [ ] Lab 9
- [ ] Lab 10
- [ ] Lab 11
- [ ] Lab 12
- [ ] Lab 13
- [ ] Lab 14

### Design

- [ ] I can design a basic multi-area OSPF topology
- [ ] I can explain why Area 0 matters
- [ ] I can predict a path before verifying it
- [ ] I can explain why OSPF selected a route

---

# 🔗 82. Connect the Dots

### ⬅️ Previous — Module 25

```text
📢 LSAs
 ↓
🗺️ LSDB
 ↓
🔢 Sequence
 ↓
⏳ Aging
 ↓
🧩 LSA Types
```

### 👉 This Module

```text
🗺️ LSDB
 ↓
🌳 SPF
 ↓
📏 Cost
 ↓
🏆 Path Selection
 ↓
⭐ Areas
 ↓
🌉 ABRs
 ↓
🛣️ Intra/Inter-Area Routes
```

### ➡️ Next — Module 27

# ⚙️ 27 — OSPF Configuration & Optimization

We will move from **understanding OSPF** to **building professional OSPF configurations**:

```text
🧑‍💻 OSPF process
 ↓
📡 Network statements
 ↓
🎯 Router ID
 ↓
🚫 Passive interfaces
 ↓
📏 Cost tuning
 ↓
🌉 Multi-area configuration
 ↓
🔧 Optimization
 ↓
🔎 Verification
 ↓
🐛 Troubleshooting
```

---

# 🌟 Final Takeaway

You should now be able to tell the complete OSPF story:

```text
🤝 Neighbors
      ↓
📢 LSAs
      ↓
🗺️ LSDB
      ↓
🌳 SPF
      ↓
📏 Cost comparison
      ↓
🏆 Best path
      ↓
🛣️ Routing table
      ↓
🚀 Forwarding
```

And when the network becomes larger:

```text
🏢 Area 10 ──🌉── ⭐ Area 0 ──🌉── Area 20
                    │
                  Area 30
```

The most important mental model is:

> 🧠 **OSPF first builds a map, then calculates the best roads on that map. Areas divide the map into manageable regions, while ABRs connect those regions through the backbone.**

🔥 **Don't memorize SPF. Visualize it. Don't memorize O IA. Understand why the route became inter-area. Don't memorize cost. Calculate it.**

That is how you move from **CCNA student → network engineer mindset.** 🚀🌐🧠
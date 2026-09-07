# 🛣️ 21 — Static Routing

> **Teach the router the road. Then prove the road works. Then break it. Then fix it. 🔥**
>
> Static routing is where routing stops being only a concept and becomes a real configuration skill.

---

# 🗺️ Your Journey So Far

```text
🧱 Networking
   ↓
🔌 Ethernet + MAC
   ↓
🔀 Switching
   ↓
🟦 VLANs
   ↓
🔗 Trunking
   ↓
🌳 STP
   ↓
⚡ EtherChannel
   ↓
🔀 Inter-VLAN Routing
   ↓
🌐 Routing Fundamentals
   ↓
👉 🛣️ STATIC ROUTING  ← YOU ARE HERE
   ↓
🤖 Dynamic Routing
   ↓
🧠 OSPF
   ↓
🔐 ACL
   ↓
🌍 NAT
```

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- ✅ Explain static routing in simple terms
- ✅ Understand why a router needs routes to remote networks
- ✅ Configure IPv4 static routes
- ✅ Understand next-hop static routes
- ✅ Understand exit-interface static routes
- ✅ Understand fully specified static routes
- ✅ Configure default static routes
- ✅ Configure host routes
- ✅ Understand floating static routes
- ✅ Understand administrative distance in static routing
- ✅ Understand recursive lookup in static routes
- ✅ Verify static routes with Cisco IOS
- ✅ Troubleshoot incorrect routes
- ✅ Build increasingly complex routing topologies
- ✅ Understand when static routing is appropriate in real networks
- ✅ Prepare for dynamic routing and OSPF

---

# 🧠 1. What Is Static Routing?

A **static route is a route manually configured by a network administrator.**

The router does not learn it automatically from a routing protocol.

You explicitly tell it:

> **“To reach this network, send traffic this way.”**

Think of giving a delivery driver a handwritten instruction:

```text
📦 Destination:
192.168.30.0/24

➡️ Take this road:
10.0.0.2
```

That's essentially a static route.

---

# 🏙️ 2. Road Map Analogy

Imagine three cities:

```text
🏙️ Pune ─── 🚗 ─── 🏙️ Mumbai ─── 🚗 ─── 🏙️ Ahmedabad
```

If the Pune driver doesn't know the road to Ahmedabad, someone must tell them:

```text
“Go through Mumbai.”
```

Networking:

```text
🟦 LAN-A
   │
  R1
   │
  R2
   │
🟩 LAN-B
```

R1 may know its directly connected networks but not automatically know R2's LAN.

You can manually tell R1:

```text
192.168.20.0/24 → 10.0.0.2
```

Now R1 has a path.

---

# 🚨 3. The Problem Static Routes Solve

Consider:

```text
PC-A 🟦
192.168.10.10
      │
      ▼
     R1
      │
      │ 10.0.0.0/30
      │
      ▼
     R2
      │
      ▼
PC-B 🟩
192.168.20.10
```

R1 directly knows:

```text
C 192.168.10.0/24
C 10.0.0.0/30
```

But R1 does **not automatically know**:

```text
192.168.20.0/24
```

So if PC-A sends traffic there, R1 may have nowhere to forward it.

We manually add:

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

Now R1 knows:

```text
192.168.20.0/24 → 10.0.0.2
```

---

# 🧩 4. Static Route Anatomy

The basic Cisco IOS syntax is:

```cisco
ip route <destination-network> <subnet-mask> <next-hop>
```

Example:

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

Break it apart:

```text
ip route
   ↓
192.168.20.0
   ↓
Destination network
   ↓
255.255.255.0
   ↓
Destination mask
   ↓
10.0.0.2
   ↓
Next hop
```

🧠 Read it as:

> **“To reach 192.168.20.0/24, send the packet to 10.0.0.2.”**

---

# ⭐ 5. First Static Route — Complete Example

## Topology

```text
🖥️ PC-A
192.168.10.10
     │
     ▼
   🟦 SW1
     │
     ▼
    🔀 R1
192.168.10.1
     │
10.0.0.1/30
     │
     ▼
    🔀 R2
10.0.0.2/30
     │
     ▼
   🟩 SW2
     │
     ▼
🖥️ PC-B
192.168.20.10
```

## Addressing Table

| Device | Interface | IP | Network |
|---|---|---|---|
| PC-A | NIC | `192.168.10.10/24` | LAN-A |
| R1 | G0/0 | `192.168.10.1/24` | LAN-A |
| R1 | G0/1 | `10.0.0.1/30` | Transit |
| R2 | G0/0 | `10.0.0.2/30` | Transit |
| R2 | G0/1 | `192.168.20.1/24` | LAN-B |
| PC-B | NIC | `192.168.20.10/24` | LAN-B |

### R1 configuration

```cisco
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

### R2 configuration

R2 needs a route back to LAN-A:

```cisco
R2(config)# ip route 192.168.10.0 255.255.255.0 10.0.0.1
```

### Test

From PC-A:

```text
ping 192.168.20.10
```

🎉 If both directions are correctly routed, communication works.

---

# 🔥 6. The Golden Rule of Static Routing

> **A route on one router is not enough for two-way communication.**

Think:

```text
A ─────► B

B ─────► A
```

Both directions need a valid path.

A common mistake is:

```text
R1 knows LAN-B ✅
R2 does not know LAN-A ❌
```

Result:

```text
Request → reaches B
Reply   → gets lost ❌
```

🔥 Always check the **return route**.

---

# 🧭 7. Next-Hop Static Route

The most common beginner form:

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

The final address is the **next-hop IP**.

The router asks:

> “How do I reach 10.0.0.2?”

This can involve another lookup.

That's the recursive lookup concept from the previous module.

---

# 🔌 8. Exit-Interface Static Route

You can specify an outgoing interface:

```cisco
ip route 192.168.20.0 255.255.255.0 g0/1
```

Meaning:

> Send traffic for this destination out G0/1.

This form can behave differently depending on whether the outgoing interface is point-to-point or multi-access Ethernet, so learn to verify the resulting route and next-hop resolution.

---

# 🧱 9. Fully Specified Static Route

A fully specified route includes both:

- exit interface
- next-hop IP

Example:

```cisco
ip route 192.168.20.0 255.255.255.0 g0/1 10.0.0.2
```

Think:

```text
🎯 Destination
      ↓
➡️ Exit Interface
      ↓
👤 Next-Hop IP
```

This can remove ambiguity in certain network designs.

---

# 🆚 10. Three Static Route Styles

| Type | Example |
|---|---|
| Next hop | `ip route 192.168.20.0 255.255.255.0 10.0.0.2` |
| Exit interface | `ip route 192.168.20.0 255.255.255.0 g0/1` |
| Fully specified | `ip route 192.168.20.0 255.255.255.0 g0/1 10.0.0.2` |

🧠 **Exam tip:** Know what information each form gives the router.

---

# 🌎 11. Default Static Route

A default route means:

> **“For destinations I don't otherwise know, send traffic here.”**

IPv4:

```text
0.0.0.0/0
```

Cisco syntax:

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

Example:

```text
             🌐 Internet
                 ▲
                 │
              ISP/R2
                 ▲
                 │
                 R1
              /       \
          LAN-A       LAN-B
```

R1 can use a default route toward the upstream router for unknown destinations.

---

# 🏠 12. Why Branch Routers Love Default Routes

A small branch may have:

```text
🏢 Branch
  │
  R1
  │
  └────────► 🏢 HQ / ISP
```

The branch does not need to manually list every possible internet destination.

Instead:

```cisco
ip route 0.0.0.0 0.0.0.0 <upstream-next-hop>
```

Everything not matched by a more specific route can use the default.

---

# 🎯 13. Host Route

A host route points to exactly one IPv4 address.

```text
/32
```

Example:

```cisco
ip route 192.168.20.50 255.255.255.255 10.0.0.2
```

This means:

```text
🎯 Exactly 192.168.20.50
```

Not the entire `192.168.20.0/24` network.

---

# 🧠 14. When Would You Use a Host Route?

Possible uses include:

- 🎯 Specific server
- 🎯 Specific loopback address
- 🎯 Special forwarding requirement
- 🎯 Testing or controlled routing
- 🎯 Highly specific path selection

Example:

```text
192.168.20.0/24 → R2
192.168.20.50/32 → R3
```

Traffic to `.50` can follow the more specific `/32` route.

---

# 🏆 15. Longest Prefix Match Returns!

Suppose the router has:

```text
S 192.168.20.0/24 → R2
S 192.168.20.50/32 → R3
```

Destination:

```text
192.168.20.50
```

Which wins?

```text
/32 🏆
```

Why?

Because it is more specific.

```text
/24 → whole network
/32 → one exact host
```

🔥 This connects Static Routing directly to the Routing Fundamentals module.

---

# 🛟 16. Floating Static Route

A **floating static route** is a static route configured with a higher administrative distance so that it is less preferred than another route under normal conditions.

Example:

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.2 200
```

The final `200` is the administrative distance.

Concept:

```text
Primary route       → preferred 🟢
Backup static route → less preferred 🟡
```

If the preferred route disappears, the floating static route can become usable if its next hop/path is reachable and the route is otherwise valid.

---

# 🚨 17. Why Floating Static Routes Are Useful

Imagine:

```text
          Primary Link
R1 ─────────────────── R2
 │
 │ Backup Link
 └──────────────────── R3
```

Normal:

```text
R1 → Primary 🟢
```

Failure:

```text
Primary ❌
    ↓
Backup 🟡 becomes preferred available option
```

This introduces a very important engineering idea:

> **Not every route should be equal.**

---

# ⚖️ 18. Administrative Distance and Static Routes

By default, a static route has an administrative distance of **1**.

Connected routes have AD **0**.

A floating static route uses a higher value, for example:

```text
200
```

This makes it less preferred than routes with lower AD to the same destination.

Remember:

```text
Lower AD = more preferred route source
```

And separately:

```text
Longest prefix match = first major specificity rule
```

Do not treat AD as a replacement for longest-prefix matching.

---

# 🧠 19. Static Route vs Default Route

| Feature | Static Route | Default Route |
|---|---|---|
| Destination | Specific network/host | Everything not otherwise matched |
| Prefix | Any specific prefix | `/0` |
| Example | `192.168.20.0/24` | `0.0.0.0/0` |
| Common use | Known remote network | Internet/upstream path |

---

# 🧠 20. Static vs Dynamic Routing

| Feature | Static 🛣️ | Dynamic 🤖 |
|---|---|---|
| Configuration | Manual | Protocol-based |
| Adaptation | Manual changes | Can adapt automatically |
| Overhead | Low | Higher |
| Small networks | Excellent | Useful but may be unnecessary |
| Large networks | Hard to maintain | Much more scalable |
| Predictability | High | Depends on protocol/design |
| Failure recovery | Manual unless backup design exists | Often automatic |

Examples of dynamic routing protocols include OSPF and EIGRP.

For this CCNA journey, **OSPF** will be especially important.

---

# 🏢 21. When Static Routing Makes Sense

Static routes are useful for:

- 🏠 Small networks
- 🏢 Small branch offices
- 🌐 Stub networks
- 🚪 Default paths
- 🛟 Backup/floating routes
- 🎯 Specific host/network paths
- 🔐 Controlled routing requirements
- 🧪 Labs

They become difficult when there are many routers and many changing paths.

---

# 🗺️ 22. Topology Gallery — 12 Static Routing Designs

## 🟦 Topology 1 — Two Routers

```text
LAN-A ─ R1 ─ R2 ─ LAN-B
```

Perfect first lab.

---

## 🟩 Topology 2 — Three Routers

```text
LAN-A ─ R1 ─ R2 ─ R3 ─ LAN-C
```

Practice multiple remote networks.

---

## 🟨 Topology 3 — Four-Router Chain

```text
A ─ R1 ─ R2 ─ R3 ─ R4 ─ D
```

Great for next-hop reasoning.

---

## 🟥 Topology 4 — Hub-and-Spoke

```text
        Branch 1
           │
Branch 2 ─ R-HUB ─ Branch 3
           │
        Branch 4
```

Central static routing.

---

## 🟪 Topology 5 — Branch to HQ

```text
🏢 Branch ─ R1 ─ WAN ─ R2 ─ 🏢 HQ
```

Real-world static route example.

---

## 🟧 Topology 6 — Branch Default Route

```text
Branch LAN
    │
   R1 ─────► ISP
          0.0.0.0/0
```

---

## 🟫 Topology 7 — Backup Route

```text
          Primary
R1 ───────────────── R2
 │
 └──── Backup ────── R3
```

Floating static route concept.

---

## 🟦 Topology 8 — Specific Host Route

```text
LAN ─ R1 ─┬─ Server Network
          └─ Special Server 🎯
```

Use `/32` specificity.

---

## 🟩 Topology 9 — Campus Core

```text
Access SW → L3 Core → Router → ISP
```

Static default toward the edge.

---

## 🟨 Topology 10 — Two Branches + HQ

```text
Branch A ─┐
          ├── HQ ── Internet
Branch B ─┘
```

Practice multiple remote routes.

---

## 🟥 Topology 11 — Redundant WAN

```text
             ISP-1
            /     \
Branch ─── R1     HQ
            \     /
             ISP-2
```

Primary + backup path.

---

## 🟪 Topology 12 — Enterprise Preview

```text
                 🌐 Internet
                     │
                  Firewall
                     │
                 Core Router
                /           \
          Campus             Branch WAN
          /   \
       Users Servers
```

Static routes can appear at selected edges, even when dynamic routing handles the core.

---

# 🧪 23. Practical Lab Series

> 🎯 **The labs intentionally become harder. Do not skip the troubleshooting labs.**

---

## 🧪 LAB 1 — First Static Route ⭐

Build:

```text
PC-A ─ SW1 ─ R1 ─ R2 ─ SW2 ─ PC-B
```

Configure only the interfaces first.

Verify:

```cisco
show ip route
```

Identify which networks are missing.

Then configure static routes.

Test:

```text
ping
```

---

## 🧪 LAB 2 — Next-Hop vs Exit Interface

Build two routers with a point-to-point link.

Configure the same destination using:

1. Next-hop style
2. Exit-interface style
3. Fully specified style

Compare:

```cisco
show ip route
```

and:

```cisco
show running-config
```

🎯 Goal: understand the difference, not just memorize syntax.

---

## 🧪 LAB 3 — Three-Router Chain 🔥

```text
LAN-A ─ R1 ─ R2 ─ R3 ─ LAN-C
```

Every router must know how to reach the remote LANs.

Create a routing table on paper before configuration.

---

## 🧪 LAB 4 — Default Route 🌎

Build:

```text
Branch ─ R1 ─ R2/ISP ─ Internet Simulation
```

Configure a default route on R1.

Verify:

```cisco
show ip route
```

Find:

```text
S* 0.0.0.0/0
```

---

## 🧪 LAB 5 — Host Route 🎯

Create:

```text
LAN-A ─ R1 ─ R2 ─ Server LAN
```

Configure a `/32` route toward one server.

Test that host.

Then add a broader network route and observe which route wins for the host.

---

## 🧪 LAB 6 — Longest Prefix Challenge 🏆

Configure:

```text
192.168.0.0/16
192.168.20.0/24
192.168.20.50/32
```

Test:

```text
192.168.20.50
192.168.20.60
192.168.30.10
```

Predict the selected route before checking the router.

---

## 🧪 LAB 7 — Floating Static Route 🛟

Build:

```text
          R2
         /  \
        /    \
R1 ────      ─── LAN
        \    /
         \  /
          R3
```

Create:

```text
Primary static route → low AD
Backup static route  → higher AD
```

Verify the routing table.

Then simulate failure of the primary path.

Observe the backup route.

---

## 🧪 LAB 8 — Broken Next Hop 🐛

Deliberately configure:

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.99
```

when `10.0.0.99` is not the intended next hop.

Investigate:

```cisco
show ip route
ping 10.0.0.99
show ip interface brief
```

---

## 🧪 LAB 9 — Missing Return Route 🚨

Configure R1's route correctly.

Do not configure R2's return route.

Observe:

```text
Request → reaches destination
Reply   → fails
```

Find the problem.

---

## 🧪 LAB 10 — Full Static Routing Challenge 🏆🔥

Build:

```text
       🟦 Branch A
            │
           R1
          /  \
         /    \
       R2      R3
         \    /
          \  /
           R4
            │
       🟥 Data Center
```

Requirements:

- Multiple LANs
- Multiple transit networks
- Static routes
- One default route
- One host route
- One floating static backup
- Full return paths
- Verification
- Failure testing

Then document:

```text
Destination
→ Next hop
→ Exit interface
→ Why this route wins
```

---

# 🔎 24. Cisco Verification Commands

## ⭐ Routing table

```cisco
show ip route
```

Your first command for route verification.

---

## ⭐ Specific route

```cisco
show ip route 192.168.20.0
```

Ask the router how it currently reaches a destination.

---

## ⭐ Static routes only

```cisco
show ip route static
```

Useful for isolating configured static routes.

---

## ⭐ Configuration

```cisco
show running-config
```

Search for:

```cisco
ip route
```

---

## ⭐ Interfaces

```cisco
show ip interface brief
```

A route cannot work correctly if the underlying path is broken.

---

## ⭐ Ping

```cisco
ping 192.168.20.10
```

---

## ⭐ Traceroute

```cisco
traceroute 192.168.20.10
```

---

## ⭐ ARP

```cisco
show arp
```

Useful when investigating next-hop resolution on Ethernet networks.

---

# 💻 25. Windows Verification

```powershell
ipconfig /all
route print
ping 192.168.20.10
tracert 192.168.20.10
arp -a
```

Ask:

```text
Does the PC have the correct gateway?
Does the PC have a route to the destination?
What path does tracert show?
```

---

# 🐧 26. Linux Verification

```bash
ip addr
ip route
ip neigh
ping 192.168.20.10
traceroute 192.168.20.10
```

The Linux equivalent of your routing-table thinking is especially visible through:

```bash
ip route
```

---

# 🧠 27. Static Route Troubleshooting — The Professional Flow

```text
                 ❌ Remote network unreachable
                            │
                            ▼
                  Can host reach gateway?
                       /          \
                     NO            YES
                     │               │
                Fix host config     ▼
                              Can router reach next hop?
                                  /          \
                                NO            YES
                                │               │
                        Check interface         ▼
                                           Does route exist?
                                            /         \
                                          NO           YES
                                          │              │
                                  Configure/fix route   ▼
                                                   Is route correct?
                                                       │
                                                       ▼
                                                Check return path
                                                       │
                                                       ▼
                                                  Check filtering
```

---

# 🚨 28. Common Static Routing Mistakes

## ❌ Mistake 1 — Wrong destination network

```cisco
ip route 192.168.30.0 ...
```

when the real destination is:

```text
192.168.20.0/24
```

---

## ❌ Mistake 2 — Wrong subnet mask

```cisco
ip route 192.168.20.0 255.255.0.0 ...
```

when `/24` was intended.

A route's prefix determines what destinations it matches.

---

## ❌ Mistake 3 — Wrong next hop

The next hop must be reachable through the appropriate path.

---

## ❌ Mistake 4 — Interface is down

```cisco
show ip interface brief
```

---

## ❌ Mistake 5 — Missing return route

The most common conceptual mistake.

---

## ❌ Mistake 6 — Expecting static routing to adapt automatically

A normal static route does not dynamically discover a new path simply because another path exists.

---

## ❌ Mistake 7 — Wrong administrative distance

A backup route with an incorrect AD may become preferred unexpectedly.

---

## ❌ Mistake 8 — Forgetting longest-prefix matching

A more specific route can beat a broader route even if you expected the broader route to be used.

---

## ❌ Mistake 9 — Testing only from the router

Always test from the actual endpoint too.

```text
Router test ≠ complete application test
```

---

## ❌ Mistake 10 — No documentation

Static routing becomes difficult to maintain when nobody remembers why a route exists.

Professional engineers document:

```text
Destination
Next hop
Purpose
Owner
Backup
Change reason
```

---

# 🧠 29. Route Verification Mindset

Never simply ask:

> “Did I type the command?”

Ask:

> **“Did the router install the route I intended?”**

Use:

```cisco
show ip route
```

Then ask:

```text
🎯 Is destination correct?
📏 Is prefix/mask correct?
➡️ Is next hop correct?
🔌 Is exit interface correct?
⚖️ Is AD appropriate?
🔁 Is return route present?
```

---

# 🧪 30. Troubleshooting Scenario Cards

## 🟥 Card A — "Route Exists, Ping Fails"

You see:

```text
S 192.168.20.0/24 [1/0] via 10.0.0.2
```

But ping fails.

Investigate:

- Can the router reach `10.0.0.2`?
- Is the outgoing interface up?
- Does R2 know the return route?
- Is the destination host online?
- Is filtering involved?

---

## 🟨 Card B — "Everything Works Except One Server"

Users can reach the server network except:

```text
192.168.20.50
```

Check for a more specific route such as `/32`.

---

## 🟩 Card C — "Backup Route Never Activates"

You configured a floating static route.

Primary fails.

Backup still doesn't appear.

Investigate:

```text
Is the backup next hop reachable?
Is the route configured correctly?
Is the backup route actually less preferred under normal conditions?
Does the platform's route resolution support the intended behavior?
```

---

## 🟦 Card D — "Only One Direction Works"

```text
A → B ✅
B → A ❌
```

Your first suspicion should be:

> **Return-path problem.**

Then verify.

---

# 🏢 31. Real-World Scenario — Small Branch Office

A company has:

```text
🏢 Branch

Users     10.10.10.0/24
Printers  10.10.20.0/24
Mgmt      10.10.50.0/24
```

Branch router:

```text
R1
 │
 └──── WAN ──── HQ
```

The branch may use:

```text
Connected routes
      +
Specific static routes
      +
Default route
      +
Floating backup
```

This is a realistic hybrid approach.

---

# 🏥 32. Real-World Scenario — Hospital WAN

```text
Hospital A ───── HQ ───── Hospital B
```

A small remote site might have a limited number of networks.

Static routing can be perfectly reasonable when:

- topology is simple
- paths rarely change
- centralized design is predictable
- operational overhead must remain low

But as the organization grows, manually maintaining hundreds of routes becomes painful.

That is where dynamic routing becomes increasingly valuable.

---

# 🧨 33. Scenario Challenge — "The Broken Branch"

You are given:

```text
              HQ
               │
              R2
             /  \
            /    \
          R1      R3
          │        │
       Branch A  Backup
```

Branch A must reach:

```text
192.168.100.0/24
```

Requirements:

1. Primary path through R2
2. Backup path through R3
3. Backup must be less preferred
4. Return path must work
5. Verify normal operation
6. Simulate primary failure
7. Verify backup operation

### 🎯 Your documentation must show

```text
Normal route:
Destination → next hop → AD

Backup route:
Destination → next hop → AD
```

---

# 🎮 34. Routing Engineer Game — "Choose the Road"

You are R1. 🧑‍💻

Routing table:

```text
192.168.0.0/16      → R2
192.168.10.0/24     → R3
192.168.10.50/32    → R4
0.0.0.0/0           → R5
```

### Destination 1

```text
192.168.10.50
```

🏆 Winner: `/32` via R4.

### Destination 2

```text
192.168.10.60
```

🏆 Winner: `/24` via R3.

### Destination 3

```text
192.168.50.10
```

🏆 Winner: `/16` via R2.

### Destination 4

```text
8.8.8.8
```

🏆 If no more-specific route exists, `/0` via R5 can be used.

🔥 You are now practicing routing logic rather than command memorization.

---

# 🎤 35. Interview Questions

## 🟢 Beginner

**Q1. What is static routing?**

A manually configured route to a destination network or host.

**Q2. What command configures an IPv4 static route on Cisco IOS?**

```cisco
ip route <destination> <mask> <next-hop-or-exit-interface>
```

**Q3. What is a default route?**

A route used when no more specific route matches the destination.

**Q4. What is a host route?**

A route to one IPv4 address, normally `/32`.

---

## 🟡 Intermediate

**Q5. What is a floating static route?**

A static route configured with a higher administrative distance so it is less preferred than another route under normal conditions.

**Q6. Why do you need a return route?**

Because the destination must be able to send the response back to the source.

**Q7. Difference between next-hop and exit-interface static routes?**

One specifies the next Layer 3 address; the other specifies the outgoing interface.

**Q8. What is a fully specified static route?**

A static route specifying both the exit interface and next-hop address.

---

## 🔴 Advanced

**Q9. What is recursive lookup?**

The router performs additional lookup(s) to resolve how to reach a next-hop address.

**Q10. Why can a `/32` beat a `/24`?**

Because `/32` is more specific and wins longest-prefix matching when both match.

**Q11. What is the default administrative distance of a static route?**

Typically 1 on Cisco IOS.

**Q12. Why might a static route not appear in the routing table?**

The route may be incorrectly configured, or its next hop/exit path may not be resolvable or operational, depending on the route form and platform behavior.

---

# ⚡ 36. Quick Revision

```text
🛣️ Static Route
= manually configured path

🎯 Destination
= network/host you want to reach

➡️ Next hop
= next Layer 3 address

🔌 Exit interface
= interface used to send traffic

🧾 Fully specified
= exit interface + next hop

🌎 Default route
= 0.0.0.0/0

🎯 Host route
= /32

🛟 Floating static
= backup static route with higher AD

🏆 Longest prefix
= most specific matching route

🔁 Return route
= required for two-way communication
```

---

# 🧾 37. Static Routing Cheat Sheet

| Requirement | Example |
|---|---|
| Network route | `ip route 192.168.20.0 255.255.255.0 10.0.0.2` |
| Exit interface | `ip route 192.168.20.0 255.255.255.0 g0/1` |
| Fully specified | `ip route 192.168.20.0 255.255.255.0 g0/1 10.0.0.2` |
| Default route | `ip route 0.0.0.0 0.0.0.0 10.0.0.2` |
| Host route | `ip route 192.168.20.50 255.255.255.255 10.0.0.2` |
| Floating route | `ip route 192.168.20.0 255.255.255.0 10.0.0.2 200` |
| Routing table | `show ip route` |
| Static routes | `show ip route static` |
| Specific route | `show ip route <destination>` |
| Interfaces | `show ip interface brief` |
| Configuration | `show running-config` |
| Reachability | `ping <ip>` |
| Path | `traceroute <ip>` |

---

# 🧭 38. Static Routing Decision Tree

```text
             🌐 Need to reach remote network
                         │
                         ▼
                 Is route installed?
                    /          \
                  NO            YES
                  │               │
            Check config          ▼
                             Is next hop reachable?
                                /          \
                              NO            YES
                              │               │
                        Check interface       ▼
                                           Test destination
                                               │
                                               ▼
                                        Check return route
                                               │
                                               ▼
                                         Check filtering
```

---

# 🧠 39. Professional Documentation Template

Every static route in a real environment should have a reason.

Use a table like:

| Destination | Prefix | Next Hop | AD | Purpose |
|---|---|---|---:|---|
| `10.20.0.0` | `/24` | `10.0.0.2` | 1 | HQ users |
| `10.30.0.0` | `/24` | `10.0.0.2` | 1 | Server network |
| `0.0.0.0` | `/0` | `10.0.0.2` | 1 | Internet |
| `10.20.0.0` | `/24` | `10.0.0.6` | 200 | Backup |

This habit will become extremely useful in real network operations.

---

# 🏁 40. Completion Checklist

### Concepts

- [ ] I can explain static routing
- [ ] I understand remote networks
- [ ] I understand next hops
- [ ] I understand exit interfaces
- [ ] I understand fully specified routes
- [ ] I understand default routes
- [ ] I understand host routes
- [ ] I understand floating static routes

### Routing Logic

- [ ] I understand longest-prefix match
- [ ] I understand administrative distance
- [ ] I understand recursive lookup
- [ ] I understand return paths

### Commands

- [ ] I can configure `ip route`
- [ ] I can verify `show ip route`
- [ ] I can use `show ip route static`
- [ ] I can use `show ip route <destination>`
- [ ] I can use `ping`
- [ ] I can use `traceroute`

### Labs

- [ ] Lab 1 complete
- [ ] Lab 2 complete
- [ ] Lab 3 complete
- [ ] Lab 4 complete
- [ ] Lab 5 complete
- [ ] Lab 6 complete
- [ ] Lab 7 complete
- [ ] Lab 8 complete
- [ ] Lab 9 complete
- [ ] Lab 10 complete

### Engineer Mindset

- [ ] I check the route before changing configuration
- [ ] I verify the next hop
- [ ] I verify the return path
- [ ] I test from the endpoint
- [ ] I document why a route exists

---

# 🔗 41. Connect the Dots

### ⬅️ Previous

**Routing Fundamentals** taught you:

```text
🗺️ Routing table
🏆 Longest prefix
⚖️ AD
➡️ Next hop
🔁 Recursive lookup
```

### 👉 This module added

```text
🛣️ Manual routes
🌎 Default routes
🎯 Host routes
🛟 Backup routes
```

### ➡️ Next

```text
🛣️ Static Routing
       ↓
🤖 Dynamic Routing Concepts
       ↓
🧠 OSPF
```

The next stage is exciting because the network will begin learning routes **automatically**.

---

# 🏆 42. Final Master Challenge — "Build a Branch Network"

You are the network engineer for a growing company.

Build this in Packet Tracer:

```text
                         🌐 Internet
                             │
                            ISP
                             │
                            R4
                           /  \
                          /    \
                       R2       R3
                       │         │
                       └────┬────┘
                            │
                           R1
                         /    \
                        /      \
                   🟦 Users   🟥 Servers
```

### Requirements

#### 🟦 Users

```text
192.168.10.0/24
```

#### 🟥 Servers

```text
192.168.20.0/24
```

#### 🔗 Transit networks

Use `/30` networks between routers.

### Configure

- Connected interfaces
- Static routes
- Default route
- Host route to one important server
- Floating backup route
- Correct return paths

### Verify

```cisco
show ip route
show ip route static
show ip interface brief
ping
traceroute
```

### Then BREAK IT 💥

Intentionally introduce:

```text
❌ Wrong destination
❌ Wrong mask
❌ Wrong next hop
❌ Shutdown interface
❌ Missing return route
❌ Incorrect AD
❌ Incorrect host route
```

### Then FIX IT 🛠️

Do not wipe the configuration.

Find each fault using evidence.

> 🔥 **This is the moment you stop being a command copier and start behaving like a network engineer.**

---

# 🌟 Final Takeaway

Static routing looks simple:

```cisco
ip route ...
```

But the real skill is understanding what happens behind that command.

When you configure a route, you are answering four questions:

```text
🎯 WHERE?
Destination network

➡️ WHICH WAY?
Next hop / exit interface

⚖️ HOW PREFERRED?
Administrative distance

🔁 CAN THE REPLY RETURN?
Return path
```

Remember:

> **A good network engineer doesn't ask “What command should I type?” first. They ask “What path should this packet take, and why?”** 🧠🔥

---

## 📚 Module 21 Complete — Static Routing

### ➡️ Next: **22 — Dynamic Routing Concepts 🤖**

> **Next mission:** Let routers start exchanging routing information automatically — and prepare for OSPF. 🚀
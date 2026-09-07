# 🌍 34 — NAT (Network Address Translation)

> **Welcome to the Address Translation Gateway.** 🔄🌐
>
> Inside a private network, devices can use private IPv4 addresses. At the network boundary, NAT can translate addresses so traffic can communicate across networks such as the public Internet.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain NAT and why it is used
- 🔐 Distinguish private and public IPv4 addressing
- 🏷️ Understand inside/outside terminology
- 🧩 Explain inside local, inside global, outside local and outside global
- 🔵 Configure static NAT
- 🟢 Configure dynamic NAT
- 🟣 Configure PAT / NAT overload
- 🔎 Verify NAT translations
- 🐛 Troubleshoot common NAT problems
- 🧪 Build practical Cisco NAT labs
- 🏢 Understand enterprise Internet-edge NAT design
- 🎤 Answer CCNA NAT questions confidently

---

# 🧭 1. Where We Are

```text
32 📖 DNS
      ↓
33 🔐 ACL
      ↓
34 🌍 NAT  ← YOU ARE HERE
      ↓
35 📡 Wireless
      ↓
36 🛡️ Network Security
```

ACL asks:

> **“Should this traffic be allowed?”**

NAT asks:

> **“Should an address be translated as this traffic crosses the boundary?”**

They can be used together, but they solve different problems.

---

# 🧠 2. What Is NAT?

**NAT = Network Address Translation.**

NAT modifies IP addressing information as packets pass through a translation device.

A common use is translating private IPv4 addresses to a public IPv4 address or address pool.

```text
🏢 Private Network
192.168.10.10
       ↓
      NAT
       ↓
🌐 Public Network
203.0.113.x
```

---

# 🔥 3. Why Is NAT Used?

Private IPv4 address space is not globally routable on the public Internet.

Organizations can therefore use private addressing internally and translate traffic at an Internet edge.

NAT can provide:

- 🔄 Address translation
- 📦 Conservation of public IPv4 addresses
- 🏢 Flexible internal addressing
- 🌐 Internet connectivity for private hosts
- 🛡️ A boundary function when combined with appropriate security controls

📌 Important: NAT itself should not be treated as a complete security control or replacement for a firewall.

---

# 🏠 4. Private IPv4 Address Ranges

The major RFC 1918 private ranges are:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Examples:

```text
10.10.10.10
172.16.20.5
192.168.1.50
```

These addresses can be reused by different private networks.

---

# 🌍 5. Public IPv4 Addresses

A public IPv4 address is globally routable within the public Internet addressing system, subject to routing and provider policies.

Example documentation address:

```text
203.0.113.10
```

This belongs to a documentation range and is useful in labs and examples.

Do not use example documentation addresses as if they were production Internet allocations.

---

# 🧩 6. NAT Terminology

Cisco NAT terminology uses four important names.

```text
Inside Local
Inside Global
Outside Local
Outside Global
```

This looks confusing initially, so learn the viewpoint carefully.

---

# 🟢 7. Inside Local

The **inside local** address is the address representing an inside host on the inside network, commonly its private address before translation.

Example:

```text
PC = 192.168.10.10
```

That may be its inside-local address.

---

# 🔵 8. Inside Global

The **inside global** address is the address representing the inside host to the outside network after translation.

Example:

```text
Inside Local  → 192.168.10.10
Inside Global → 203.0.113.10
```

---

# 🟣 9. Outside Global

The **outside global** address is the address used to identify an outside host as it exists in the outside/global network.

Example:

```text
Web server = 198.51.100.20
```

---

# 🟠 10. Outside Local

The **outside local** address represents how the outside host is known from the inside network.

In many simple Internet-access examples, outside local and outside global may appear identical.

The terminology matters most when translating or describing both sides of a connection precisely.

---

# 🧠 11. Easy Terminology Trick

Think:

```text
LOCAL  → address as seen from the local side
GLOBAL → address as seen from the global/outside side

INSIDE → inside host
OUTSIDE → outside host
```

Combine them:

```text
Inside Local
Inside Global
Outside Local
Outside Global
```

Do not memorize them as “private/public” only. The terms describe addressing from the NAT device's perspective.

---

# 🔄 12. Static NAT

Static NAT creates a one-to-one mapping.

Example:

```text
Inside server
192.168.10.10
      ↕
203.0.113.10
Public representation
```

The mapping is predictable.

This is useful when an internal service needs a stable translated address.

---

# ⚙️ 13. Cisco Static NAT Configuration

Example:

```text
Inside server = 192.168.10.10
Public address = 203.0.113.10
```

Configure:

```cisco
ip nat inside source static 192.168.10.10 203.0.113.10
```

Mark interfaces:

```cisco
interface gigabitEthernet0/0
 ip nat inside

interface gigabitEthernet0/1
 ip nat outside
```

Verify:

```cisco
show ip nat translations
show ip nat statistics
```

📌 The interface names and addressing must match your actual topology.

---

# 🟢 14. Dynamic NAT

Dynamic NAT uses a pool of public addresses.

Conceptually:

```text
Private hosts
10.0.0.10 ─┐
10.0.0.11 ─┼── NAT ──► Public pool
10.0.0.12 ─┘           203.0.113.10–20
```

A private host receives a temporary mapping from the available public pool.

Unlike static NAT, the mapping is not necessarily permanent or fixed to one public address.

---

# ⚙️ 15. Dynamic NAT Configuration

Example public pool:

```text
203.0.113.10 – 203.0.113.20
```

Create the pool:

```cisco
ip nat pool PUBLIC-POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
```

Define which inside addresses may be translated using an ACL:

```cisco
access-list 1 permit 192.168.10.0 0.0.0.255
```

Bind the ACL to the pool:

```cisco
ip nat inside source list 1 pool PUBLIC-POOL
```

Mark interfaces:

```cisco
interface gigabitEthernet0/0
 ip nat inside

interface gigabitEthernet0/1
 ip nat outside
```

---

# 🟣 16. PAT — Port Address Translation

**PAT = Port Address Translation.**

It is commonly called:

```text
NAT overload
```

PAT allows many private hosts to share one public IPv4 address by distinguishing flows using transport-layer port information.

```text
192.168.10.10:50001 ─┐
192.168.10.11:50002 ─┼──► 203.0.113.10
192.168.10.12:50003 ─┘
```

The NAT device maintains state so return traffic can be mapped back to the correct internal host.

---

# 🧠 17. Why PAT Is So Powerful

Imagine:

```text
🏢 200 private users
🌐 1 public IPv4 address
```

With PAT, many simultaneous connections can share that public address because the translation tracks ports and connection state.

This became extremely important because IPv4 public address space is limited.

---

# ⚙️ 18. Cisco PAT — ACL + Interface

A common Cisco configuration uses an ACL to identify inside traffic and the outside interface's address for overload.

```cisco
access-list 1 permit 192.168.10.0 0.0.0.255
```

Then:

```cisco
ip nat inside source list 1 interface gigabitEthernet0/1 overload
```

Mark interfaces:

```cisco
interface gigabitEthernet0/0
 ip nat inside

interface gigabitEthernet0/1
 ip nat outside
```

This tells the router to translate matching inside traffic using the outside interface address and overload it using port information.

---

# 🔎 19. Verify NAT

Important commands:

```cisco
show ip nat translations
show ip nat statistics
show running-config | include ip nat
show access-lists
```

A useful troubleshooting sequence is:

```text
Private host generates traffic
        ↓
NAT ACL matches?
        ↓
Inside interface marked?
        ↓
Outside interface marked?
        ↓
Translation created?
        ↓
Route exists?
        ↓
Return traffic arrives?
        ↓
Translation reverses correctly?
```

---

# 🧪 20. Packet Tracer Lab — PAT

## 🎯 Objective

Allow a private LAN to reach a simulated external network using PAT.

Topology:

```text
💻 PC1 ─┐
💻 PC2 ─┼─ 🔀 SW ── 🛣️ R1 ── 🌐 R2/ISP ── 🖥️ Server
💻 PC3 ─┘
```

Example:

```text
LAN = 192.168.10.0/24
R1 inside = 192.168.10.1
R1 outside = 203.0.113.2
Server = 198.51.100.10
```

Configure:

```cisco
access-list 1 permit 192.168.10.0 0.0.0.255
ip nat inside source list 1 interface gigabitEthernet0/1 overload
```

Interfaces:

```cisco
interface g0/0
 ip nat inside

interface g0/1
 ip nat outside
```

Add the required IP addressing and routing for your lab topology.

---

# 🔎 21. PAT Verification Lab

Generate traffic from a client:

```text
PC → ping external server
```

Then inspect:

```cisco
show ip nat translations
```

You should see translation state after qualifying traffic has been generated.

Also check:

```cisco
show ip nat statistics
```

If there are no translations, work backward through:

```text
Client IP
↓
Gateway
↓
Route
↓
ACL match
↓
NAT inside/outside marking
↓
Translation
```

---

# 🧪 22. Packet Tracer Lab — Static NAT

## Objective

Publish an internal server through a fixed translated address.

Example:

```text
Server inside = 192.168.10.10
Translated = 203.0.113.10
```

Configuration:

```cisco
ip nat inside source static 192.168.10.10 203.0.113.10
```

Mark interfaces:

```cisco
interface g0/0
 ip nat inside

interface g0/1
 ip nat outside
```

Verify:

```cisco
show ip nat translations
```

---

# 🚨 23. NAT Troubleshooting

When NAT does not work:

```text
1️⃣ Is the source host correctly addressed?
        ↓
2️⃣ Is its default gateway correct?
        ↓
3️⃣ Can it reach the NAT router?
        ↓
4️⃣ Does a route to the destination exist?
        ↓
5️⃣ Is the inside interface marked `ip nat inside`?
        ↓
6️⃣ Is the outside interface marked `ip nat outside`?
        ↓
7️⃣ Does the NAT ACL match the source?
        ↓
8️⃣ Is the NAT rule correct?
        ↓
9️⃣ Is translation being created?
        ↓
🔟 Does return traffic have a valid path?
```

---

# 🐛 24. Common NAT Mistakes

### ❌ Inside/outside reversed

The NAT rule may exist, but interfaces are marked incorrectly.

### ❌ ACL does not match source

The translation policy never selects the packet.

### ❌ Missing `overload`

A configuration intended for PAT may instead require a different NAT behavior.

### ❌ No default route

NAT can translate an address, but it cannot replace routing.

### ❌ Return path missing

Translation is not enough if response traffic cannot return correctly.

### ❌ Testing before generating traffic

Dynamic NAT/PAT entries may not exist until qualifying traffic is generated.

---

# 🧠 25. NAT Does Not Replace Routing

This is critical.

```text
Routing → Where should the packet go?
NAT     → What address should represent it?
```

A NAT router still needs valid routing.

Example:

```text
PC
 ↓
NAT router
 ↓
❌ No route to destination
```

Translation alone cannot make that packet reach the destination.

---

# 🛡️ 26. NAT vs Firewall

Do not confuse them.

| Technology | Main purpose |
|---|---|
| NAT | Address/port translation |
| ACL | Permit/deny traffic according to rules |
| Firewall | Stateful/security policy enforcement and inspection |

They can work together.

```text
Traffic
  ↓
Firewall / security policy
  ↓
NAT
  ↓
Routing
  ↓
Internet
```

The exact order depends on platform and architecture.

---

# 🏢 27. Enterprise Internet Edge

A typical enterprise may look like:

```text
🏢 Users
   │
   ▼
🔀 Switches
   │
   ▼
🛡️ Firewall / Edge
   │
   ▼
🌍 ISP
   │
   ▼
🌐 Internet
```

NAT/PAT may translate private internal addresses toward an ISP-facing address.

Public-facing services may use dedicated NAT rules or provider-assigned addressing depending on architecture.

---

# 🎯 28. Scenario Challenge — NAT Detective

A company has:

```text
PC1 = 192.168.10.10
PC2 = 192.168.10.11
Gateway = 192.168.10.1
```

The NAT router has:

```text
Inside = G0/0
Outside = G0/1
```

PCs can ping the router but cannot reach the external server.

### Your task

Check in this order:

```text
Can PC reach gateway?          ✅
Can router reach server?       ?
Inside interface marked?       ?
Outside interface marked?      ?
NAT ACL matches?               ?
NAT translation created?       ?
Default route exists?          ?
Return route exists?           ?
```

Do not assume NAT is the problem until routing and interface status have been checked.

---

# 🎤 29. CCNA Interview Questions

### 🟢 Beginner

**Q1. What is NAT?**

Network Address Translation changes addressing information as traffic passes through a translation device.

**Q2. Why is NAT commonly used with IPv4?**

It allows private addressing internally and can reduce the need for large numbers of public IPv4 addresses.

**Q3. What is PAT?**

Port Address Translation allows multiple internal flows to share an address by distinguishing flows with transport-layer port information.

### 🟡 Intermediate

**Q4. Static NAT vs dynamic NAT?**

Static NAT provides a fixed one-to-one mapping; dynamic NAT allocates mappings from a configured public pool.

**Q5. What is NAT overload?**

Another common name for PAT.

**Q6. What are inside local and inside global?**

Inside local identifies the inside host from the local/inside perspective; inside global is the address representing that inside host to the outside network.

### 🔴 Troubleshooting

**Q7. Why might `show ip nat translations` be empty?**

No qualifying traffic may have been generated, the NAT policy may not match, interfaces may not be marked correctly, or routing/connectivity may be preventing the expected flow.

**Q8. Does NAT replace a default route?**

No. NAT translates addressing; routing still determines where packets are forwarded.

---

# ⚡ 30. Quick Revision

```text
NAT = Network Address Translation

Private IPv4:
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

Static NAT:
1 private ↔ 1 fixed translated address

Dynamic NAT:
Private addresses ↔ public pool

PAT / Overload:
Many flows → shared public IP
using port information

Terminology:
Inside Local
Inside Global
Outside Local
Outside Global

Interfaces:
ip nat inside
ip nat outside

Verify:
show ip nat translations
show ip nat statistics

NAT ≠ Routing
NAT ≠ Firewall
```

---

# 📋 31. NAT Cheat Sheet

```cisco
! Static NAT
ip nat inside source static 192.168.10.10 203.0.113.10

! Dynamic NAT pool
ip nat pool PUBLIC-POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
access-list 1 permit 192.168.10.0 0.0.0.255
ip nat inside source list 1 pool PUBLIC-POOL

! PAT / Overload
access-list 1 permit 192.168.10.0 0.0.0.255
ip nat inside source list 1 interface g0/1 overload

! Interface roles
interface g0/0
 ip nat inside

interface g0/1
 ip nat outside

! Verification
show ip nat translations
show ip nat statistics
show running-config | include ip nat
show access-lists
```

---

# 🏁 32. Final Takeaways

You should now be able to explain:

- Why NAT exists
- Private vs public IPv4 addressing
- Static NAT
- Dynamic NAT
- PAT / overload
- Inside/outside terminology
- Wildcard-based NAT selection
- `ip nat inside` / `ip nat outside`
- NAT verification
- NAT troubleshooting
- NAT vs ACL vs firewall
- Why NAT does not replace routing

The professional mental model is:

```text
🏢 PRIVATE HOST
      ↓
📍 SOURCE ADDRESS
      ↓
🛣️ ROUTING DECISION
      ↓
🔄 NAT TRANSLATION
      ↓
🌍 OUTSIDE NETWORK
      ↓
🔁 RETURN TRAFFIC
      ↓
🔄 REVERSE TRANSLATION
      ↓
🏢 INTERNAL HOST
```

---

# 🚀 Next Module

```text
34 🌍 NAT
      ↓
35 📡 Wireless
      ↓
36 🛡️ Network Security
      ↓
37 🛠️ Network Management
```

**Next stop: 📡 Wireless Networking — WLAN architecture, RF basics, SSIDs, channels and wireless security.**

# 🌐 31 — DHCP Fundamentals & Cisco Configuration

> **Welcome to the IP Addressing Help Desk.** 🧑‍💻📬🌐
>
> A device joins a network and immediately asks:
>
> **“Who can give me an IP address?”**
>
> DHCP answers that question automatically.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain what DHCP is and why it is used
- 📦 Understand the DHCP DORA process
- 🔢 Understand IP address leases
- 🌐 Identify the main DHCP options
- 🖥️ Configure a Cisco router as a DHCP server
- 🚫 Exclude addresses from a DHCP pool
- 🏷️ Configure default gateway and DNS options
- 🔁 Understand DHCP renewal
- 🌉 Understand DHCP relay at a practical level
- 🔎 Verify DHCP operation on Cisco IOS
- 🐛 Troubleshoot common DHCP failures
- 🧪 Build a complete Packet Tracer DHCP lab
- 🏢 Connect DHCP behavior to real enterprise networks
- 🎤 Answer CCNA interview questions confidently

---

# 🧭 1. Where We Are

```text
23 🧠 OSPF Fundamentals
        ↓
24 🤝 OSPF Adjacencies
        ↓
25 📢 OSPF LSAs + LSDB
        ↓
26 🌳 SPF + Areas
        ↓
27 ⚙️ OSPF Configuration
        ↓
28 🐛 OSPF Troubleshooting
        ↓
29 🌐 OSPF Design
        ↓
30 📦 OSPF Advanced Operations
        ↓
31 📨 DHCP  ← YOU ARE HERE
```

We now move from **routing control** to **host addressing and network services**.

---

# 🧠 2. What Is DHCP?

**DHCP = Dynamic Host Configuration Protocol.**

It allows a network device to automatically receive network configuration information instead of requiring an administrator to configure every host manually.

A DHCP server can provide information such as:

```text
📍 IP address
🧮 Subnet mask / prefix information
🛣️ Default gateway
📖 DNS server
⏳ Lease duration
```

Without DHCP, an administrator might have to manually configure every workstation.

With DHCP:

```text
💻 Laptop joins network
        ↓
📡 DHCP request
        ↓
🖥️ DHCP server
        ↓
📦 Address + network settings
        ↓
💻 Laptop configures itself
```

---

# 🌍 3. Real-World Analogy — Hotel Reception 🏨

Imagine checking into a hotel.

You do not walk around trying random room numbers.

You go to reception:

```text
🧳 You
  ↓
🏨 Reception
  ↓
🔑 Room assignment
```

DHCP works in a similar way.

```text
💻 Client
  ↓
📨 DHCP request process
  ↓
🖥️ DHCP Server
  ↓
📍 IP assignment
```

The server effectively says:

> “Here is an address you may use, along with the network information you need.”

---

# 🔥 4. Why Is DHCP Needed?

Imagine a company with:

```text
🏢 500 employees
💻 500 laptops
📱 200 phones
🖨️ 30 printers
📹 50 cameras
```

Manually assigning an IP configuration to every dynamic endpoint would create unnecessary administrative work.

DHCP provides:

✅ Automation
✅ Centralized address management
✅ Reduced configuration errors
✅ Easier onboarding
✅ Lease-based reuse of addresses
✅ Scalable host addressing

---

# 🧩 5. DHCP Participants

The two most important roles are:

```text
💻 DHCP Client
🖥️ DHCP Server
```

### DHCP Client

The client is the device that needs configuration.

Examples:

- 💻 Laptop
- 🖥️ Desktop
- 📱 Smartphone
- 🖨️ Printer
- 📺 Smart device

### DHCP Server

The server manages and leases addresses.

It may be:

- 🔵 Cisco router
- 🖥️ Windows Server
- 🐧 Linux server
- ☁️ Cloud DHCP service
- 🌐 Dedicated network appliance

---

# 📨 6. DHCP Uses UDP

DHCP commonly uses UDP.

```text
Server port = UDP 67
Client port = UDP 68
```

Memory trick:

```text
🖥️ Server → 67
💻 Client → 68
```

A client that does not yet have a usable IP configuration must rely on local/broadcast communication for the initial discovery process.

---

# 🚀 7. The Famous DHCP DORA Process

One of the most important CCNA concepts.

```text
D → Discover
O → Offer
R → Request
A → Acknowledgment
```

The sequence is:

```text
💻 CLIENT
   │
   │ 1️⃣ DHCP DISCOVER
   ▼
📢 DHCP SERVER(S)
   │
   │ 2️⃣ DHCP OFFER
   ▼
💻 CLIENT
   │
   │ 3️⃣ DHCP REQUEST
   ▼
📢 DHCP SERVER
   │
   │ 4️⃣ DHCP ACK
   ▼
💻 CLIENT ✅
```

---

# 1️⃣ 8. DHCP Discover

The client does not know which DHCP server is available.

So it starts with a **DHCP Discover**.

Conceptually:

> “Is there any DHCP server out there?” 📣

```text
💻 Client
   │
   │ DHCP DISCOVER
   ▼
📢 Local network
```

At this stage, the client is trying to discover a DHCP server.

---

# 2️⃣ 9. DHCP Offer

A DHCP server receives the Discover and may respond with an offer.

The offer can contain information such as:

```text
📍 Proposed IP address
🧮 Subnet information
🛣️ Default gateway
📖 DNS server
⏳ Lease information
```

Conceptually:

> “I can provide you this address.”

```text
📢 DHCP Server
      │
      │ DHCP OFFER
      ▼
💻 Client
```

---

# 3️⃣ 10. DHCP Request

The client chooses an offer and sends a DHCP Request.

Conceptually:

> “I want to use this offered address.”

```text
💻 Client
      │
      │ DHCP REQUEST
      ▼
📢 DHCP Server
```

The request also allows the client to indicate which offer it selected.

---

# 4️⃣ 11. DHCP Acknowledgment

The server confirms the assignment with a DHCP ACK.

```text
📢 DHCP Server
      │
      │ DHCP ACK
      ▼
💻 Client ✅
```

Now the client can use the configuration subject to the lease and relevant validation rules.

---

# 🧠 12. DORA Memory Trick

Think:

```text
D = “Discover me!” 👀
O = “Offer available.” 🎁
R = “Request that one.” 🙋
A = “Acknowledged.” ✅
```

### Exam memory

```text
DISCOVER → OFFER → REQUEST → ACK
```

Never reverse the middle two steps.

---

# 🔎 13. Simple DORA Example

Suppose a client needs an address from:

```text
192.168.10.0/24
```

The available DHCP scope includes:

```text
192.168.10.100
192.168.10.101
192.168.10.102
192.168.10.103
...
```

Possible exchange:

```text
Client:
“Any DHCP server?”
        ↓
Server:
“I offer 192.168.10.100.”
        ↓
Client:
“I request 192.168.10.100.”
        ↓
Server:
“ACK. Use it.”
```

---

# 🧮 14. DHCP Lease

DHCP does not normally mean:

> “This IP belongs to this device forever.”

Instead, the address is generally assigned for a **lease period**.

Think of it like renting a parking space. 🚗

```text
🅿️ Parking space
      ↓
⏳ Reserved for a period
      ↓
🔄 Renewed or returned
```

This helps the server reuse addresses when clients leave the network.

---

# 🔄 15. DHCP Renewal

A client does not necessarily wait until its lease completely expires.

It attempts to renew the lease during the lease lifecycle.

Conceptually:

```text
Lease starts
    ↓
⏳ Time passes
    ↓
🔄 Renewal attempt
    ↓
✅ Lease extended
```

The exact timers and message behavior depend on the DHCP implementation and lease configuration.

---

# 🧠 16. Why Leases Are Useful

Suppose an office has:

```text
100 available addresses
300 devices over a day
```

Not every device is online simultaneously.

With leases, addresses can be recycled when devices no longer need them.

This makes dynamic addressing far more efficient than permanently dedicating an address to every temporary endpoint.

---

# 🎛️ 17. Important DHCP Configuration Parameters

A DHCP scope/pool commonly needs:

| Parameter | Purpose |
|---|---|
| Network/prefix | Defines the address range |
| Default gateway | Tells clients where to send off-subnet traffic |
| DNS server | Provides name-resolution server information |
| Lease | Controls how long the address is assigned |
| Exclusions | Prevents selected addresses from being leased |

---

# 🚫 18. DHCP Excluded Addresses

Sometimes certain addresses must remain manually controlled.

Example:

```text
Router = 192.168.10.1
Server = 192.168.10.10
Printer = 192.168.10.20
```

You do not want DHCP to randomly hand those addresses to laptops.

So exclude them.

Cisco IOS example:

```cisco
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.10.10
ip dhcp excluded-address 192.168.10.20
```

You can also exclude ranges where appropriate.

---

# 🔵 19. Cisco IOS as a DHCP Server

A Cisco router can provide DHCP services for connected networks.

Basic design:

```text
                    🌐
                    │
              ┌──────────┐
              │  Router  │
              │  DHCP    │
              │  Server  │
              └────┬─────┘
                   │
                   │
             ┌─────┴─────┐
             │   Switch  │
             └─┬────┬────┘
               │    │
              💻    💻
```

---

# ⚙️ 20. Complete Cisco DHCP Configuration

Example network:

```text
Network:       192.168.10.0/24
Gateway:       192.168.10.1
DHCP range:    192.168.10.100 - 192.168.10.254
DNS server:    8.8.8.8
```

### Step 1 — Configure the router interface

```cisco
Router(config)# interface gigabitEthernet0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
```

### Step 2 — Exclude reserved addresses

```cisco
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.99
```

### Step 3 — Create the DHCP pool

```cisco
Router(config)# ip dhcp pool LAN-POOL
```

### Step 4 — Define the network

```cisco
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
```

### Step 5 — Define the default gateway

```cisco
Router(dhcp-config)# default-router 192.168.10.1
```

### Step 6 — Define the DNS server

```cisco
Router(dhcp-config)# dns-server 8.8.8.8
```

The result is:

```text
💻 Client
   ↓
DHCP
   ↓
192.168.10.100+
   ↓
Gateway = 192.168.10.1
DNS = 8.8.8.8
```

---

# 🔎 21. Verify DHCP Configuration

Useful Cisco commands include:

```cisco
show ip dhcp pool
show ip dhcp binding
show ip dhcp conflict
show running-config | section dhcp
```

### `show ip dhcp pool`

Shows DHCP pool information, including utilization.

### `show ip dhcp binding`

Shows active DHCP bindings.

Example style of output:

```text
Bindings from all pools not associated with VRF:

IP address       Client-ID/              Lease expiration
                 Hardware address
192.168.10.100   0100.xxxx.xxxx.xxxx     --
```

### `show ip dhcp conflict`

Shows detected address conflicts.

---

# 💻 22. Verify the Client

On a Windows client:

```powershell
ipconfig /all
```

You should inspect:

```text
IPv4 Address
Subnet Mask
Default Gateway
DHCP Server
DNS Servers
```

On Linux, useful commands include:

```bash
ip addr
ip route
resolvectl status
```

---

# 🧪 23. Packet Tracer Lab

## 🎯 Objective

Configure a Cisco router as a DHCP server and automatically assign IPv4 settings to two PCs.

## 🗺️ Topology

```text
       💻 PC1
          │
          │
          ▼
     ┌─────────┐
     │ Switch  │
     └────┬────┘
          │
          │
          ▼
      ┌────────┐
      │ Router │
      │ DHCP   │
      └────────┘
          ▲
          │
       💻 PC2
```

## 📊 Addressing Plan

| Device | Interface | Addressing |
|---|---|---|
| Router | G0/0 | 192.168.10.1/24 |
| PC1 | NIC | DHCP |
| PC2 | NIC | DHCP |
| DNS | Logical option | 8.8.8.8 |

## ⚙️ Router Configuration

```cisco
enable
configure terminal

interface gigabitEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

ip dhcp excluded-address 192.168.10.1 192.168.10.99

ip dhcp pool LAN-POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
exit

end
write memory
```

## 💻 PC Configuration

In Packet Tracer:

```text
PC → Desktop → IP Configuration → DHCP
```

The PC should receive an address from the pool.

---

# 🔎 24. Lab Verification Checklist

On the router:

```cisco
show ip interface brief
show ip dhcp pool
show ip dhcp binding
```

On each PC, verify:

```text
IPv4 address ✅
Subnet mask ✅
Default gateway ✅
DHCP server ✅
DNS server ✅
```

Then test:

```text
PC1 → ping 192.168.10.1
PC2 → ping 192.168.10.1
PC1 → ping PC2
```

---

# 🚨 25. Troubleshooting DHCP Step-by-Step

Use a structured workflow.

```text
Client has no IP
      ↓
Check physical link
      ↓
Check VLAN
      ↓
Check interface state
      ↓
Check DHCP pool
      ↓
Check excluded addresses
      ↓
Check bindings/conflicts
      ↓
Check relay path if remote
      ↓
Renew and test again
```

---

# 🐛 26. Problem — PC Gets 169.254.x.x

A Windows host may self-assign an APIPA address when DHCP configuration fails.

Example:

```text
169.254.x.x
```

Do not immediately assume the router is broken.

Check:

```text
🔌 Cable/link
🔀 Switch port/VLAN
🔵 Router interface
📦 DHCP pool
🚫 Exclusions
🧭 DHCP relay if applicable
```

---

# 🐛 27. Problem — DHCP Pool Has No Free Addresses

Check:

```cisco
show ip dhcp pool
show ip dhcp binding
```

Possible causes:

```text
Too many clients
Long lease durations
Wrong subnet sizing
Stale bindings
Insufficient address space
```

Fix the root cause rather than simply deleting bindings blindly.

---

# 🐛 28. Problem — Client Gets Wrong Gateway

Example:

```text
Expected: 192.168.10.1
Received: 192.168.20.1
```

Inspect the DHCP pool.

```cisco
show running-config | section dhcp
```

Look for:

```cisco
default-router
```

Also verify that the client is on the intended VLAN/subnet.

---

# 🐛 29. Problem — DHCP Works in One VLAN but Not Another

This is a classic topology question.

If the DHCP server is not inside the client VLAN, the DHCP request must be forwarded appropriately.

Typical enterprise design:

```text
VLAN 10 Client
      │
      ▼
🏢 Layer 3 Gateway
      │
      │ DHCP Relay
      ▼
🖥️ Central DHCP Server
```

Cisco commonly uses:

```cisco
ip helper-address <DHCP-SERVER-IP>
```

The command is configured on the Layer 3 interface that receives client broadcasts.

Example:

```cisco
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.50.10
```

---

# 🌉 30. DHCP Relay — Why Is It Needed?

Routers do not normally forward broadcasts across Layer 3 boundaries in the same way a switch floods them within a broadcast domain.

Imagine:

```text
💻 Client VLAN 10
       │
       ▼
🛣️ Router / L3 Switch
       │
       ▼
🖥️ DHCP Server VLAN 50
```

The client's DHCP discovery must reach the server somehow.

DHCP relay performs that forwarding function.

Conceptually:

```text
Client Broadcast
      ↓
Gateway / Relay
      ↓
Unicast-style forwarding to DHCP Server
```

---

# ⚠️ 31. DHCP Relay vs DHCP Server

Do not confuse these.

| Feature | DHCP Server | DHCP Relay |
|---|---|---|
| Assigns leases | ✅ | ❌ |
| Maintains pool | ✅ | ❌ |
| Forwards DHCP requests | Not its main role | ✅ |
| Common location | Server/router/appliance | Router/L3 switch |
| Cisco command | `ip dhcp pool` | `ip helper-address` |

---

# 🧠 32. DHCP Scope Mental Model

Think of a DHCP pool as a box of numbered tickets. 🎟️

```text
POOL
┌──────────────────────────────┐
│ 100  101  102  103  104 ... │
└──────────────────────────────┘
```

Some numbers may be permanently reserved:

```text
🚫 1     Gateway
🚫 10    Server
🚫 20    Printer
✅ 100+  Dynamic clients
```

This mental model makes exclusions easy to remember.

---

# 🔐 33. Security Consideration — Rogue DHCP

DHCP is also a security concern.

Imagine an attacker connects a rogue DHCP server:

```text
💻 Clients
   │
   ├──────────────► ✅ Legit DHCP
   │
   └──────────────► ⚠️ Rogue DHCP
```

A rogue server might attempt to provide malicious gateway or DNS information.

On switched networks, **DHCP Snooping** is a security mechanism used to help control trusted DHCP behavior.

That topic belongs to the Network Security section, but remember the relationship:

```text
DHCP
  ↓
Switch Security
  ↓
DHCP Snooping
```

---

# 🌍 34. Enterprise Scenario

A company has:

```text
VLAN 10 = HR
VLAN 20 = Finance
VLAN 30 = IT
VLAN 40 = Guest
```

The company wants centralized DHCP.

Possible architecture:

```text
                     🖥️ DHCP Server
                           │
                           │
                    ┌──────┴──────┐
                    │ L3 Core     │
                    └──┬───┬───┬──┘
                       │   │   │
                    VLAN10 VLAN20 VLAN30
                     HR   Finance  IT
```

Each SVI can use a relay address to the central DHCP server.

Benefits:

✅ Centralized control
✅ Easy scope management
✅ Less manual configuration
✅ Better scalability

---

# 🎯 35. Scenario Challenge — You Solve It

A company has:

```text
Network: 10.10.50.0/24
Gateway: 10.10.50.1
Server: 10.10.50.10
Printer: 10.10.50.20
```

There are 60 employee laptops.

Requirements:

```text
🚫 Do not allocate .1
🚫 Do not allocate .10
🚫 Do not allocate .20
✅ Clients should receive addresses automatically
✅ Gateway must be .1
✅ DNS must be 8.8.8.8
```

### Your task

Write the Cisco DHCP configuration without looking at the solution first.

Expected dynamic range can begin at:

```text
10.10.50.21
```

or another suitable range that avoids reserved addresses.

---

# ✅ 36. Challenge Solution

One valid implementation is:

```cisco
ip dhcp excluded-address 10.10.50.1
ip dhcp excluded-address 10.10.50.10
ip dhcp excluded-address 10.10.50.20

ip dhcp pool EMPLOYEE-LAN
 network 10.10.50.0 255.255.255.0
 default-router 10.10.50.1
 dns-server 8.8.8.8
```

A more conservative design might exclude a larger reserved block for future infrastructure devices.

The key principle is:

> **Reserve addresses intentionally; let DHCP own only the addresses meant for dynamic clients.**

---

# ⚠️ 37. Common Mistakes

### ❌ Mistake 1 — Forgetting `no shutdown`

The router interface may remain administratively down.

### ❌ Mistake 2 — Wrong network statement

Example:

```cisco
network 192.168.20.0 255.255.255.0
```

while the interface is actually:

```text
192.168.10.1/24
```

### ❌ Mistake 3 — Forgetting exclusions

Infrastructure devices may receive addresses intended for static use.

### ❌ Mistake 4 — Wrong VLAN

DHCP cannot fix a broken Layer 2 path.

### ❌ Mistake 5 — Forgetting relay on remote VLANs

A centralized DHCP server requires proper Layer 3 forwarding/relay from remote client networks.

### ❌ Mistake 6 — Confusing DHCP with DNS

DHCP gives configuration.

DNS resolves names.

They are different services.

---

# 🧪 38. Packet Tracer Mini Challenges

## Challenge A — Basic DHCP

Create:

```text
1 Router
1 Switch
3 PCs
```

Configure one DHCP pool.

Goal:

```text
All PCs receive valid addresses.
```

---

## Challenge B — Reserved Infrastructure

Add:

```text
1 Server = 192.168.20.10
1 Printer = 192.168.20.20
```

Make sure DHCP never leases those addresses.

---

## Challenge C — Two VLANs

Create:

```text
VLAN 10 = 192.168.10.0/24
VLAN 20 = 192.168.20.0/24
```

Use Layer 3 routing and provide DHCP service for both networks.

---

# 🎤 39. Interview Questions

## 🟢 Beginner

### Q1. What is DHCP?

A protocol used to dynamically provide hosts with IP configuration information.

### Q2. What does DORA stand for?

```text
Discover
Offer
Request
Acknowledgment
```

### Q3. Which UDP ports are associated with DHCP?

```text
Server = UDP 67
Client = UDP 68
```

### Q4. Why does DHCP use a lease?

To temporarily assign addresses so they can later be renewed or reused.

---

## 🟡 Intermediate

### Q5. Why exclude addresses from a DHCP pool?

To reserve them for infrastructure or other statically addressed devices.

### Q6. What does `default-router` do in a Cisco DHCP pool?

It tells clients which default gateway to use.

### Q7. What does `dns-server` do?

It tells clients which DNS server address to use.

### Q8. Why is DHCP relay required?

To allow DHCP requests from a client network to reach a DHCP server across a Layer 3 boundary.

---

## 🔴 Advanced / Scenario

### Q9. Clients in VLAN 20 cannot obtain an address, but VLAN 10 works. What do you check?

Check:

```text
VLAN assignment
SVI/subinterface
IP helper configuration
DHCP pool
Routing
Interface status
ACL/security filtering
```

### Q10. Clients receive the wrong gateway. What is a likely cause?

Incorrect DHCP pool configuration or clients being placed in the wrong subnet/VLAN.

### Q11. Can a Cisco router act as both a DHCP server and a DHCP relay for different interfaces?

Yes, depending on the network design and configuration. Different interfaces can participate in different roles.

---

# ⚡ 40. Quick Revision

```text
📨 DHCP
= Dynamic Host Configuration Protocol

🔢 UDP
Server = 67
Client = 68

🚀 DORA
Discover
Offer
Request
ACK

📦 DHCP can provide
IP address
Subnet information
Default gateway
DNS server
Lease information

🔵 Cisco DHCP server
ip dhcp excluded-address ...
ip dhcp pool NAME
 network X.X.X.X MASK
 default-router X.X.X.X
 dns-server X.X.X.X

🔎 Verification
show ip dhcp pool
show ip dhcp binding
show ip dhcp conflict
show running-config | section dhcp

🌉 Relay
ip helper-address X.X.X.X
```

---

# 📋 41. DHCP Cheat Sheet

| Task | Cisco IOS Command |
|---|---|
| Exclude one address | `ip dhcp excluded-address A.B.C.D` |
| Exclude range | `ip dhcp excluded-address START END` |
| Create pool | `ip dhcp pool NAME` |
| Define network | `network A.B.C.D MASK` |
| Set gateway | `default-router A.B.C.D` |
| Set DNS | `dns-server A.B.C.D` |
| View pools | `show ip dhcp pool` |
| View bindings | `show ip dhcp binding` |
| View conflicts | `show ip dhcp conflict` |
| View DHCP config | `show running-config \| section dhcp` |
| Configure relay | `ip helper-address A.B.C.D` |

---

# 🧠 42. Final Mental Model

When a new laptop connects:

```text
💻 “I need network settings.”
          │
          ▼
📨 DISCOVER
          │
          ▼
🖥️ DHCP SERVER
          │
          ▼
🎁 OFFER
          │
          ▼
💻 REQUEST
          │
          ▼
✅ ACK
          │
          ▼
🌐 Client joins the network
```

And the whole process rests on one simple idea:

> **DHCP automates host network configuration so administrators do not have to manually configure every dynamic endpoint.**

---

# 🏁 43. Knowledge Check

Before moving forward, make sure you can answer these without notes:

- What problem does DHCP solve?
- What does DORA mean?
- Which UDP ports are used?
- What is a DHCP lease?
- Why are addresses excluded?
- How do you configure a Cisco DHCP pool?
- Which command displays DHCP bindings?
- Why is `ip helper-address` required in some designs?
- What is the difference between a DHCP server and relay?
- How would you troubleshoot a client that receives 169.254.x.x?

If you can explain those concepts **and configure the Packet Tracer lab without copying commands**, you have the foundation required for the next networking service topics. 🚀

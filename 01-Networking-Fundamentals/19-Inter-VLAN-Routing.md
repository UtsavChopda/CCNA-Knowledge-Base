# 🔀 19 — Inter-VLAN Routing

> **From isolated VLANs to a real routed network 🚀**
>
> VLANs give us separation. **Inter-VLAN Routing gives us controlled communication between those separate networks.**

---

## 🧭 Where We Are in the Journey

```text
Networking Basics
      ↓
Ethernet + MAC
      ↓
Switching
      ↓
VLANs 🟦🟩🟨
      ↓
Trunking 🔗
      ↓
VTP / STP / EtherChannel
      ↓
👉 INTER-VLAN ROUTING  ← YOU ARE HERE
      ↓
Routing Fundamentals
      ↓
OSPF → DHCP → DNS → ACL → NAT → ...
```

### 🎯 The big question

> **If VLAN 10 and VLAN 20 are different Layer 2 broadcast domains, how can a PC in VLAN 10 communicate with a PC in VLAN 20?**

Answer: **Layer 3 routing.** 🧠

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- ✅ Explain why inter-VLAN routing is required
- ✅ Distinguish VLANs, subnets, and default gateways
- ✅ Explain Layer 2 vs Layer 3 communication
- ✅ Understand Router-on-a-Stick (ROAS)
- ✅ Configure router subinterfaces with 802.1Q
- ✅ Understand and configure SVIs on multilayer switches
- ✅ Understand routed ports and `no switchport`
- ✅ Configure multiple VLAN gateways
- ✅ Verify routing using Cisco IOS commands
- ✅ Troubleshoot VLAN, trunk, gateway and SVI problems
- ✅ Build multiple inter-VLAN routing topologies in Packet Tracer
- ✅ Understand the relationship between MAC tables, ARP tables and routing tables
- ✅ Prepare for enterprise campus routing concepts

---

# 🧠 1. The Problem: VLANs Create Separation

Imagine a college network:

```text
🟦 VLAN 10 — Students
🟩 VLAN 20 — Faculty
🟨 VLAN 30 — Admin
🟥 VLAN 40 — Servers
```

A Layer 2 switch keeps these VLANs separate.

```text
             SWITCH
        ┌─────────────────┐
        │ VLAN 10 🟦      │── PC-A
        │                 │
        │ VLAN 20 🟩      │── PC-B
        │                 │
        │ VLAN 30 🟨      │── PC-C
        └─────────────────┘

       🚫 L2 does NOT route between VLANs
```

If PC-A in VLAN 10 wants to communicate with PC-B in VLAN 20, something must perform **Layer 3 routing**.

That device can be:

- 🛜 A router
- 🔀 A multilayer (Layer 3) switch

---

# 🏠 2. Easy Analogy — Apartment Buildings

Think of every VLAN as a separate apartment building.

```text
🏢 Building A = VLAN 10
🏢 Building B = VLAN 20
🏢 Building C = VLAN 30
```

People inside Building A can move around their own building without leaving it.

But to visit Building B, they need a controlled entrance/road between buildings.

That controlled road is the **Layer 3 gateway/router**. 🚦

```text
🟦 VLAN 10
     │
     │ gateway
     ▼
   🚦 ROUTER / L3 SWITCH
     │
     ├──────────► 🟩 VLAN 20
     │
     └──────────► 🟨 VLAN 30
```

---

# 🔍 3. VLAN vs Subnet vs Default Gateway

These three concepts are extremely important.

| Concept | Main job |
|---|---|
| 🟦 VLAN | Layer 2 logical separation |
| 🌐 IP subnet | Layer 3 logical network |
| 🚪 Default gateway | Layer 3 exit from the local subnet |
| 🔀 Router/L3 switch | Routes packets between networks |

A common design is:

| VLAN | Purpose | Subnet | Gateway |
|---|---|---|---|
| 10 | Students | `192.168.10.0/24` | `192.168.10.1` |
| 20 | Faculty | `192.168.20.0/24` | `192.168.20.1` |
| 30 | Admin | `192.168.30.0/24` | `192.168.30.1` |

⚠️ **VLAN and subnet are not technically the same thing**, although enterprise designs commonly map one VLAN to one IPv4 subnet.

---

# 🧩 4. Why Does a Host Need a Default Gateway?

Suppose:

```text
PC-A
IP:      192.168.10.10
Mask:    255.255.255.0
Gateway: 192.168.10.1
```

Destination:

```text
192.168.20.20
```

PC-A sees that `192.168.20.20` is outside its local `/24` network.

So it sends the packet to its **default gateway**.

```text
PC-A
192.168.10.10
     │
     │ destination is remote
     ▼
🚪 192.168.10.1
     │
     ▼
🔀 Layer 3 routing
     │
     ▼
🟩 VLAN 20
     │
     ▼
PC-B
192.168.20.20
```

---

# 🧠 5. Layer 2 vs Layer 3 Communication

### Same VLAN / same subnet

Usually no router is required.

```text
PC-A 🟦 ── SWITCH ── PC-B 🟦
```

The switch forwards based on MAC addresses.

### Different VLAN / different subnet

A Layer 3 device is required.

```text
PC-A 🟦
   │
   ▼
SWITCH
   │ trunk/access design
   ▼
ROUTER / L3 SWITCH 🔀
   │
   ▼
PC-B 🟩
```

---

# 🔄 6. What Actually Happens During Inter-VLAN Communication?

Example:

```text
PC-A: 192.168.10.10/24
Gateway: 192.168.10.1

PC-B: 192.168.20.20/24
Gateway: 192.168.20.1
```

PC-A wants to reach PC-B.

### Step 1 — Determine local or remote

PC-A checks the destination against its subnet mask.

`192.168.20.20` is not in `192.168.10.0/24`.

### Step 2 — Find the gateway MAC

PC-A uses ARP to discover the MAC address of `192.168.10.1`.

### Step 3 — Build an Ethernet frame

```text
Destination MAC = Gateway MAC
Source MAC      = PC-A MAC
```

### Step 4 — Switch forwards the frame

The switch knows the frame belongs to VLAN 10.

### Step 5 — Layer 3 device receives it

The router/L3 switch removes the incoming Layer 2 framing and examines the IP packet.

### Step 6 — Routing decision

The device checks its routing table.

```text
192.168.10.0/24 → connected
192.168.20.0/24 → connected
```

### Step 7 — New Layer 2 frame

The router/L3 switch creates a new Ethernet frame toward PC-B.

```text
Destination MAC = PC-B MAC
Source MAC      = Router/L3 interface MAC
```

### Step 8 — PC-B receives it 🎯

```text
PC-A 🟦
  ↓
Switch
  ↓
Router/L3 Switch 🔀
  ↓
Switch
  ↓
PC-B 🟩
```

💡 **Key idea:** The IP packet is routed, while the Ethernet frame is rewritten at the Layer 3 boundary.

---

# 🚦 7. Inter-VLAN Routing Methods

There are several approaches.

| Method | Device | Typical use |
|---|---|---|
| Router-on-a-Stick | Router | Small/medium labs and networks |
| SVI routing | Multilayer switch | Enterprise campus networks |
| Routed ports | L3 switch/router | Point-to-point Layer 3 links |
| Legacy one-arm routing | Router | Historical/learning concept |

We will focus heavily on **ROAS + SVI**, because these are foundational CCNA skills.

---

# 🍢 8. Router-on-a-Stick (ROAS)

The funny name has a simple meaning.

One physical router interface carries traffic for **multiple VLANs** using multiple logical subinterfaces.

```text
                 ROUTER
          ┌─────────────────┐
          │ G0/0            │
          │  ├─ G0/0.10 🟦  │
          │  ├─ G0/0.20 🟩  │
          │  └─ G0/0.30 🟨  │
          └────────┬────────┘
                   │
                TRUNK 🔗
                   │
              ┌────▼─────┐
              │  SWITCH  │
              └─┬──┬──┬──┘
                │  │  │
               🟦 🟩 🟨
```

The single physical router link is like one stick carrying multiple VLANs. 🍢

---

# 🏷️ 9. ROAS and 802.1Q Tagging

The switch sends VLAN traffic over the trunk.

For example:

```text
VLAN 10 → 802.1Q tag 10
VLAN 20 → 802.1Q tag 20
VLAN 30 → 802.1Q tag 30
```

The router subinterface identifies the VLAN.

```text
G0/0.10 → VLAN 10
G0/0.20 → VLAN 20
G0/0.30 → VLAN 30
```

Example:

```cisco
interface g0/0.10
 encapsulation dot1q 10
 ip address 192.168.10.1 255.255.255.0
```

---

# 🛠️ 10. ROAS Configuration — Complete Example

## 🎯 Topology

```text
 PC-A 🟦 VLAN 10                 PC-B 🟩 VLAN 20
192.168.10.10                    192.168.20.10
      │                                  │
      └──────────┐          ┌────────────┘
                 ▼          ▼
               ┌──────────────┐
               │   SWITCH     │
               │              │
               │ Fa/Gi ports  │
               └──────┬───────┘
                      │
                   TRUNK 🔗
                      │
                 ┌────▼─────┐
                 │  ROUTER  │
                 │   G0/0   │
                 └──────────┘
```

## 📋 Addressing Table

| Device | Interface | VLAN | IP | Gateway |
|---|---|---:|---|---|
| PC-A | NIC | 10 | `192.168.10.10/24` | `192.168.10.1` |
| PC-B | NIC | 20 | `192.168.20.10/24` | `192.168.20.1` |
| Router | G0/0.10 | 10 | `192.168.10.1/24` | — |
| Router | G0/0.20 | 20 | `192.168.20.1/24` | — |

## 🟦 Step 1 — Create VLANs

```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name STUDENTS
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name FACULTY
Switch(config-vlan)# exit
```

## 🔌 Step 2 — Configure access ports

```cisco
Switch(config)# interface f0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface f0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit
```

## 🔗 Step 3 — Configure switch-to-router trunk

```cisco
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

> 💡 On some older Cisco switch platforms, you may need to select the trunk encapsulation before `switchport mode trunk`; on many modern models, 802.1Q is already the only supported option.

## 🚦 Step 4 — Enable the physical router interface

```cisco
Router(config)# interface g0/0
Router(config-if)# no shutdown
Router(config-if)# exit
```

Do **not** normally put the user VLAN IP directly on the physical interface when using ROAS subinterfaces.

## 🟦 Step 5 — Create VLAN 10 subinterface

```cisco
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# no shutdown
```

## 🟩 Step 6 — Create VLAN 20 subinterface

```cisco
Router(config)# interface g0/0.20
Router(config-subif)# encapsulation dot1q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# no shutdown
```

## 💻 Step 7 — Configure PCs

PC-A:

```text
IP:      192.168.10.10
Mask:    255.255.255.0
Gateway: 192.168.10.1
```

PC-B:

```text
IP:      192.168.20.10
Mask:    255.255.255.0
Gateway: 192.168.20.1
```

## 🧪 Step 8 — Test

From PC-A:

```text
ping 192.168.20.10
```

Expected:

```text
Reply from 192.168.20.10
```

🎉 You have completed your first Inter-VLAN routing path!

---

# 🔎 11. ROAS Verification Commands

### Switch

```cisco
show vlan brief
show interfaces trunk
show interfaces g0/1 switchport
show mac address-table
show running-config
```

### Router

```cisco
show ip interface brief
show interfaces g0/0.10
show interfaces g0/0.20
show ip route
show arp
show running-config
```

### What you want to see

```text
G0/0.10   192.168.10.1   up   up
G0/0.20   192.168.20.1   up   up
```

And:

```text
C 192.168.10.0/24 is directly connected
C 192.168.20.0/24 is directly connected
```

---

# 🧠 12. Understanding `up/up`

Interface status is usually shown as:

```text
Status     Protocol
   ↑          ↑
  up         up
```

### `up/up` 🟢
Physical/link and line protocol are operational.

### `administratively down` 🔴
Usually shutdown has been configured.

### `down/down` 🔴
Often indicates a physical/link problem or inactive underlying connection.

### ROAS special point
A subinterface depends on the parent physical interface being operational and on the VLAN/trunk path being correctly established.

Use:

```cisco
show ip interface brief
show interfaces g0/0
show interfaces trunk
```

---

# 🏢 13. Multilayer Switch + SVI

Enterprise networks commonly use a **Layer 3 switch** instead of sending all inter-VLAN traffic through a single router.

The Layer 3 switch can create **SVIs — Switch Virtual Interfaces**.

```text
                 🏢 MULTILAYER SWITCH
        ┌─────────────────────────────┐
        │                             │
        │ VLAN 10 → SVI 10            │
        │ VLAN 20 → SVI 20            │
        │ VLAN 30 → SVI 30            │
        │                             │
        └─────────────────────────────┘
             │       │       │
            🟦      🟩      🟨
```

An SVI provides a Layer 3 interface associated with a VLAN.

---

# ⚙️ 14. SVI Configuration

## 🎯 Example

```cisco
Switch(config)# ip routing
```

Then:

```cisco
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

VLAN 20:

```cisco
Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

Now the multilayer switch can route between these connected networks.

---

# 🚨 15. Why `ip routing` Matters

On a multilayer switch, an SVI can have an IP address without the switch necessarily performing Layer 3 routing for user traffic.

Enable:

```cisco
ip routing
```

Verify:

```cisco
show running-config | include ip routing
show ip route
```

💡 Think:

```text
SVI = Layer 3 interface
ip routing = Layer 3 forwarding capability
```

---

# 🟢 16. SVI `up/up` — Important Troubleshooting Concept

A common beginner mistake is:

> “I created `interface vlan 10`, gave it an IP and used `no shutdown`, so it must be up.”

Not necessarily.

The exact behavior depends on platform/software, but an SVI generally needs the associated VLAN to exist and have an operational Layer 2 presence/path for the SVI to become operational.

Check:

```cisco
show vlan brief
show interfaces vlan 10
show ip interface brief
```

If required, verify that an access port or trunk carrying VLAN 10 is active.

---

# 🔀 17. SVI Topology

```text
                 🌐 ROUTER / INTERNET
                        │
                   Routed Uplink
                        │
                ┌───────▼────────┐
                │  L3 SWITCH 🔀  │
                │                │
                │ SVI VLAN 10 🟦 │
                │ SVI VLAN 20 🟩 │
                │ SVI VLAN 30 🟨 │
                └─┬─────┬─────┬─┘
                  │     │     │
                 🟦    🟩    🟨
```

This is much closer to a typical enterprise campus architecture.

---

# 🆚 18. ROAS vs SVI

| Feature | ROAS 🍢 | SVI 🔀 |
|---|---|---|
| Main device | Router | Multilayer switch |
| VLAN gateways | Subinterfaces | SVIs |
| Trunk to routing device | Required | Not required for local VLAN gateway function |
| Typical scale | Small/medium | Medium/large |
| Hardware efficiency | One router interface | High-speed L3 switching |
| Configuration | `encapsulation dot1q` | `interface vlan` |
| Routing command | Router routes by default | `ip routing` commonly required |
| CCNA importance | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

# 🔌 19. Routed Ports

A multilayer switch can turn a physical interface into a Layer 3 routed port.

```cisco
interface g0/1
 no switchport
 ip address 10.0.0.1 255.255.255.252
 no shutdown
```

Now the port is not acting as a normal Layer 2 switchport.

Verify:

```cisco
show interfaces g0/1 switchport
show ip interface brief
show ip route
```

### ⚠️ SVI vs routed port

```text
SVI
interface vlan 10
     ↓
Logical L3 interface for a VLAN

Routed Port
interface g0/1
 no switchport
     ↓
Physical L3 interface
```

Do not confuse the two. 🧠

---

# 🏙️ 20. Enterprise Example

Imagine a company with:

```text
🟦 VLAN 10 — Users
🟩 VLAN 20 — Finance
🟨 VLAN 30 — HR
🟥 VLAN 40 — Servers
🟪 VLAN 50 — Management
```

A multilayer switch provides:

```text
VLAN 10 → 10.10.10.1
VLAN 20 → 10.10.20.1
VLAN 30 → 10.10.30.1
VLAN 40 → 10.10.40.1
VLAN 50 → 10.10.50.1
```

Traffic between VLANs is routed by the L3 switch.

Security policies can later be applied using ACLs. 🔐

---

# 🗺️ 21. Topology Gallery — 10 Designs

## 🟦 Topology 1 — Basic ROAS

```text
PCs → L2 Switch → Trunk → Router
```

Best for learning fundamentals.

---

## 🟩 Topology 2 — ROAS with 3 VLANs

```text
🟦 VLAN10 ┐
🟩 VLAN20 ├→ Switch → Trunk → Router
🟨 VLAN30 ┘
```

---

## 🟨 Topology 3 — L3 Switch with SVIs

```text
Users → L3 Switch → Router/Firewall
         │
         ├─ SVI 10
         ├─ SVI 20
         └─ SVI 30
```

---

## 🟥 Topology 4 — Multiple Access Switches

```text
PCs → Access SW1 ─┐
                  ├─ Trunks → L3 Switch
PCs → Access SW2 ─┘
```

---

## 🟪 Topology 5 — Server VLAN

```text
Users 🟦 ──┐
Finance 🟩 ├── L3 Switch 🔀 ── Server VLAN 🟥
HR 🟨 ─────┘
```

---

## 🟧 Topology 6 — Management VLAN

```text
Users ── VLAN 10
Admins ─ VLAN 50 ── Management SVI
```

---

## 🟫 Topology 7 — Routed Uplink

```text
Access/L3 Switch ──(L3 /30)── Core L3 Switch
```

The uplink is routed rather than trunked.

---

## 🟦 Topology 8 — Redundant L3 Gateways

```text
             ┌── L3 SW1 🔀 ──┐
VLANs ───────┤               ├── Core
             └── L3 SW2 🔀 ──┘
```

A first look at gateway redundancy concepts.

---

## 🟩 Topology 9 — Voice + Data

```text
IP Phone 📞
    │
    ├── Voice VLAN 🟪
    └── Data VLAN 🟦 → PC
              │
              ▼
          L3 Gateway
```

---

## 🌐 Topology 10 — Dual-Stack Preview

```text
VLAN 10
 ├─ IPv4 gateway
 └─ IPv6 gateway

VLAN 20
 ├─ IPv4 gateway
 └─ IPv6 gateway
```

Inter-VLAN routing can exist for both IPv4 and IPv6.

---

# 🧪 22. Practical Lab Series

> **Do not rush these.** The goal is to build troubleshooting instincts, not just memorize commands. 🧠

---

## 🧪 LAB 1 — Same VLAN Baseline

### Objective
Prove that same-VLAN communication works before introducing routing.

### Topology

```text
PC-A 🟦 ── SW ── PC-B 🟦
```

### Addressing

| PC | IP | VLAN |
|---|---|---:|
| A | `192.168.10.10/24` | 10 |
| B | `192.168.10.20/24` | 10 |

### Test

```text
ping 192.168.10.20
```

Expected: ✅ Success.

### Learning point
No inter-VLAN routing is needed.

---

## 🧪 LAB 2 — ROAS with 2 VLANs ⭐

Build:

```text
PC-A 🟦 VLAN10
      │
    SWITCH
      │ trunk
    ROUTER
      │
PC-B 🟩 VLAN20
```

Requirements:

- VLAN 10
- VLAN 20
- Access ports
- Trunk
- Router subinterfaces
- Correct gateways
- Successful cross-VLAN ping

### Verification checklist

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show arp
```

---

## 🧪 LAB 3 — ROAS with 3 VLANs 🔥

Create:

```text
VLAN 10 → 192.168.10.0/24
VLAN 20 → 192.168.20.0/24
VLAN 30 → 192.168.30.0/24
```

Router:

```text
G0/0.10 → .1
G0/0.20 → .1
G0/0.30 → .1
```

Test every pair:

```text
10 → 20
10 → 30
20 → 30
```

🎯 Goal: all valid inter-VLAN paths work.

---

## 🧪 LAB 4 — Multilayer Switch SVI ⭐⭐⭐

Build:

```text
PCs
 │
 ▼
L2 Switches
 │ trunks
 ▼
L3 Switch
 ├─ SVI 10
 ├─ SVI 20
 └─ SVI 30
```

Configure:

```cisco
ip routing
```

Then create the SVIs.

Verify:

```cisco
show ip interface brief
show ip route
```

---

## 🧪 LAB 5 — Routed Uplink

Create two Layer 3 switches:

```text
L3-SW1 ───── L3-SW2
       /30
```

Example:

```text
SW1: 10.0.0.1/30
SW2: 10.0.0.2/30
```

Configure both interfaces with:

```cisco
no switchport
```

Verify the connected route.

---

## 🧪 LAB 6 — Broken Trunk Challenge 🐛

Intentionally remove trunking.

Symptom:

```text
Same VLAN → works
Different VLAN → fails
```

Investigate:

```cisco
show interfaces trunk
show interfaces g0/1 switchport
```

Fix the trunk.

---

## 🧪 LAB 7 — Wrong Gateway Challenge 🚨

Give PC-A:

```text
IP:      192.168.10.10
Gateway: 192.168.20.1 ❌
```

Try to reach VLAN 20.

Observe failure.

Correct the gateway:

```text
192.168.10.1 ✅
```

---

## 🧪 LAB 8 — SVI Down Challenge 🔴

Create VLAN 10 and SVI 10 but deliberately ensure there is no operational Layer 2 presence for the VLAN.

Check:

```cisco
show vlan brief
show ip interface brief
show interfaces vlan 10
```

Find why the SVI is not operational.

---

## 🧪 LAB 9 — Allowed VLAN Challenge 🔐

Configure:

```cisco
switchport trunk allowed vlan 10
```

Then attempt VLAN 20 communication.

Expected: ❌ failure.

Fix:

```cisco
switchport trunk allowed vlan 10,20
```

---

## 🧪 LAB 10 — Full Campus Challenge 🏆

Build:

```text
             🌐 Internet/Firewall
                    │
              ┌─────▼─────┐
              │ Core L3   │
              │ Switch    │
              └─────┬─────┘
                    │
          ┌─────────┴─────────┐
          │                   │
      Access SW1          Access SW2
       │ │ │                │ │ │
      🟦 🟩 🟨             🟦 🟩 🟨
```

Requirements:

- 3 VLANs
- Access ports
- Trunks
- SVIs
- `ip routing`
- Routed uplink
- End-host gateways
- Verification
- Troubleshooting documentation

🎯 Final test:

Every allowed VLAN must communicate according to the design.

---

# 🔧 23. Cisco Command Reference

## VLAN

```cisco
show vlan brief
```

Shows VLANs and access-port membership.

## Trunk

```cisco
show interfaces trunk
```

Shows trunk status and VLAN information.

## Interface status

```cisco
show ip interface brief
```

Fast overview of Layer 3 interfaces.

## Switchport details

```cisco
show interfaces g0/1 switchport
```

Useful for identifying whether an interface is acting as an access/trunk switchport.

## Routing table

```cisco
show ip route
```

Critical for Layer 3 troubleshooting.

## ARP

```cisco
show arp
```

Maps IPv4 addresses to Layer 2 addresses known to the device.

## MAC table

```cisco
show mac address-table
```

Shows Layer 2 MAC learning.

## Interface details

```cisco
show interfaces
```

Useful for physical and data-link troubleshooting.

## Configuration

```cisco
show running-config
```

Check what is actually configured.

---

# 💻 24. Windows Verification Commands

### IP configuration

```powershell
ipconfig /all
```

### Test gateway

```powershell
ping 192.168.10.1
```

### Test remote VLAN

```powershell
ping 192.168.20.10
```

### ARP table

```powershell
arp -a
```

### Routing table

```powershell
route print
```

### Path testing

```powershell
tracert 192.168.20.10
```

---

# 🐧 25. Linux Verification Commands

```bash
ip addr
ip route
ip neigh
ping 192.168.10.1
ping 192.168.20.10
traceroute 192.168.20.10
```

💡 `ip route` is especially useful for confirming the host's default route.

---

# 🐛 26. Troubleshooting — The Golden Flow

When inter-VLAN routing fails, **do not randomly change commands.** Follow the path.

```text
1️⃣ Is the PC configured correctly?
        ↓
2️⃣ Is the PC in the correct VLAN?
        ↓
3️⃣ Is the VLAN created?
        ↓
4️⃣ Is the trunk working?
        ↓
5️⃣ Is the VLAN allowed on the trunk?
        ↓
6️⃣ Is the gateway correct?
        ↓
7️⃣ Is the gateway interface up?
        ↓
8️⃣ Does the routing table know both networks?
        ↓
9️⃣ Does ARP resolve correctly?
        ↓
🔟 Is anything filtering the traffic?
```

This workflow is more valuable than memorizing 100 commands. 🧠

---

# 🚨 27. Common Failure Scenarios

## ❌ Scenario 1 — Wrong VLAN

PC should be VLAN 10 but is connected to VLAN 20.

Check:

```cisco
show vlan brief
show interfaces f0/1 switchport
```

---

## ❌ Scenario 2 — VLAN Doesn't Exist

```cisco
show vlan brief
```

If VLAN 20 is missing, create it.

---

## ❌ Scenario 3 — Trunk Missing

Check:

```cisco
show interfaces trunk
```

---

## ❌ Scenario 4 — VLAN Not Allowed

Check allowed VLANs on the trunk.

```cisco
show interfaces trunk
```

---

## ❌ Scenario 5 — Wrong ROAS Encapsulation

Example:

```cisco
interface g0/0.20
 encapsulation dot1q 30
```

But the subinterface is intended for VLAN 20.

Result: VLAN mapping is wrong.

---

## ❌ Scenario 6 — Wrong Default Gateway

VLAN 10 host must normally use its VLAN 10 gateway.

```text
192.168.10.1 ✅
192.168.20.1 ❌
```

---

## ❌ Scenario 7 — SVI Shutdown

Check:

```cisco
show running-config interface vlan 10
```

If needed:

```cisco
interface vlan 10
 no shutdown
```

---

## ❌ Scenario 8 — `ip routing` Missing

On a multilayer switch:

```cisco
show running-config | include ip routing
```

Enable when required:

```cisco
ip routing
```

---

## ❌ Scenario 9 — Duplicate IP

Two hosts using the same address can cause unpredictable communication.

Check host configurations and ARP behavior.

---

## ❌ Scenario 10 — Wrong Subnet Mask

Example:

```text
PC-A: 192.168.10.10/24
PC-B: 192.168.20.10/16 ❌
```

A wrong mask can change the host's idea of what is local vs remote.

---

## ❌ Scenario 11 — Routed Port Mistaken for Switchport

If you configure:

```cisco
no switchport
```

that interface is no longer behaving as a normal Layer 2 switchport.

Check:

```cisco
show interfaces g0/1 switchport
show ip interface brief
```

---

## ❌ Scenario 12 — Native VLAN Mismatch

ROAS and trunk designs can fail when native VLAN handling is inconsistent.

Verify both sides and avoid changing native VLAN settings casually.

---

# 🧠 28. MAC + ARP + Routing Table — The Three Tables to Remember

This is one of the most useful mental models in CCNA.

### Switch MAC table

```text
MAC → Switch port/VLAN
```

Question:
> Where is this device?

### ARP table

```text
IPv4 → MAC
```

Question:
> What Layer 2 address corresponds to this IPv4 neighbor?

### Routing table

```text
Network → Next hop/interface
```

Question:
> Where should this IP packet go?

```text
🟦 MAC TABLE
     ↓
Where is the device?

🟨 ARP TABLE
     ↓
What MAC belongs to this IPv4 neighbor?

🟥 ROUTING TABLE
     ↓
Where should the IP packet go?
```

🔥 Learn this deeply and troubleshooting becomes much easier.

---

# 🔐 29. Security Considerations

Inter-VLAN routing provides connectivity, **not automatic security**.

A VLAN is not a complete security boundary by itself.

Good practices include:

- 🔐 Restrict allowed VLANs on trunks
- 🔐 Avoid using VLAN 1 for normal user data where practical
- 🔐 Protect management VLANs
- 🔐 Use secure management protocols
- 🔐 Apply ACLs where policy requires them
- 🔐 Use DHCP snooping/DAI where appropriate
- 🔐 Control unused switch ports
- 🔐 Document gateway addressing
- 🔐 Monitor unusual inter-VLAN traffic

> 🧠 **Segmentation + routing + policy = useful security architecture.**

ACLs will become much more important when you reach the ACL module.

---

# 🏥 30. Real-World Scenario — Hospital Network

Imagine:

```text
🟦 VLAN 10 — Doctors
🟩 VLAN 20 — Nurses
🟨 VLAN 30 — Administration
🟥 VLAN 40 — Medical Servers
🟪 VLAN 50 — Network Management
```

Doctors may need access to medical servers.

Administration may need access to finance applications.

Network management traffic should be restricted.

A multilayer switch can provide the VLAN gateways and routing, while security policies can control what is allowed between networks.

```text
Doctors 🟦 ─┐
Nurses  🟩 ─┤
Admin   🟨 ─┼── L3 SWITCH 🔀 ── Servers 🟥
Mgmt    🟪 ─┘
```

🎯 This is the bridge from CCNA lab knowledge to real enterprise design.

---

# 🧩 31. Scenario Challenge — "The College Network Is Down"

You are the junior network engineer. 👨‍💻

The college has:

```text
VLAN 10 → Students
VLAN 20 → Faculty
VLAN 30 → Admin
```

Students can communicate with students.

Faculty can communicate with faculty.

But:

```text
Student → Faculty ❌
Student → Admin ❌
Faculty → Admin ❌
```

### Your mission 🎯

Find the fault **without immediately looking for the final answer**.

Check in this order:

```text
□ Host IP
□ Host mask
□ Default gateway
□ VLAN assignment
□ VLAN existence
□ Trunk
□ Allowed VLANs
□ Gateway interface/SVI
□ Routing table
□ ARP
□ Filtering
```

### Bonus

Ask yourself:

> If same-VLAN pings work but every cross-VLAN ping fails, which layer should I investigate first?

---

# 🧠 32. Practice Exercises

### 🟢 Beginner

1. What is inter-VLAN routing?
2. Why can't a normal Layer 2 switch route between VLANs?
3. What is a default gateway?
4. What is an SVI?
5. What does `no switchport` do?
6. What is Router-on-a-Stick?
7. Why is 802.1Q important in ROAS?

### 🟡 Intermediate

8. Explain the packet journey from VLAN 10 to VLAN 20.
9. Compare ROAS and SVI routing.
10. Why can an SVI be down even after `no shutdown`?
11. What does `ip routing` do on a multilayer switch?
12. What is the difference between an SVI and a routed port?
13. Why does the Ethernet destination MAC change after routing?

### 🔴 Advanced

14. Design a 6-VLAN campus using SVIs.
15. Design a ROAS network for three departments.
16. Explain how a wrong subnet mask can break inter-VLAN communication.
17. Explain how an allowed-VLAN restriction can cause selective failure.
18. Design a routed uplink between two L3 switches.
19. Explain how ACLs could later restrict traffic between VLANs.

### 🧨 Troubleshooting

20. VLAN 10 works locally but cannot reach VLAN 20. Give 10 possible causes.
21. SVI is `down/down`. What would you inspect?
22. Router subinterface is `up/down`. What would you investigate?
23. The routing table has both connected networks, but the PC still cannot ping the other VLAN. What next?
24. Only one VLAN fails across a trunk. What does that suggest?

---

# 🎤 33. Interview Questions

## 🟢 Beginner

**Q1. What is inter-VLAN routing?**

Routing traffic between different VLANs using a Layer 3 device.

**Q2. Why is a default gateway required?**

It provides the host with a Layer 3 path to remote networks.

**Q3. What is Router-on-a-Stick?**

A router uses multiple 802.1Q subinterfaces over one physical link to route between VLANs.

**Q4. What is an SVI?**

A logical Layer 3 interface associated with a VLAN on a multilayer switch.

## 🟡 Intermediate

**Q5. ROAS vs SVI?**

ROAS uses router subinterfaces; SVI routing uses VLAN interfaces on a multilayer switch.

**Q6. What does `no switchport` do?**

It converts a supported multilayer switch interface from Layer 2 switchport operation to Layer 3 routed-port operation.

**Q7. Why use `ip routing`?**

It enables Layer 3 routing on a multilayer switch when required.

## 🔴 Advanced

**Q8. Why does the destination MAC change after a router hop?**

Because the Layer 2 frame is terminated and a new frame is built for the next Layer 2 segment.

**Q9. Why can VLAN 10 work while VLAN 20 fails?**

Possible causes include incorrect VLAN assignment, VLAN missing, trunk allowed-list issue, wrong gateway, incorrect subinterface/SVI mapping, or addressing errors.

**Q10. How would you troubleshoot inter-VLAN routing?**

Follow the packet path from host configuration → VLAN → trunk → gateway → routing table → ARP → filtering.

---

# ⚡ 34. Quick Revision

```text
VLAN = Layer 2 separation 🟦

Different VLANs = different Layer 2 broadcast domains

Different IP networks need Layer 3 routing 🔀

Default gateway = host's exit toward remote networks 🚪

ROAS = router + subinterfaces + 802.1Q 🍢

SVI = VLAN-associated Layer 3 interface on L3 switch

ip routing = enables L3 routing on multilayer switch

no switchport = Layer 3 routed physical interface

MAC table = MAC → port/VLAN
ARP = IPv4 → MAC
Routing table = network → path
```

---

# 🧾 35. Cheat Sheet

| Need | Command |
|---|---|
| VLAN list | `show vlan brief` |
| Trunk check | `show interfaces trunk` |
| Interface overview | `show ip interface brief` |
| Routing table | `show ip route` |
| ARP | `show arp` |
| MAC table | `show mac address-table` |
| Interface details | `show interfaces` |
| Config | `show running-config` |
| Enable L3 routing | `ip routing` |
| Create SVI | `interface vlan 10` |
| SVI IP | `ip address 192.168.10.1 255.255.255.0` |
| Routed port | `no switchport` |
| ROAS subinterface | `interface g0/0.10` |
| ROAS VLAN mapping | `encapsulation dot1q 10` |
| Host Windows IP | `ipconfig /all` |
| Windows routes | `route print` |
| Linux IP | `ip addr` |
| Linux routes | `ip route` |

---

# 🧭 36. Troubleshooting Decision Tree

```text
                 Cross-VLAN Ping Fails
                         │
                         ▼
                Can host ping gateway?
                    /           \
                  NO             YES
                  │                │
          Check VLAN/IP/GW       ▼
                            Check routing
                                │
                        ┌───────┴────────┐
                        │                │
                     ROAS              SVI
                        │                │
                  Check trunk      Check ip routing
                  subinterface      Check SVI state
                  dot1q mapping     Check VLAN
                        │                │
                        └───────┬────────┘
                                ▼
                          Check ARP
                                │
                                ▼
                        Check filtering/ACL
```

---

# 🏁 37. Completion Checklist

### Fundamentals

- [ ] I understand why VLANs cannot communicate through Layer 2 alone
- [ ] I understand VLAN vs subnet
- [ ] I understand default gateway
- [ ] I can explain a packet's journey between VLANs

### ROAS

- [ ] I can create VLANs
- [ ] I can configure access ports
- [ ] I can configure a trunk
- [ ] I can create router subinterfaces
- [ ] I understand `encapsulation dot1q`
- [ ] I can configure VLAN gateways

### SVI

- [ ] I understand an SVI
- [ ] I can configure `interface vlan X`
- [ ] I understand `ip routing`
- [ ] I can troubleshoot SVI state

### Routed ports

- [ ] I understand `no switchport`
- [ ] I can configure a Layer 3 point-to-point link
- [ ] I can verify connected routes

### Troubleshooting

- [ ] I can check VLANs
- [ ] I can check trunks
- [ ] I can check gateways
- [ ] I can check the routing table
- [ ] I can check ARP
- [ ] I can isolate a failure systematically

### Practical

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

---

# 🔗 38. Connect This Topic to the Rest of CCNA

### ⬅️ Previous concepts

- VLANs created Layer 2 segmentation
- Trunking carries multiple VLANs
- STP prevents Layer 2 loops
- EtherChannel bundles links

### 👉 This module adds

**Layer 3 communication between those VLANs.**

### ➡️ Next concepts

You are now ready to move deeper into:

```text
Inter-VLAN Routing
      ↓
🌐 Routing Fundamentals
      ↓
Connected / Static Routes
      ↓
Dynamic Routing
      ↓
OSPF
```

Later, ACLs will add **traffic policy** between these routed networks.

---

# 🌟 Final Takeaway

> **VLANs separate networks. Trunks carry VLANs. Layer 3 gateways connect VLANs.**

Remember this picture:

```text
       🟦 VLAN 10
            │
            │
       🚪 Gateway
            │
            ▼
       🔀 ROUTING
        /       \
       /         \
🟩 VLAN 20      🟨 VLAN 30
```

If you can explain **why the packet needs a gateway, how the switch carries the VLAN, how the router/L3 switch makes a routing decision, and how the new Layer 2 frame reaches the destination**, you genuinely understand Inter-VLAN Routing. 🧠🔥

---

## 🏆 Challenge Yourself Before Moving On

Without looking at the configuration above, build a network containing:

```text
🟦 VLAN 10 — Users
🟩 VLAN 20 — Finance
🟨 VLAN 30 — Servers
```

Then implement it **twice**:

1. 🍢 Using Router-on-a-Stick
2. 🔀 Using a Multilayer Switch with SVIs

Finally, deliberately break:

- one access VLAN
- one trunk
- one allowed VLAN
- one gateway
- one routing configuration

Then troubleshoot each failure using only verification commands.

> 💡 **The moment you can break your own network and confidently repair it, you are moving from “learning commands” to “thinking like a network engineer.”** 🚀

---

**📚 Module 19 Complete — Inter-VLAN Routing**

**Next → 20 — Routing Fundamentals 🌐**
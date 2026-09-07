# 🔄 Basic Network Communication

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain how data moves between networked devices.
- Understand the roles of frames, packets, segments, and data.
- Describe encapsulation and decapsulation at a basic level.
- Explain same-network and different-network communication.
- Understand the role of MAC addresses, IP addresses, ARP, switches, routers, and gateways.
- Follow a packet through common network topologies.
- Use basic commands to verify connectivity and investigate failures.
- Troubleshoot communication using a structured Layer 1-to-application approach.

---

# 1. What Is Network Communication? 🌐

**Network communication** is the exchange of data between devices using network technologies and agreed protocols.

A simple example:

```text
💻 PC1
  │
  │ Data
  ▼
🔀 Switch
  │
  ▼
💻 PC2
```

The actual process involves multiple layers and protocols.

A useful simplified model is:

```text
Application data
      ↓
Transport information
      ↓
IP packet
      ↓
Ethernet frame
      ↓
Bits/signals
```

---

# 2. A Simple Real-World Analogy 📦

Imagine sending a parcel.

```text
📝 Message
   ↓
📦 Package
   ↓
🏷️ Destination information
   ↓
🚚 Local delivery
   ↓
🛣️ Long-distance transport
   ↓
🏠 Destination
```

Networking works in a similar layered way.

```text
Application data
   ↓
Segment / datagram
   ↓
IP packet
   ↓
L2 frame
   ↓
Physical transmission
```

Each layer adds information needed for its part of the journey.

---

# 3. The Main Pieces of Communication

| Concept | Basic purpose |
|---|---|
| Application data | Information the application wants to send |
| TCP/UDP | Transport communication between applications |
| IP | Logical addressing and routing |
| MAC | Local Layer 2 delivery |
| Switch | Forwards Layer 2 frames |
| Router | Forwards Layer 3 packets between networks |
| Default gateway | Local router used to reach remote networks |
| ARP | Resolves IPv4 address to MAC on a local link |
| DNS | Resolves names to IP information |

---

# 4. Data, Segment, Datagram, Packet, Frame 🔄

Different layers use different terminology.

### Application data

```text
HTTP request
DNS query
File data
```

### TCP segment

When application data is carried using TCP, the transport-layer unit is commonly called a **segment**.

### UDP datagram

When UDP is used, the transport-layer unit is commonly called a **datagram**.

### IP packet

The Layer 3 unit is an **IP packet**.

### Ethernet frame

The Layer 2 unit on an Ethernet link is an **Ethernet frame**.

Conceptually:

```text
Application Data
      ↓
TCP Segment / UDP Datagram
      ↓
IP Packet
      ↓
Ethernet Frame
      ↓
Bits / Signals
```

> 📌 Exact terminology can depend on the protocol and layer. This simplified model is intended to build the CCNA foundation.

---

# 5. Encapsulation 📦

**Encapsulation** is the process of adding protocol information as data moves down the networking stack before transmission.

Simplified:

```text
Application
   │
   ▼
[ Application Data ]
   │
   ▼
[ TCP Header | Data ]
   │
   ▼
[ IP Header | TCP Header | Data ]
   │
   ▼
[ Ethernet Header | IP Packet | FCS ]
   │
   ▼
Physical transmission
```

Each layer adds information required by the corresponding receiving layer.

---

# 6. Decapsulation 📤

At the receiving device, headers are processed and removed as information moves upward through the stack.

```text
Physical
   ↓
Ethernet Frame
   ↓
IP Packet
   ↓
TCP Segment / UDP Datagram
   ↓
Application Data
```

This is called **decapsulation**.

### Big picture

```text
Sender                         Receiver

Application                    Application
     ↓                              ↑
Transport                      Transport
     ↓                              ↑
Network                        Network
     ↓                              ↑
Data Link                      Data Link
     ↓                              ↑
Physical  ─────────────────→  Physical
```

---

# 7. Same-Network Communication 🏠

Suppose:

```text
PC1 = 192.168.10.10/24
PC2 = 192.168.10.20/24
```

Both belong to:

```text
192.168.10.0/24
```

Topology:

```text
PC1 ───────── SW1 ───────── PC2
```

PC1 determines that PC2 is local.

A simplified process is:

```text
PC1
 ↓
Determine destination is local
 ↓
ARP for PC2's MAC if needed
 ↓
Build Ethernet frame
 ↓
Switch forwards frame
 ↓
PC2 receives frame
```

The router is not required for basic communication between these two hosts on the same Layer 2 network.

---

# 8. Different-Network Communication 🌍

Suppose:

```text
PC1 = 192.168.10.10/24
Server = 192.168.20.50/24
```

Topology:

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ Server
```

The destination is remote from PC1's subnet.

PC1 therefore sends the frame toward its **default gateway**.

Simplified flow:

```text
PC1
 ↓
Determine destination is remote
 ↓
ARP for default gateway
 ↓
Ethernet frame → Router
 ↓
Router removes/handles incoming L2 frame
 ↓
Router checks destination IP
 ↓
Routing decision
 ↓
New L2 frame on outgoing interface
 ↓
Server
```

---

# 9. Same Network vs Different Network

| Situation | First Layer 2 destination |
|---|---|
| Destination is local | Destination host's MAC |
| Destination is remote | Default gateway's MAC |

This is one of the most important foundational concepts in networking.

Example:

```text
Same subnet:
PC1 → PC2 MAC

Remote subnet:
PC1 → Gateway MAC → Router → ...
```

The IP destination remains the remote destination, while the local Ethernet destination is the next-hop device.

---

# 10. Role of the Default Gateway 🛣️

The default gateway is normally the Layer 3 device a host uses to reach destinations outside its local subnet.

Example:

```text
PC1
IP:      192.168.10.10
Mask:    255.255.255.0
Gateway: 192.168.10.1
```

Topology:

```text
192.168.10.0/24

PC1 ───── SW1 ───── R1
                    │
                    │
              Other networks
```

Without an appropriate gateway, the host may communicate with local devices but cannot normally reach remote IP networks.

---

# 11. Role of a Switch 🔀

A switch primarily forwards Ethernet frames based on Layer 2 information.

Simplified process:

```text
Frame arrives
     ↓
Learn source MAC
     ↓
Read destination MAC
     ↓
Lookup MAC table
     ↓
Known? ── Yes → Forward to correct interface
  │
  No
  ↓
Flood within VLAN
```

A switch does not normally make the same routing decisions that a router makes.

---

# 12. Role of a Router 🛣️

A router connects different IP networks and makes Layer 3 forwarding decisions.

Simplified process:

```text
Incoming frame
      ↓
Receive on interface
      ↓
Process Layer 3 packet
      ↓
Check destination IP
      ↓
Routing table lookup
      ↓
Choose outgoing interface/next hop
      ↓
Build appropriate new Layer 2 frame
      ↓
Forward
```

---

# 13. Role of ARP 🔍

For IPv4 Ethernet communication, a host often needs to discover the MAC address associated with a local IPv4 next hop.

### Local destination

```text
Who has 192.168.10.20?
```

### Remote destination

The host generally needs the MAC of the default gateway:

```text
Who has 192.168.10.1?
```

ARP therefore helps connect:

```text
IPv4 addressing
      ↓
MAC addressing
      ↓
Ethernet delivery
```

IPv6 does not use ARP; it uses Neighbor Discovery (ND) instead.

---

# 14. A Complete Simple Communication Example 🧠

Consider:

```text
PC1:     192.168.10.10/24
Gateway: 192.168.10.1
Server:  192.168.20.50/24
```

Topology:

```text
💻 PC1
192.168.10.10
     │
     ▼
🔀 SW1
     │
     ▼
🛣️ R1
     │
     ▼
🔀 SW2
     │
     ▼
🖥️ Server
192.168.20.50
```

### Step 1 — Application

PC1 wants information from the server.

### Step 2 — Transport

The application uses TCP or UDP as required.

### Step 3 — IP

The packet has:

```text
Source IP      = 192.168.10.10
Destination IP = 192.168.20.50
```

### Step 4 — Next hop

PC1 sees that `192.168.20.50` is remote.

It uses gateway `192.168.10.1` as the local next hop.

### Step 5 — ARP

PC1 learns the gateway's MAC if it is not already in the ARP cache.

### Step 6 — Ethernet

The first frame has approximately:

```text
Source MAC      = PC1 MAC
Destination MAC = R1 G0/0 MAC
```

### Step 7 — Switch

SW1 forwards the frame toward R1.

### Step 8 — Router

R1 routes the IP packet toward the server network.

### Step 9 — New Ethernet frame

R1 sends a new Layer 2 frame on its outgoing interface.

### Step 10 — Server

The server receives the packet and processes the request.

---

# 15. MAC Addresses Change at Each Routed Hop 🔄

This is a critical concept.

For a routed path:

```text
PC1 ─── R1 ─── R2 ─── Server
```

The Layer 2 frame is local to each link.

Conceptually:

```text
Link 1:
Src MAC = PC1
Dst MAC = R1

Link 2:
Src MAC = R1
Dst MAC = R2

Link 3:
Src MAC = R2
Dst MAC = Server
```

The Layer 3 source and destination IP addresses generally remain associated with the end-to-end IP packet, subject to technologies such as NAT.

---

# 16. Communication Across Multiple Routers 🌍

Topology:

```text
PC1
 │
 ▼
SW1
 │
 ▼
R1
 │
 ▼
R2
 │
 ▼
R3
 │
 ▼
SW2
 │
 ▼
Server
```

The packet may cross multiple Layer 3 hops.

At each router:

```text
Receive
  ↓
Inspect destination IP
  ↓
Routing table lookup
  ↓
Select next hop/interface
  ↓
Create next-hop Layer 2 frame
  ↓
Forward
```

The router does not simply send the original Ethernet frame unchanged across the entire path.

---

# 17. Unicast, Broadcast, and Multicast Communication 📢

## Unicast

One sender → one receiver.

```text
PC1 ─────────→ PC2
```

## Broadcast

One sender → all relevant devices in the broadcast domain.

```text
          PC2
           ↑
           │
PC1 ──── SW1 ──── PC3
           │
           ↓
          PC4
```

IPv4 broadcasts are generally confined by routers.

## Multicast

One sender → a subscribed group.

```text
           ┌──→ PC2
Sender ────┼──→ PC3
           └──→ PC4
```

---

# 18. What Happens When You Ping? 🧪

A ping uses **ICMP Echo Request** and **ICMP Echo Reply**.

Simplified:

```text
PC1                         PC2
 │                            │
 │── ICMP Echo Request ─────→│
 │                            │
 │←─ ICMP Echo Reply ────────│
 │                            │
```

Before the ICMP packet can travel over Ethernet, the sender may need address-resolution information.

Therefore a simple ping can involve:

```text
ARP
 ↓
Ethernet
 ↓
IP
 ↓
ICMP
```

---

# 19. What Happens When You Open a Website? 🌐

A simplified process is:

```text
1. User enters hostname
        ↓
2. DNS resolution
        ↓
3. Determine destination/network path
        ↓
4. Resolve local next-hop MAC if required
        ↓
5. Establish required transport/security session
        ↓
6. Send application request
        ↓
7. Server responds
        ↓
8. Browser displays content
```

In modern HTTPS environments, the exact transport may be TCP or QUIC over UDP depending on the protocol and implementation.

---

# 20. Physical Topology — Same LAN 🗺️

```text
┌───────┐       ┌───────┐       ┌───────┐
│ PC1   │───────│  SW1  │───────│ PC2   │
└───────┘       └───┬───┘       └───────┘
                    │
                  ┌─┴───┐
                  │ PC3 │
                  └─────┘
```

Communication remains inside the local Layer 2 network when the destinations are local.

---

# 21. Physical Topology — Routed LANs 🗺️

```text
             LAN 1                         LAN 2

        ┌───────────┐                 ┌───────────┐
        │   SW1     │                 │   SW2     │
        └─┬───────┬─┘                 └─┬───────┬─┘
          │       │                       │       │
         PC1     PC2                     PC3    Server
          │       │                       │       │
          └───────┘                       └───────┘
                 \                         /
                  \                       /
                   └─────── R1 ─────────┘
```

R1 provides Layer 3 connectivity between the two IP networks.

---

# 22. Physical Topology — Multi-Router WAN 🌍

```text
LAN A                WAN                  LAN B

PCs ─ SW1 ─ R1 ───────── R2 ───────── R3 ─ SW2 ─ Server
```

This is a simplified representation of communication across multiple routed hops.

The exact WAN technology can vary:

- Ethernet
- Fiber
- MPLS
- Internet VPN
- Other provider technologies

---

# 23. Physical Topology — Client to Internet 🌐

```text
💻 Client
   │
   ▼
🔀 Access Switch
   │
   ▼
🛣️ Default Gateway/Router
   │
   ▼
🔥 Firewall/NAT
   │
   ▼
🌐 ISP
   │
   ▼
🌍 Internet
   │
   ▼
🖥️ Remote Server
```

Many real-world networks are more complex, but this topology provides a useful foundation.

---

# 24. Basic Cisco Configuration Example 🔵

For a simple routed lab, configure router interfaces according to the addressing plan.

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit

interface gigabitEthernet 0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
 exit

end
```

> Interface numbering varies by Cisco platform. Always confirm available interfaces with `show ip interface brief`.

---

# 25. Cisco Verification Commands 🔎

### Interface status

```cisco
show ip interface brief
```

### Interface details

```cisco
show interfaces
```

### Routing table

```cisco
show ip route
```

### ARP table

```cisco
show arp
```

### MAC table

```cisco
show mac address-table
```

### Configuration

```cisco
show running-config
```

### Test connectivity

```cisco
ping 192.168.20.50
```

### Trace path

```cisco
traceroute 192.168.20.50
```

### Neighbor discovery

```cisco
show cdp neighbors
show lldp neighbors
```

---

# 26. Endpoint Verification Commands 💻

## Windows

```powershell
ipconfig /all
arp -a
ping 192.168.20.50
tracert 192.168.20.50
nslookup example.com
```

## Linux

```bash
ip addr
ip route
ip neigh
ping 192.168.20.50
traceroute 192.168.20.50
nslookup example.com
dig example.com
```

---

# 27. Practical Lab 1 — PC-to-PC Communication 🧪

## Objective

Understand basic same-LAN communication.

## Topology

```text
PC1 ───────── SW1 ───────── PC2
```

## Addressing

| Device | IP | Mask |
|---|---|---|
| PC1 | `192.168.10.10` | `255.255.255.0` |
| PC2 | `192.168.10.20` | `255.255.255.0` |

## Tasks

1. Build the topology in Packet Tracer.
2. Configure both PCs.
3. Verify IP configuration.
4. Ping PC2 from PC1.
5. Check the switch MAC table.
6. Observe ARP on the endpoints.
7. Document what changed before and after the ping.

### Expected result

The ping should succeed if the interfaces, addressing, and cabling are correct.

---

# 28. Practical Lab 2 — Different Networks 🧪

## Topology

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ PC2
```

## Addressing

```text
LAN 1 = 192.168.10.0/24
LAN 2 = 192.168.20.0/24
```

| Device | Interface | IP |
|---|---|---|
| PC1 | NIC | `192.168.10.10/24` |
| R1 | G0/0 | `192.168.10.1/24` |
| R1 | G0/1 | `192.168.20.1/24` |
| PC2 | NIC | `192.168.20.20/24` |

Gateways:

```text
PC1 → 192.168.10.1
PC2 → 192.168.20.1
```

## Tasks

1. Configure all addresses.
2. Bring router interfaces up.
3. Verify R1 interfaces.
4. Verify connected routes.
5. Ping R1 from each PC.
6. Ping PC2 from PC1.
7. Inspect ARP tables.
8. Inspect the switch MAC tables.

---

# 29. Practical Lab 3 — Multi-Router Communication 🧪

## Topology

```text
PC1 ─ SW1 ─ R1 ─ R2 ─ SW2 ─ PC2
```

Use three networks:

```text
LAN A = 192.168.10.0/24
R1-R2 = 192.168.30.0/30
LAN B = 192.168.20.0/24
```

## Tasks

1. Configure all interfaces.
2. Configure routing so LAN A can reach LAN B.
3. Verify routing tables.
4. Ping each router interface.
5. Ping PC2 from PC1.
6. Run traceroute/tracert.
7. Identify each Layer 3 hop.

### Learning point

Traceroute helps visualize the Layer 3 path toward a destination.

---

# 30. Practical Lab 4 — Client to Server Service 🧪

## Topology

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ SERVER1
```

### Tasks

1. Configure two IP networks.
2. Configure routing.
3. Configure the server with an appropriate service.
4. Verify IP reachability.
5. Test the application.
6. Compare ping success with application success.
7. Troubleshoot a deliberately introduced failure.

### Goal

Understand that communication is more than simply:

```text
ping = success
```

Application communication may depend on DNS, ports, firewalls, and services.

---

# 31. Verification Method 🔎

Use this order instead of random troubleshooting:

```text
1️⃣ Physical link
        ↓
2️⃣ Interface status
        ↓
3️⃣ Layer 2 / VLAN
        ↓
4️⃣ MAC learning
        ↓
5️⃣ Host IP configuration
        ↓
6️⃣ ARP / neighbor resolution
        ↓
7️⃣ Default gateway
        ↓
8️⃣ Routing table
        ↓
9️⃣ DNS
        ↓
🔟 TCP/UDP port
        ↓
1️⃣1️⃣ Application/service
```

---

# 32. Troubleshooting Scenario 1 🚨

### Problem

PC1 and PC2 are connected to the same switch but cannot ping each other.

### Check

```text
Physical link
   ↓
IP addresses
   ↓
Subnet masks
   ↓
VLAN membership
   ↓
MAC table
   ↓
ARP cache
```

### Likely causes

- Wrong IP address
- Different subnet
- Interface down
- Wrong VLAN
- Bad cable
- Host firewall
- Duplicate addressing

---

# 33. Troubleshooting Scenario 2 🚨

### Problem

PC1 can reach its gateway but cannot reach a remote server.

### Investigation

```powershell
ping <gateway>
ping <server>
tracert <server>
```

On routers:

```cisco
show ip route
show ip interface brief
show arp
```

Possible causes:

- Missing route
- Incorrect route
- Remote interface down
- ACL/firewall filtering
- Incorrect gateway on the server
- Return-path problem

---

# 34. Troubleshooting Scenario 3 🚨

### Problem

The server IP responds to ping, but the application does not work.

### Think in layers

```text
IP reachability → Working?
        ↓
Transport port → Reachable?
        ↓
Server service → Running?
        ↓
Firewall → Permitting?
        ↓
Application → Correct configuration?
```

Do not stop troubleshooting after ping succeeds.

---

# 35. Common Beginner Mistakes ❌

### Mistake 1: Thinking the MAC address stays unchanged end-to-end

MAC addressing is local to Layer 2 links. Routers create new Layer 2 frames for outgoing links.

### Mistake 2: Thinking the switch routes between networks

A traditional Layer 2 switch forwards frames. Routing is a Layer 3 function, although multilayer switches can also perform routing.

### Mistake 3: Sending remote traffic directly to the remote server's MAC

The host normally sends the first Ethernet frame to its local default gateway when the destination is remote.

### Mistake 4: Assuming ping proves everything works

Ping only tests a particular form of IP/ICMP reachability.

### Mistake 5: Ignoring the return path

Two-way communication requires the response to have a valid path back to the source.

### Mistake 6: Troubleshooting randomly

Use a structured, layered process.

---

# 36. Industry Scenario 🏢

A company has:

```text
Users
  ↓
Access switches
  ↓
Distribution/Core
  ↓
Routers/Layer 3 switches
  ↓
Firewall
  ↓
Internet / Data Center
```

A user opens an internal application.

The traffic may involve:

```text
DNS
 ↓
Ethernet
 ↓
ARP/Neighbor Discovery
 ↓
IP
 ↓
Routing
 ↓
ACL/Firewall
 ↓
TCP/UDP
 ↓
Application
```

A network engineer must understand how these pieces interact rather than treating each command or protocol as an isolated topic.

---

# 37. Scenario-Based Challenge 🎯

## University Network

A student laptop has:

```text
IP:      10.10.20.50/24
Gateway: 10.10.20.1
```

The student wants to access a server:

```text
10.10.50.100
```

Topology:

```text
Laptop
  │
  ▼
Access Switch
  │
  ▼
Layer 3 Gateway
  │
  ▼
Core Network
  │
  ▼
Server Network
  │
  ▼
Server
```

### Questions

1. Is `10.10.50.100` local or remote to the laptop?
2. Whose MAC address does the laptop need for the first Ethernet frame?
3. Which device makes the first routing decision?
4. Will the original Ethernet frame travel unchanged across the entire network?
5. What commands could help troubleshoot failure?

### Expected answers

1. Remote.
2. The default gateway's MAC address.
3. The Layer 3 gateway/router.
4. No. New Layer 2 frames are used on subsequent links.
5. Examples:

```text
ipconfig /all
arp -a
ping
tracert
show ip interface brief
show ip route
show arp
show mac address-table
```

---

# 38. Practice Exercises ✍️

### Exercise 1

Explain this sequence in your own words:

```text
Data → Segment → Packet → Frame → Bits
```

### Exercise 2

Compare communication when the destination is:

```text
A. Same subnet
B. Different subnet
```

### Exercise 3

Draw a topology containing:

```text
2 PCs
2 switches
2 routers
1 server
```

Then show the path from PC1 to the server.

### Exercise 4

A host can ping its gateway but cannot reach a remote network.

List at least five possible causes.

### Exercise 5

Explain why the MAC destination changes at every routed hop while the IP destination generally remains the remote destination.

---

# 39. Interview Questions 🎤

## Beginner

1. What is network communication?
2. What is a frame?
3. What is a packet?
4. What is a segment?
5. What is encapsulation?
6. What is decapsulation?
7. What is a default gateway?

## Intermediate

8. Explain same-subnet communication.
9. Explain different-subnet communication.
10. What role does ARP play?
11. What does a switch do with a frame?
12. What does a router do with a packet?
13. Why does a router create a new Layer 2 frame?
14. What happens when a destination MAC is unknown?

## Advanced

15. Explain the end-to-end journey of a packet across multiple routers.
16. Why is Layer 2 information local to a link?
17. How do encapsulation and decapsulation relate to the OSI model?
18. Why can a packet reach the destination but the application still fail?
19. How can NAT change the expected end-to-end IP-address behavior?

## Troubleshooting

20. A host can reach local devices but not remote networks. What do you check?
21. A host can reach its gateway but not the destination. What do you check next?
22. How would you use traceroute to identify a possible routing problem?
23. How would you distinguish an ARP problem from a routing problem?
24. Why must you check the return path?

---

# 40. Quick Revision ⚡

```text
🔄 Network Communication
│
├── Application Data
│
├── Transport
│   ├── TCP Segment
│   └── UDP Datagram
│
├── Network
│   └── IP Packet
│
├── Data Link
│   └── Ethernet Frame
│
├── Physical
│   └── Bits / Signals
│
├── Same Network
│   └── Destination MAC
│
├── Remote Network
│   └── Default Gateway MAC
│
├── Switch
│   └── Layer 2 forwarding
│
├── Router
│   └── Layer 3 forwarding
│
└── Troubleshooting
    └── Physical → L2 → IP → Routing → Service
```

---

# 41. Cheat Sheet 📋

| Concept | Key point |
|---|---|
| Communication | Exchange of data between networked systems |
| Encapsulation | Adding protocol information while moving down the stack |
| Decapsulation | Processing/removing protocol information while moving up the stack |
| Segment | Common TCP transport-layer unit |
| Datagram | Common UDP transport-layer unit |
| Packet | Layer 3 IP data unit |
| Frame | Layer 2 Ethernet data unit |
| Same subnet | Host can use destination's local Layer 2 address |
| Remote subnet | Host normally sends first frame to default gateway |
| ARP | IPv4 address-to-MAC resolution on local Ethernet |
| Switch | Primarily forwards Layer 2 frames |
| Router | Forwards Layer 3 packets between networks |
| Gateway | Local Layer 3 path to remote networks |
| Ping | ICMP reachability test |
| Traceroute | Helps reveal Layer 3 path/hops |
| DNS | Name-resolution service |

---

# 42. Connection to Previous and Next Topics 🔗

### Previous topics

- [08 — Switching Fundamentals](08-Switching-Fundamentals.md)
- [09 — Client-Server Architecture](09-Client-Server-Architecture.md)
- [10 — Peer-to-Peer Networking](10-Peer-to-Peer-Networking.md)

You have learned what switches do, how applications can communicate through client-server relationships, and how peers can communicate directly.

### Next topic

➡️ **12 — Collision and Broadcast Domains**

Next you will learn how Layer 2 networks are divided into collision and broadcast domains, why switches changed collision behavior, and how routers and VLANs affect broadcast boundaries.

---

# ✅ Completion Checklist

- [ ] I understand basic network communication.
- [ ] I can distinguish data, segments/datagrams, packets, and frames.
- [ ] I understand encapsulation.
- [ ] I understand decapsulation.
- [ ] I understand same-subnet communication.
- [ ] I understand different-subnet communication.
- [ ] I understand the role of the default gateway.
- [ ] I understand the roles of switches and routers.
- [ ] I understand the basic purpose of ARP.
- [ ] I understand why MAC addresses change across routed hops.
- [ ] I understand unicast, broadcast, and multicast at a basic level.
- [ ] I can explain what happens during ping.
- [ ] I can use Windows networking commands.
- [ ] I can use Linux networking commands.
- [ ] I can use basic Cisco verification commands.
- [ ] I completed the PC-to-PC lab.
- [ ] I completed the different-network lab.
- [ ] I completed the multi-router lab.
- [ ] I completed the client-server lab.
- [ ] I can troubleshoot communication systematically.
- [ ] I can explain the packet journey in an interview.

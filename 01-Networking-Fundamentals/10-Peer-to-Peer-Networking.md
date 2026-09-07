# 🤝 Peer-to-Peer Networking

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain peer-to-peer (P2P) networking in simple terms.
- Understand how peers communicate without requiring a dedicated central server.
- Compare peer-to-peer and client-server architectures.
- Understand common P2P topologies and deployment models.
- Identify the advantages and limitations of P2P networks.
- Understand how Windows and Linux devices can participate in peer-to-peer communication.
- Understand basic file/printer/resource sharing concepts.
- Use connectivity and name-resolution commands when troubleshooting P2P communication.
- Design a small practical P2P network in Cisco Packet Tracer.
- Apply P2P concepts to realistic home, lab, and small-office scenarios.

---

# 1. What Is Peer-to-Peer Networking?

**Peer-to-peer networking** is a networking model in which devices communicate directly and can share resources with one another without requiring a dedicated central server for every shared resource.

Each participating device is called a **peer**.

A peer can provide resources and consume resources.

```text
💻 PC1  ←────────→  💻 PC2
  ↕                    ↕
Shares files        Uses files
Shares printer      Shares printer
```

Unlike a traditional client-server model, there does not have to be one dedicated machine providing all services.

> 📌 P2P is a **networking/application architecture**, not a requirement that devices be connected by a particular physical topology.

---

# 2. Simple Analogy 🤝

Imagine three students working on a project.

```text
👨‍🎓 Student A
      ↕
👩‍🎓 Student B
      ↕
👨‍🎓 Student C
```

Each student can:

- Give files to another student.
- Receive files.
- Share information.
- Request something from another student.

There is no single student acting as the permanent central server.

That is the basic idea behind peer-to-peer communication.

---

# 3. Client-Server vs Peer-to-Peer

| Feature | Client-Server | Peer-to-Peer |
|---|---|---|
| Dedicated central server | Usually | Not required |
| Resource provider | Centralized | Any participating peer can provide resources |
| Administration | More centralized | More distributed |
| Small network setup | Can be more infrastructure | Often simple |
| Large enterprise use | Very common | Less suitable as the primary model for many centralized services |
| Failure model | Server can be critical | Resources may be distributed |
| Management | Easier to centralize | Can become difficult as peers increase |
| Example | Company file server | Two PCs directly sharing files |

### Important

Real networks can use **both models at the same time**.

For example:

```text
Company network
│
├── Client ↔ Central File Server
│
├── Client ↔ DNS Server
│
└── PC1 ↔ PC2 for a temporary local share
```

---

# 4. What Makes a Device a Peer?

A peer is a participating network device that can communicate directly with another peer and potentially provide or consume resources.

For example:

```text
PC1
├── Provides shared folder
└── Uses PC2's shared folder

PC2
├── Provides shared printer
└── Uses PC1's shared folder
```

Both devices have peer capabilities.

A peer does not stop being a peer simply because it is temporarily acting as the requester or provider in a particular exchange.

---

# 5. Common P2P Resource Sharing 📁

Peer-to-peer networks can be used for:

- 📁 File sharing
- 🖨️ Printer sharing
- 🎵 Media sharing
- 💻 Direct application communication
- 🧪 Lab/test environments
- 🎮 Some multiplayer applications
- 🌐 Distributed applications

The exact implementation depends on the operating system and application.

---

# 6. P2P Does Not Mean “No Switch” 🔀

A very common beginner mistake is:

> “If it is peer-to-peer, devices must be directly connected by cable.”

Not necessarily.

A P2P network can use a switch.

### Topology 1 — Direct connection

```text
💻 PC1 ───────── 💻 PC2
```

### Topology 2 — Switched LAN

```text
        ┌─────────┐
        │   SW1   │
        └─┬─────┬─┘
          │     │
         PC1   PC2
```

The architecture describes **how resources/services are organized**, while the topology describes **how devices are connected**.

---

# 7. P2P Topology — Two Devices 🗺️

The simplest P2P network is a direct point-to-point connection.

```text
PC1 ───────────────── PC2
```

Example addressing:

| Device | IP address | Mask |
|---|---|---|
| PC1 | `192.168.1.10` | `/24` |
| PC2 | `192.168.1.20` | `/24` |

Because both devices are in the same IP network, they can communicate directly after Layer 2 address resolution.

### Use cases

- Temporary file transfer
- Testing
- Lab environments
- Direct device communication

---

# 8. P2P Topology — Small Switched LAN 🔀

A more practical P2P network can use a switch.

```text
                 🔀 SW1
              /    |    \
             /     |     \
           PC1    PC2    PC3
```

Each PC can share resources with the others.

There is no dedicated file server in this example.

### Example

```text
PC1 → shares folder A
PC2 → shares folder B
PC3 → shares printer
```

The switch provides Layer 2 connectivity, while the P2P model determines how resources are shared between endpoints.

---

# 9. P2P Topology — Larger Local Group

A small workgroup can contain several peers.

```text
                 🔀 SW1
       ┌──────────┼──────────┐
       │          │          │
      PC1        PC2        PC3
       │          │          │
   Shared A   Shared B   Shared C
       │          │          │
       └──────────┼──────────┘
                  │
                 PC4
```

Every peer does not have to connect physically to every other peer. The switch provides connectivity.

This is an important distinction between **logical architecture** and **physical topology**.

---

# 10. P2P Topology — Multi-Switch Small Office 🏢

```text
                 🔀 SW1
              /    |    \
            PC1   PC2   PC3
              \
               \
                🔀 SW2
              /    |    \
            PC4   PC5   PC6
```

All PCs can potentially participate as peers, provided Layer 2/Layer 3 connectivity and host-sharing permissions allow it.

The network can still use centralized services such as DNS or DHCP even though some resources are shared peer-to-peer.

---

# 11. P2P Topology — Routed Networks 🌐

Peers do not have to be in the same IP subnet.

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ PC2
```

Example:

```text
PC1 = 192.168.10.10/24
Gateway = 192.168.10.1

PC2 = 192.168.20.10/24
Gateway = 192.168.20.1
```

The peers are in different IP networks.

A router is therefore required for Layer 3 communication.

Whether a particular P2P application can operate across routed networks depends on the application, name resolution, firewall policies, and protocol design.

---

# 12. P2P Topology — Wireless LAN 📶

P2P resource sharing can also happen over Wi-Fi.

```text
                 📡 Access Point
                /      |      \
              PC1    Laptop   PC2
                \      |      /
                 Peer communication
```

The wireless access point provides network connectivity.

The sharing relationship remains peer-to-peer if devices provide resources directly to each other rather than relying on a dedicated server for the resource.

---

# 13. P2P vs Physical Topology 🧠

Do not confuse these concepts.

### Architecture

Describes the relationship between systems/services.

```text
Client-Server
Peer-to-Peer
```

### Physical topology

Describes how devices are physically connected.

```text
Star
Mesh
Ring
Point-to-Point
```

Therefore, a P2P network can use a:

- Direct point-to-point connection
- Star topology through a switch
- Wireless infrastructure topology
- Routed network
- Other physical/logical designs

---

# 14. How P2P Communication Works 🔄

Consider PC1 requesting a shared resource from PC2.

```text
PC1                         PC2
 │                            │
 │──── Resource request ─────→│
 │                            │
 │←──── Resource/data ────────│
 │                            │
```

At the networking level, communication may involve:

```text
Application
    ↓
TCP/UDP
    ↓
IP
    ↓
Ethernet/Wi-Fi
    ↓
Switch/AP/Router
    ↓
Destination peer
```

The exact protocols depend on the operating system and application.

---

# 15. Same-Subnet P2P Communication

Suppose:

```text
PC1 = 192.168.1.10/24
PC2 = 192.168.1.20/24
```

PC1 determines that PC2 is on the same local subnet.

For IPv4 Ethernet communication, ARP can be used to discover PC2's MAC address.

Then the frame can be sent locally.

```text
PC1
IP → 192.168.1.20
     ↓
ARP
     ↓
PC2 MAC
     ↓
Ethernet frame
     ↓
PC2
```

---

# 16. Different-Subnet P2P Communication

Suppose:

```text
PC1 = 192.168.10.10/24
PC2 = 192.168.20.10/24
```

PC1 identifies PC2 as remote.

The frame is sent toward the default gateway.

```text
PC1
  ↓
Default Gateway
  ↓
Router
  ↓
PC2 network
  ↓
PC2
```

The router forwards the IP packet between networks.

---

# 17. P2P and MAC Addresses 🏷️

For local Ethernet communication, devices use Layer 2 MAC addresses.

Example:

```text
PC1
IP  = 192.168.1.10
MAC = AAAA.AAAA.AAAA

PC2
IP  = 192.168.1.20
MAC = BBBB.BBBB.BBBB
```

After ARP resolution, PC1 can send an Ethernet frame toward:

```text
Source MAC      = AAAA.AAAA.AAAA
Destination MAC = BBBB.BBBB.BBBB
```

The switch forwards the frame according to its MAC address table.

---

# 18. P2P and Ports 🚪

A peer application may listen on a transport-layer port.

For example, an application could use:

```text
TCP/UDP port → application endpoint
```

Remember:

```text
Physical switch port ≠ TCP/UDP port
```

This distinction is essential when troubleshooting P2P applications.

---

# 19. Advantages of P2P ✅

### Simple for small networks

A dedicated server may not be necessary.

### Low initial infrastructure

Small groups can share resources directly.

### Distributed resources

Resources can exist on multiple peers.

### Useful for temporary environments

Labs and temporary collaboration can benefit from direct sharing.

### No single dedicated resource server required

A small workgroup can share resources between its computers.

---

# 20. Disadvantages of P2P ❌

### Difficult centralized management

As the number of peers increases, administration becomes harder.

### Security management can become inconsistent

Each device may have different permissions and security settings.

### Resource availability depends on peers

If the computer hosting a shared folder is turned off, the resource may become unavailable.

### Backup can be harder

Data may be distributed across many computers.

### Scalability limitations

Large organizations usually need more centralized and structured architectures.

---

# 21. When Is P2P Appropriate?

P2P can make sense for:

- 🏠 Home networks
- 👥 Small workgroups
- 🧪 Temporary labs
- 🎓 Classroom exercises
- 🧑‍💻 Developer/test environments
- 📁 Small-scale direct sharing

It is generally less appropriate as the primary architecture for large enterprise environments requiring centralized identity, policy, backup, logging, and resource management.

---

# 22. Real-World Example — Home Network 🏠

```text
                 📡 Wi-Fi Router/AP
              /       |        \
          Laptop     PC       Phone
             \        |        /
              \── Peer sharing ──/
```

A home user may share a folder or printer between computers without deploying a dedicated file server.

The same home network can still use centralized services such as:

```text
DNS → provided by router/ISP/public resolver
DHCP → often provided by the home router
```

So one network can contain both centralized services and peer-to-peer resource sharing.

---

# 23. Real-World Example — Small Office 🏢

A five-person office might initially use:

```text
             🔀 Switch
        ┌────┼────┬────┐
       PC1  PC2  PC3  PC4
        │    │    │    │
      Share Share Share Share
```

As the business grows, it may move to:

```text
Clients → Central File Server
Clients → Authentication Server
Clients → Central Backup
```

### Lesson

Network architecture should evolve with organizational requirements.

---

# 24. Real-World Example — Distributed Applications 🌐

Some modern applications use peer-to-peer concepts to distribute communication or data among participants.

A simplified model is:

```text
Peer A ↔ Peer B
   ↕       ↕
Peer C ↔ Peer D
```

The exact implementation depends on the application protocol and may still use supporting centralized services for discovery, authentication, coordination, or other functions.

---

# 25. Windows P2P Connectivity Commands 💻

### View complete network configuration

```powershell
ipconfig /all
```

Check:

- IP address
- Subnet mask
- Default gateway
- DNS servers
- Physical address/MAC

### Test peer reachability

```powershell
ping 192.168.1.20
```

### View ARP cache

```powershell
arp -a
```

### Test hostname resolution

```powershell
nslookup PC2
```

### Trace a routed peer

```powershell
tracert 192.168.20.10
```

> Availability of local-name resolution depends on the Windows network configuration and services in use.

---

# 26. Linux P2P Connectivity Commands 🐧

### IP information

```bash
ip addr
```

### Interface information

```bash
ip link
```

### Routing

```bash
ip route
```

### Neighbor/ARP information

```bash
ip neigh
```

### Reachability

```bash
ping 192.168.1.20
```

### DNS/name lookup

```bash
nslookup pc2.example.local
dig pc2.example.local
```

### Trace a route

```bash
traceroute 192.168.20.10
```

---

# 27. Cisco IOS Verification 🔵

Cisco infrastructure may be between peers.

### Interface status

```cisco
show ip interface brief
```

### MAC address learning

```cisco
show mac address-table
```

### ARP information

```cisco
show arp
```

### Routing table

```cisco
show ip route
```

### Interface details

```cisco
show interfaces
```

### Neighbor discovery

```cisco
show cdp neighbors
show lldp neighbors
```

### Connectivity testing from a Cisco device

```cisco
ping 192.168.1.20
traceroute 192.168.20.10
```

> Exact command availability and syntax can vary by Cisco platform and IOS/IOS XE release.

---

# 28. P2P Troubleshooting Methodology 🚨

When PC1 cannot communicate with PC2, troubleshoot from the bottom up.

```text
🔌 Physical connection
        ↓
🟢 Interface status
        ↓
🏷️ VLAN / Layer 2
        ↓
📍 IP address + mask
        ↓
🛣️ Default gateway
        ↓
🧭 Routing
        ↓
📛 Name resolution
        ↓
🚪 Transport port
        ↓
🛡️ Firewall / security policy
        ↓
📁 Sharing/application service
        ↓
✅ Verify again
```

Do not immediately assume the shared application is broken.

---

# 29. Troubleshooting Scenario — Same LAN

### Problem

PC1 and PC2 are connected to the same switch but cannot share resources.

Check:

```text
1. Are both interfaces up?
2. Are both devices in the correct VLAN?
3. Do they have valid IP addresses?
4. Are they in the same subnet if direct local communication is expected?
5. Does ping work?
6. Does ARP/neighbor discovery work?
7. Is the host firewall allowing the application?
8. Is the sharing service enabled?
9. Are permissions correct?
```

Useful commands:

```powershell
ipconfig /all
ping <PC2-IP>
arp -a
```

Cisco:

```cisco
show interfaces status
show vlan brief
show mac address-table
```

---

# 30. Troubleshooting Scenario — Different Networks 🌐

### Problem

PC1 can communicate locally but cannot reach PC2 in another subnet.

Example:

```text
PC1 → 192.168.10.10/24
PC2 → 192.168.20.10/24
```

Check:

```text
PC1 gateway
      ↓
Router interfaces
      ↓
Routing table
      ↓
PC2 gateway
      ↓
Return path
```

Cisco commands:

```cisco
show ip interface brief
show ip route
show arp
ping 192.168.20.10
traceroute 192.168.20.10
```

---

# 31. Troubleshooting Scenario — Ping Works, Sharing Fails

Suppose:

```text
ping PC2
   ↓
SUCCESS
```

But:

```text
File share
   ↓
FAILURE
```

Layer 3 reachability is probably working.

Investigate:

- Host firewall
- Sharing configuration
- Authentication
- Permissions
- Application/service status
- Required transport ports
- Name resolution if using a hostname

### Key lesson

```text
Network reachability ≠ Application availability
```

---

# 32. Troubleshooting Scenario — Name Works Incorrectly 📛

Suppose:

```text
ping 192.168.1.20     → works
ping PC2              → fails
```

This suggests that IP connectivity may be working while name resolution is not.

Investigate:

```powershell
nslookup PC2
ipconfig /all
```

Also consider the specific name-resolution mechanism being used in the environment.

---

# 33. Practical Lab 1 — Direct P2P Connection 🧪

## Objective

Build a two-PC network without a switch or router.

### Topology

```text
PC1 ───────────────── PC2
```

### Addressing

| Device | IP | Mask |
|---|---|---|
| PC1 | `192.168.1.10` | `255.255.255.0` |
| PC2 | `192.168.1.20` | `255.255.255.0` |

### Tasks

1. Connect the two endpoints.
2. Configure the IP addresses.
3. Verify the interface configuration.
4. Ping PC2 from PC1.
5. Check ARP information.
6. Document the MAC addresses.

### Expected result

PC1 should be able to reach PC2 when the physical/interface configuration and addressing are correct.

---

# 34. Practical Lab 2 — P2P Through a Switch 🔀

## Topology

```text
             🔀 SW1
            /     \
          PC1     PC2
```

### Tasks

1. Connect PC1 and PC2 to SW1.
2. Assign addresses in the same subnet.
3. Ping between the PCs.
4. Inspect the switch MAC table.

Cisco verification:

```cisco
show mac address-table
show interfaces status
```

### Questions

- Which MAC address was learned on each interface?
- What happens before the switch knows the destination MAC?
- What changes after traffic is generated?

---

# 35. Practical Lab 3 — Four-Peer Small LAN 🧪

### Topology

```text
                 🔀 SW1
        ┌─────────┼─────────┐
        │         │         │
       PC1       PC2       PC3
        │                   │
        └──────── PC4 ──────┘
```

### Tasks

1. Connect four PCs to one switch.
2. Configure a common subnet.
3. Test every peer with ping.
4. Record the MAC table.
5. Identify each PC's switch port.
6. Move one PC to another switch interface.
7. Generate traffic again.
8. Observe the MAC-table change.

### Learning goal

Understand that P2P communication can exist over a switched star topology.

---

# 36. Practical Lab 4 — Routed P2P Communication 🛣️

### Topology

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ PC2
```

### Networks

```text
LAN-A = 192.168.10.0/24
LAN-B = 192.168.20.0/24
```

### Addressing

| Device | Interface | IP |
|---|---|---|
| PC1 | NIC | `192.168.10.10/24` |
| R1 | G0/0 | `192.168.10.1/24` |
| R1 | G0/1 | `192.168.20.1/24` |
| PC2 | NIC | `192.168.20.10/24` |

### Tasks

1. Configure PC1 and PC2.
2. Configure R1 interfaces.
3. Configure default gateways.
4. Verify interfaces.
5. Verify routing.
6. Ping PC2 from PC1.
7. Use traceroute/tracert.
8. Inspect ARP tables.

### Cisco verification

```cisco
show ip interface brief
show ip route
show arp
```

---

# 37. Practical Lab 5 — P2P Troubleshooting Challenge 🎯

Build:

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ PC2
```

Intentionally introduce one fault at a time:

### Fault A

Wrong subnet mask on PC1.

### Fault B

Wrong default gateway on PC1.

### Fault C

R1 interface shutdown.

### Fault D

Wrong IP address on PC2.

### Fault E

Incorrect VLAN assignment on a switch port.

### Task

For each fault:

1. Identify the symptom.
2. Gather evidence.
3. Find the root cause.
4. Apply the smallest appropriate fix.
5. Verify connectivity again.
6. Document the troubleshooting process.

---

# 38. Security Considerations 🔐

P2P sharing can create security risks when poorly managed.

Potential concerns include:

- Unauthorized file sharing
- Weak passwords
- Excessive permissions
- Malware spreading between peers
- Unpatched endpoints
- Inconsistent firewall policies
- Sensitive data stored on user devices

### Good practices

- Use strong authentication.
- Share only what is required.
- Apply least privilege.
- Keep endpoints patched.
- Use host firewalls.
- Disable unnecessary sharing.
- Separate sensitive enterprise resources from unmanaged peer shares.

> P2P architecture should not be treated as a security mechanism by itself.

---

# 39. Industry Scenario — Small Design Team 🎨

A team of four designers needs to exchange large files temporarily.

Initial setup:

```text
PC1 ─┐
PC2 ─┼── 🔀 Switch
PC3 ─┤
PC4 ─┘
```

Each designer can temporarily share files from their workstation.

As the organization grows, the team may move to:

```text
Users
  ↓
Central File Server / NAS
  ↓
Central Backup
  ↓
Access Controls
```

### Why?

Centralized storage provides better control over:

- Permissions
- Backup
- Availability
- Auditing
- Data management

---

# 40. Scenario-Based Challenge 🧠

## College Computer Lab

A college has 30 computers connected to switches.

Students want to share files directly between PCs for a temporary assignment.

### Questions

1. Could peer-to-peer sharing solve the requirement?
2. Does a P2P network require a dedicated server?
3. Can a switch be used?
4. Can the PCs be in the same VLAN?
5. What if two PCs are in different IP networks?
6. What network device may be required?
7. What security risks should the administrator consider?
8. When would a central file server become a better solution?

### Think first

Do not choose a technology just because it works technically. Consider:

```text
Scale
Security
Management
Backup
Availability
Performance
Cost
```

---

# 41. Practice Exercises ✍️

### Exercise 1

Explain P2P in your own words without using the phrase “direct connection.”

### Exercise 2

Draw three different physical topologies that could support P2P communication.

### Exercise 3

Compare these two designs:

```text
PC1 ↔ PC2
```

and

```text
PC1 ↔ Switch ↔ PC2
```

Explain what changed physically and what did not change architecturally.

### Exercise 4

A peer can ping another peer but cannot access its shared resource. List five possible causes.

### Exercise 5

Explain why P2P becomes harder to manage as the number of peers increases.

### Exercise 6

Design a small-office network with six peers and document:

- IP addresses
- subnet
- switch ports
- shared resources
- security controls

---

# 42. Interview Questions 🎤

## Beginner

1. What is peer-to-peer networking?
2. What is a peer?
3. Does P2P require a dedicated server?
4. Give two examples of P2P resource sharing.
5. Can P2P use a switch?
6. What is the difference between a peer and a server?

## Intermediate

7. Compare P2P and client-server architecture.
8. Why is P2P useful for small networks?
9. Why can P2P become difficult to manage at scale?
10. Can P2P communication occur between different IP networks?
11. What role does a switch play in a switched P2P network?
12. What role does a router play when peers are in different networks?

## Advanced

13. Explain the difference between P2P architecture and physical topology.
14. Why can a network use both client-server and P2P communication simultaneously?
15. What operational problems can occur when shared resources are distributed across many endpoints?
16. Why are centralized identity and backup services usually preferred in enterprise environments?
17. How can security policy affect P2P resource sharing?

## Troubleshooting

18. Two peers are on the same switch but cannot communicate. What would you check first?
19. Ping works but file sharing fails. What should you investigate?
20. Communication works by IP but not hostname. What is a likely area of investigation?
21. Two peers are in different subnets. What network information must be correct?
22. How would you use a MAC address table during P2P troubleshooting?
23. How would you determine whether the problem is Layer 2, Layer 3, DNS, firewall, or application-related?

---

# 43. Quick Revision ⚡

```text
🤝 Peer-to-Peer
│
├── No dedicated central server required
├── Peers can provide resources
├── Peers can consume resources
│
├── Can use many physical topologies
│   ├── Direct link
│   ├── Switched star
│   ├── Wireless LAN
│   └── Routed networks
│
├── Advantages
│   ├── Simple for small environments
│   ├── Low infrastructure requirement
│   └── Distributed resources
│
└── Limitations
    ├── Harder centralized management
    ├── Distributed security
    ├── Distributed backup
    └── Limited enterprise scalability
```

---

# 44. Cheat Sheet 📋

| Concept | Key point |
|---|---|
| P2P | Peers can provide and consume resources |
| Dedicated server | Not required for every shared resource |
| Peer | Participating device that can communicate/share resources |
| Switch | Provides Layer 2 connectivity |
| Router | Connects different IP networks |
| Same subnet | Can communicate locally using Layer 2 after address resolution |
| Different subnet | Requires Layer 3 forwarding |
| MAC | Used for local Layer 2 delivery |
| IP | Used for logical Layer 3 communication |
| Physical topology | How devices are connected |
| Architecture | How devices/services interact |
| Main advantage | Simple for small environments |
| Main limitation | Centralized management is harder |
| Security concern | Uncontrolled peer sharing can expose resources |

---

# 45. Connection to Previous and Next Topics 🔗

### Previous topic

[09 — Client-Server Architecture](09-Client-Server-Architecture.md)

You learned how clients request services from centralized or dedicated server roles.

### Next topic

➡️ **11 — Basic Network Communication**

Next, we will follow what actually happens when one device communicates with another:

```text
Application
   ↓
Transport
   ↓
IP
   ↓
Ethernet/Wi-Fi
   ↓
Switch/Router
   ↓
Destination
```

This will connect the architecture concepts you learned here to actual packet and frame movement through a network.

---

# ✅ Completion Checklist

- [ ] I understand peer-to-peer networking.
- [ ] I can explain what a peer is.
- [ ] I can compare P2P with client-server architecture.
- [ ] I understand that P2P does not require a direct cable between every peer.
- [ ] I understand P2P over a switch.
- [ ] I understand P2P over Wi-Fi.
- [ ] I understand P2P communication across routed networks.
- [ ] I can distinguish architecture from physical topology.
- [ ] I understand the advantages of P2P.
- [ ] I understand the limitations of P2P.
- [ ] I know when P2P is appropriate.
- [ ] I understand basic P2P security considerations.
- [ ] I can use Windows connectivity commands.
- [ ] I can use Linux connectivity commands.
- [ ] I can use Cisco verification commands.
- [ ] I completed the direct P2P lab.
- [ ] I completed the switched P2P lab.
- [ ] I completed the four-peer LAN lab.
- [ ] I completed the routed P2P lab.
- [ ] I completed the troubleshooting challenge.
- [ ] I can troubleshoot P2P communication systematically.
- [ ] I can explain P2P in an interview.

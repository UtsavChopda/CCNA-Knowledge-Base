# 🏢 Client-Server Architecture

> **Module:** 01 — Networking Fundamentals  
> **Learning path:** Learn → Understand → Configure → Verify → Troubleshoot → Document → Practice

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain client-server architecture in simple terms.
- Identify clients, servers, services, and protocols.
- Understand request-response communication.
- Distinguish a server role from a physical server device.
- Understand common server types used in networks.
- Compare centralized client-server and peer-to-peer models.
- Trace a simple client request through a network.
- Use basic commands to test and troubleshoot client-server communication.
- Apply the concept to real-world enterprise networks.

---

# 1. What Is Client-Server Architecture?

**Client-server architecture** is a networking model in which one device or application, called the **client**, requests a service or resource from another device or application, called the **server**.

Simple example:

```text
💻 Client
   │
   │ Request
   ▼
🖥️ Server
   │
   │ Response
   ▼
💻 Client
```

The client usually initiates communication, while the server provides a service.

### Examples

- Web browser → Web server
- Email application → Mail server
- DNS client → DNS server
- File client → File server
- DHCP client → DHCP server

---

# 2. Simple Analogy 🍽️

Think about a restaurant.

```text
👤 Customer → requests food
🧑‍🍳 Kitchen → prepares food
🧑‍💼 Waiter → carries request/response
```

In networking:

```text
💻 Client → requests a service
🌐 Network → carries the communication
🖥️ Server → provides the service
```

The analogy is not exact, but it helps you remember the basic relationship.

---

# 3. Client vs Server

| Feature | Client | Server |
|---|---|---|
| Main role | Requests service | Provides service |
| Usually initiates request | Yes | Usually responds/listens |
| Example | Web browser | Web server |
| Can be a physical computer? | Yes | Yes |
| Can be an application? | Yes | Yes |
| Uses network protocols | Yes | Yes |

> 📌 A device can act as a client for one service and a server for another.

---

# 4. A Server Is Not Necessarily a Huge Computer

One of the most important beginner concepts:

> **Server describes a role/service, not necessarily the size or physical appearance of a computer.**

A server can be:

- A physical rack server
- A virtual machine
- A cloud instance
- A container/application
- A network appliance
- A service running on a normal computer in a lab

Example:

```text
Physical Host
      │
      ├── VM1 → Web Server
      ├── VM2 → DNS Server
      └── VM3 → File Server
```

One physical machine can therefore provide multiple server roles.

---

# 5. What Is a Service?

A **service** is a function made available to clients over a network.

Examples:

```text
🌐 Web service
📛 DNS service
📧 Email service
📁 File service
🕒 Time service
🔐 Authentication service
📍 DHCP service
```

A server may provide several services simultaneously.

---

# 6. Request-Response Model 🔄

Many client-server interactions follow a request-response pattern.

```text
Client                         Server
  │                              │
  │────── Request ──────────────→│
  │                              │
  │←───── Response ──────────────│
  │                              │
```

Example: opening a website.

```text
Browser
   │
   │ HTTP/HTTPS request
   ▼
Web Server
   │
   │ Web content/response
   ▼
Browser
```

The exact exchange can involve DNS, TCP or QUIC, TLS, HTTP, routing, ARP/neighbor discovery, and many other operations.

---

# 7. Example — Opening a Website 🌐

Suppose you enter:

```text
https://example.com
```

A simplified flow is:

```text
💻 Browser
   │
   ├── 1. Need the server's IP
   │
   ▼
📛 DNS Server
   │
   └── Returns an IP address
   │
   ▼
🛣️ Network / Router
   │
   ▼
🌐 Web Server
   │
   └── Returns website content
   ▼
💻 Browser
```

This single user action can involve multiple client-server relationships.

---

# 8. Common Client-Server Examples

## 🌐 Web

```text
Browser → Web Server
```

Common protocols:

```text
HTTP
HTTPS
```

## 📛 DNS

```text
DNS Client → DNS Server
```

The client asks for name resolution.

## 📍 DHCP

```text
DHCP Client → DHCP Server
```

The client requests network configuration.

## 📧 Email

```text
Mail Client ↔ Mail Server
```

Common protocols include SMTP, IMAP, and POP3.

## 📁 File Services

```text
User PC → File Server
```

Examples include SMB and NFS environments.

## 🔐 Authentication

```text
User Device → Authentication Service
```

Enterprise environments commonly centralize identity and access services.

---

# 9. Client and Server Communication Requires a Network

Consider:

```text
💻 Client
   │
   ▼
🔀 Switch
   │
   ▼
🛣️ Router
   │
   ▼
🔥 Firewall
   │
   ▼
🖥️ Server
```

Every layer contributes to successful communication.

For example:

- Physical connection must work.
- Interfaces must be operational.
- VLANs must be correct.
- IP addressing must be valid.
- Routing must work when networks differ.
- DNS may be required for names.
- Firewall/ACL policies must permit traffic.
- The server service must actually be running.
- The client must use the correct protocol and port.

---

# 10. Server IP Address 📍

A client needs a way to reach the server.

Example:

```text
Client:
192.168.10.20

Server:
192.168.10.50
```

If both are in the same subnet, communication can occur directly at Layer 2 after the necessary address resolution.

If they are in different networks:

```text
Client
192.168.10.20
      │
      ▼
Default Gateway
      │
      ▼
Router
      │
      ▼
Server Network
192.168.20.0/24
      │
      ▼
Server
192.168.20.50
```

The router provides Layer 3 forwarding between the networks.

---

# 11. Server Ports 🚪

A server can provide multiple services using different transport-layer ports.

Common examples:

| Service | Common protocol | Common port |
|---|---|---:|
| HTTP | TCP | 80 |
| HTTPS | TCP | 443 |
| DNS | UDP/TCP | 53 |
| DHCP server | UDP | 67 |
| DHCP client | UDP | 68 |
| SSH | TCP | 22 |
| SMTP | TCP | 25 |
| IMAP | TCP | 143 |
| POP3 | TCP | 110 |

> 📌 Port numbers identify transport-layer application endpoints. They are not the same thing as physical switch ports.

---

# 12. Physical Port vs Logical Port

This distinction is extremely important.

### Physical port

Example:

```text
Switch Gi0/1
```

It is a physical network interface.

### Transport-layer port

Example:

```text
TCP 443
```

It identifies an application/service endpoint.

Think:

```text
Switch port → physical/network interface
TCP/UDP port → application communication endpoint
```

---

# 13. Server Listening

A network service usually needs to be **listening** on the expected address and port.

Example:

```text
Server
  │
  └── TCP 443 → HTTPS service listening
```

If the service is stopped:

```text
Client → Server IP:443
              ↓
        No service listening
              ↓
          Connection fails
```

Therefore, successful ping does not guarantee that a specific application service is available.

---

# 14. Ping vs Application Connectivity 🧪

Suppose:

```text
ping 192.168.20.50
```

works.

But:

```text
https://192.168.20.50
```

does not work.

Possible causes include:

- Web service is stopped.
- TCP 443 is blocked.
- TLS/application problem.
- Server application configuration is incorrect.
- The server expects a hostname.
- Firewall policy blocks the service.

### Key lesson

```text
Ping works
    ≠
Every application works
```

---

# 15. Client-Server vs Peer-to-Peer

| Feature | Client-Server | Peer-to-Peer |
|---|---|---|
| Central server | Usually yes | Not required |
| Management | Centralized | More distributed |
| Scalability | Generally better for centralized services | Can become difficult at larger scale |
| Administration | Centralized | Distributed |
| Example | Company file server | Two PCs sharing files directly |

Both models are useful. The correct choice depends on requirements.

---

# 16. Centralized Enterprise Example 🏢

A company may have:

```text
                         🖥️ DNS
                           │
                           │
💻 User PCs ──── 🔀 Access Switch ──── 🔐 Identity Server
                           │
                           ├──────────── 📁 File Server
                           │
                           ├──────────── 🌐 Web Applications
                           │
                           └──────────── 📍 DHCP Server
```

Users access centralized services while network administrators can manage infrastructure more consistently.

---

# 17. Network Topology — Client-Server Lab 🗺️

## Topology 1 — Same LAN

```text
              ┌───────────────┐
              │      SW1      │
              └───┬─────┬─────┘
                  │     │
                 PC1   SERVER1
                  │     │
             Client   Server
```

Example addressing:

| Device | Role | IP |
|---|---|---|
| PC1 | Client | `192.168.10.10/24` |
| SERVER1 | Server | `192.168.10.50/24` |

### Tasks

1. Configure both IP addresses.
2. Verify interfaces.
3. Ping the server from PC1.
4. Enable/configure a simple server service in Packet Tracer.
5. Test the service from the client.

---

# 18. Network Topology — Different Networks 🌐

```text
PC1
192.168.10.10/24
   │
   ▼
SW1
   │
   ▼
R1
G0/0: 192.168.10.1/24
G0/1: 192.168.20.1/24
   │
   ▼
SW2
   │
   ▼
SERVER1
192.168.20.50/24
```

### Addressing Table

| Device | Interface | IP | Gateway |
|---|---|---|---|
| PC1 | NIC | `192.168.10.10/24` | `192.168.10.1` |
| R1 | G0/0 | `192.168.10.1/24` | — |
| R1 | G0/1 | `192.168.20.1/24` | — |
| SERVER1 | NIC | `192.168.20.50/24` | `192.168.20.1` |

### Verification

From PC1:

```text
ping 192.168.20.50
```

On R1:

```cisco
show ip interface brief
show ip route
```

### Learning point

The router is required because the client and server are in different IP networks.

---

# 19. Network Topology — Enterprise Client-Server 🌍

```text
                         🌐 Internet
                              │
                              ▼
                         🔥 Firewall
                              │
                              ▼
                         🛣️ Router
                              │
                              ▼
                       🔀 Core/Distribution
                              │
                ┌─────────────┼─────────────┐
                │             │             │
                ▼             ▼             ▼
          🔀 Access SW   🔀 Access SW   🔀 Access SW
                │             │             │
             💻💻💻         💻💻💻         💻💻💻
                │
                └──────────────┐
                               ▼
                         🖥️ Server Farm
                         ├── DNS
                         ├── DHCP
                         ├── File
                         └── Application
```

This illustrates how a simple client-server relationship can exist inside a much larger enterprise network.

---

# 20. Basic Windows Commands 💻

### View IP configuration

```powershell
ipconfig /all
```

Useful for checking:

- IP address
- Subnet mask
- Default gateway
- DNS servers
- MAC address

### Test reachability

```powershell
ping 192.168.20.50
```

### Trace the path

```powershell
tracert 192.168.20.50
```

### Resolve a hostname

```powershell
nslookup example.com
```

### View ARP cache

```powershell
arp -a
```

---

# 21. Linux Commands 🐧

### Address information

```bash
ip addr
```

### Routing table

```bash
ip route
```

### Test reachability

```bash
ping 192.168.20.50
```

### Trace route

```bash
traceroute 192.168.20.50
```

### DNS lookup

```bash
nslookup example.com
dig example.com
```

### Neighbor information

```bash
ip neigh
```

---

# 22. Cisco IOS Verification Commands 🔵

Cisco devices may not be the client or server themselves, but they are often between them.

### Interface status

```cisco
show ip interface brief
```

### Routing table

```cisco
show ip route
```

### ARP information

```cisco
show arp
```

### Test from the network device

```cisco
ping 192.168.20.50
```

### Trace path

```cisco
traceroute 192.168.20.50
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

> Command availability and exact syntax can vary by Cisco platform and IOS/IOS XE release.

---

# 23. Verification Checklist 🔎

When testing a client-server connection, verify in layers:

```text
1️⃣ Physical
   ↓
2️⃣ Interface status
   ↓
3️⃣ VLAN / Layer 2
   ↓
4️⃣ IP address
   ↓
5️⃣ Default gateway
   ↓
6️⃣ Routing
   ↓
7️⃣ DNS, if hostname is used
   ↓
8️⃣ TCP/UDP port
   ↓
9️⃣ Server service
   ↓
🔟 Application
```

This prevents random troubleshooting.

---

# 24. Troubleshooting Scenario 🚨

### Problem

A user says:

> “I cannot open the company website.”

### Step 1 — Check IP configuration

```powershell
ipconfig /all
```

### Step 2 — Test gateway

```powershell
ping 192.168.10.1
```

### Step 3 — Test server IP

```powershell
ping 192.168.20.50
```

### Step 4 — Test DNS

```powershell
nslookup company.example
```

### Step 5 — Check path

```powershell
tracert 192.168.20.50
```

### Step 6 — Check application port/service

If network reachability works but the application does not, investigate the server service, firewall, port, and application configuration.

---

# 25. Common Problems and Causes

| Symptom | Possible cause |
|---|---|
| Cannot ping server | Physical, VLAN, IP, routing, ACL/firewall |
| Ping works but website fails | Web service/port/application issue |
| Hostname fails but IP works | DNS problem |
| Same-LAN communication fails | VLAN, interface, addressing, Layer 2 issue |
| Remote-network communication fails | Gateway/routing issue |
| Server reachable but login fails | Authentication/application issue |
| Connection times out | Routing, firewall, service, or filtering |
| Connection refused | Host reachable but service/port may not be accepting connections |

---

# 26. Industry Scenario 🏦

A bank has thousands of employee computers.

Users access:

```text
💻 Employee PC
      │
      ▼
🔐 Authentication Server
      │
      ▼
📁 File Services
      │
      ▼
🏦 Banking Applications
```

Centralized servers allow the organization to manage authentication, applications, data, and access policies consistently.

The network engineer's job is not simply to make `ping` work. The goal is reliable communication between clients and required services while maintaining performance and security.

---

# 27. Scenario-Based Challenge 🎯

## Hospital Network

A hospital has:

- 200 employee PCs
- Central DNS
- DHCP servers
- Application servers
- File servers
- Authentication services
- Network printers

A nurse can access the Internet but cannot open the internal patient-management application.

### Your investigation

Answer before checking the solution:

1. Is the client receiving a valid IP address?
2. Can it reach its default gateway?
3. Can it reach the application server IP?
4. Does DNS resolve the application hostname?
5. Is the correct TCP/UDP port reachable?
6. Is the application service running?
7. Could an ACL/firewall block the service?

### Expected troubleshooting mindset

Do not immediately blame the server.

Walk through:

```text
Client
  ↓
Local network
  ↓
Gateway
  ↓
Routing
  ↓
Firewall/ACL
  ↓
Server IP
  ↓
Port
  ↓
Application
```

---

# 28. Practical Lab 🧪

## Lab 1 — Basic Client-Server

### Objective

Build a small LAN containing a client and server.

### Topology

```text
PC1 ───────── SW1 ───────── SERVER1
```

### Tasks

1. Add PC1, SW1, and Server1.
2. Connect them with Ethernet links.
3. Assign IP addresses in the same subnet.
4. Test PC1 → Server1 with ping.
5. Configure a simple Packet Tracer server service.
6. Test the service from PC1.
7. Document the results.

### Verification

Record:

```text
PC1 IP:
PC1 MAC:
SERVER1 IP:
SERVER1 MAC:
Ping result:
Application result:
```

---

# 29. Practical Lab 🧪

## Lab 2 — Routed Client-Server

### Objective

Understand how a router enables communication between different IP networks.

### Topology

```text
PC1 ─ SW1 ─ R1 ─ SW2 ─ SERVER1
```

### Networks

```text
Client LAN  → 192.168.10.0/24
Server LAN  → 192.168.20.0/24
```

### Tasks

1. Configure PC1.
2. Configure SERVER1.
3. Configure R1 interfaces.
4. Configure default gateways.
5. Verify R1 interfaces.
6. Verify R1 routing table.
7. Ping the server from PC1.
8. Test the server service.

### Cisco verification

```cisco
show ip interface brief
show ip route
show arp
```

---

# 30. Practical Lab 🧪

## Lab 3 — DNS + Web Client-Server

### Topology

```text
             ┌───────────────┐
             │      SW1      │
             └──┬─────┬──────┘
                │     │
               PC1   DNS/Web Server
```

### Goal

Access a web service by hostname rather than directly by IP.

### Tasks

1. Configure the client IP settings.
2. Configure the server IP settings.
3. Configure DNS service.
4. Create an appropriate DNS record in the lab.
5. Configure the web service.
6. Verify DNS resolution.
7. Open the web service from the client.
8. Troubleshoot any failure systematically.

### Learning point

This lab demonstrates that application access can depend on multiple services:

```text
DNS
 ↓
IP connectivity
 ↓
Transport port
 ↓
Web service
```

---

# 31. Common Beginner Mistakes ❌

### ❌ Mistake 1: “Server means a special expensive computer.”

Server describes a role/service. Physical hardware is only one implementation.

### ❌ Mistake 2: “If ping works, the server is working.”

Ping tests IP-level reachability, not every application service.

### ❌ Mistake 3: “Port 443 is a switch port.”

TCP/UDP port 443 is a transport-layer application endpoint. It is not a physical interface.

### ❌ Mistake 4: “The client always communicates directly with the server.”

Traffic may pass through switches, routers, firewalls, load balancers, proxies, VPNs, and other infrastructure.

### ❌ Mistake 5: “DNS and the web server are the same service.”

They are separate services that can run on the same or different systems.

### ❌ Mistake 6: “A server can only serve one client.”

Servers are designed to handle requests from multiple clients, subject to their resources and configuration.

---

# 32. Practice Exercises ✍️

### Exercise 1

Explain the difference between:

```text
Client
Server
Service
Protocol
Port
```

### Exercise 2

Draw a topology showing:

```text
5 clients
1 switch
1 router
1 server
```

Label the IP networks.

### Exercise 3

A client can ping the server IP but cannot open HTTPS.

List at least five possible causes.

### Exercise 4

A client can access a website by IP address but not by hostname.

What component should you investigate first?

### Exercise 5

Explain why a router is required when the client and server are in different IP networks.

---

# 33. Interview Questions 🎤

## Beginner

1. What is client-server architecture?
2. What is a client?
3. What is a server?
4. What is a network service?
5. Give three examples of client-server communication.
6. Is a server always a physical machine?

## Intermediate

7. What happens when a client sends a request to a server in another network?
8. What is the difference between a physical port and a TCP/UDP port?
9. Why can ping work while an application fails?
10. What is the request-response model?
11. Why is DNS important for many client-server applications?
12. Can one machine act as both client and server?

## Advanced

13. Explain the full path of an HTTPS request from a client to a remote server.
14. How do routing and firewall policies affect client-server communication?
15. Why can a server have multiple IP addresses and services?
16. How can virtualization change the traditional client-server model?
17. What is the difference between service availability and network reachability?

## Troubleshooting

18. A user can ping a server but cannot connect to TCP 443. What would you check?
19. A user can connect by IP but not by hostname. What is likely wrong?
20. A remote server is unreachable. What commands would you run first?
21. How would you determine whether the problem is on the client, network, firewall, or server?
22. What does a connection refusal suggest compared with a timeout?

---

# 34. Quick Revision ⚡

```text
🏢 Client-Server
│
├── Client
│   └── Requests a service
│
├── Server
│   └── Provides a service
│
├── Service
│   └── Function available over the network
│
├── Communication
│   └── Request ↔ Response
│
├── Common examples
│   ├── Web
│   ├── DNS
│   ├── DHCP
│   ├── Email
│   └── File services
│
└── Troubleshooting
    ├── Physical
    ├── Layer 2
    ├── IP
    ├── Routing
    ├── DNS
    ├── Port
    ├── Firewall
    └── Application
```

---

# 35. Cheat Sheet 📋

| Concept | Key point |
|---|---|
| Client | Requests a service |
| Server | Provides a service |
| Service | Network-accessible function |
| Request | Client asks for something |
| Response | Server returns result/data |
| Server role | Can run on physical, virtual, cloud, or other platforms |
| TCP/UDP port | Application communication endpoint |
| Physical port | Network interface/connection |
| Ping | Tests basic IP reachability |
| DNS | Resolves names to IP information |
| Gateway | Provides Layer 3 path off the local network |
| Firewall | Can permit or block traffic |
| Application | Final service consumed by the user |

---

# 36. Connection to Previous and Next Topics 🔗

### Previous topic

[08 — Switching Fundamentals](08-Switching-Fundamentals.md)

Switches provide the Layer 2 connectivity that allows clients and servers on a LAN to communicate.

### Next topic

➡️ **10 — Peer-to-Peer Networking**

Next you will compare centralized client-server communication with direct peer-to-peer communication.

This comparison will help you understand why organizations use centralized services while small networks can sometimes use direct device-to-device sharing.

---

# ✅ Completion Checklist

- [ ] I understand client-server architecture.
- [ ] I can explain client vs server.
- [ ] I understand what a service is.
- [ ] I know that a server can be physical, virtual, cloud-based, or application-based.
- [ ] I understand request-response communication.
- [ ] I know common client-server examples.
- [ ] I understand physical ports vs TCP/UDP ports.
- [ ] I understand why ping does not prove application availability.
- [ ] I understand how routing affects remote server access.
- [ ] I can use basic Windows networking commands.
- [ ] I can use basic Linux networking commands.
- [ ] I can use basic Cisco verification commands.
- [ ] I completed the same-LAN client-server lab.
- [ ] I completed the routed client-server lab.
- [ ] I completed the DNS/web lab.
- [ ] I can troubleshoot client-server connectivity systematically.
- [ ] I can explain the concept in an interview.

# 🌐 32 — DNS Fundamentals & Name Resolution

> **Welcome to the Internet's Phonebook.** 📖🌐
>
> Humans remember names. Networks ultimately communicate using IP addresses.
>
> DNS connects the two worlds.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain what DNS is and why it exists
- 🔤 Understand hostname-to-IP resolution
- 🌍 Explain the DNS hierarchy
- 🧩 Understand recursive and iterative DNS queries
- 📦 Identify common DNS record types
- 🔄 Explain forward and reverse lookups
- 🧠 Understand caching and TTL
- 🖥️ Identify DNS client, resolver and authoritative server roles
- 💻 Use Windows and Linux DNS troubleshooting commands
- 🔵 Configure basic DNS settings on Cisco IOS where applicable
- 🧪 Build a practical DNS lab in Packet Tracer
- 🚨 Troubleshoot common DNS failures systematically
- 🏢 Relate DNS to enterprise networks and cybersecurity
- 🎤 Answer CCNA-level DNS questions confidently

---

# 🧭 1. Where We Are

```text
30 📦 OSPF Advanced Operations
        ↓
31 📨 DHCP
        ↓
32 📖 DNS  ← YOU ARE HERE
        ↓
33 🔐 ACL
```

DHCP answers:

> **“What network configuration should this device use?”**

DNS answers:

> **“What IP address belongs to this name?”**

Together they are fundamental network services.

---

# 🧠 2. What Is DNS?

**DNS = Domain Name System.**

DNS is a distributed naming system used to translate domain names and hostnames into information such as IP addresses.

For example:

```text
Human-friendly name
        ↓
www.example.com
        ↓
DNS resolution
        ↓
IP address
        ↓
Network communication
```

Instead of remembering an IP address for every service, users can work with meaningful names.

---

# 📱 3. Real-World Analogy — Contacts App

Imagine your phone contacts.

You remember:

```text
👤 Mom
👤 Friend
👤 College
```

Your phone internally associates those names with phone numbers.

DNS works similarly:

```text
🌐 www.example.com
        ↓
      DNS
        ↓
      IP address
```

The important difference is that DNS is a distributed network service rather than a single contact list.

---

# 🔥 4. Why Do We Need DNS?

Without DNS, users would frequently need to remember numerical addresses.

Compare:

```text
❌ 203.0.113.25

✅ www.company.example
```

DNS provides:

- 🧠 Human-readable names
- 🔄 Flexible IP changes
- ⚡ Caching for faster responses
- 🌍 Distributed global naming
- 🏢 Internal enterprise naming
- 📧 Service discovery through specialized records

DNS is therefore much more than a simple “name to IP converter.”

---

# 🧩 5. The Main DNS Players

A typical DNS lookup can involve several roles.

```text
💻 Client
   ↓
🔎 Stub Resolver
   ↓
🧑‍💻 Recursive Resolver
   ↓
🌳 DNS Hierarchy
   ↓
🏛️ Authoritative DNS Server
```

### 💻 DNS Client

The endpoint requesting DNS information.

Examples:

- Laptop
- Desktop
- Phone
- Server
- Network appliance

### 🔎 Stub Resolver

The software component on the endpoint that starts the lookup and asks a configured DNS resolver for the answer.

### 🧑‍💻 Recursive Resolver

The resolver performs the work of finding the answer on behalf of the client when it does not already have a valid cached response.

Examples include enterprise, ISP and public recursive DNS services.

### 🏛️ Authoritative DNS Server

An authoritative server holds DNS data for a zone and provides authoritative answers for names in that zone.

---

# 🌳 6. DNS Hierarchy

DNS is hierarchical.

Think of it as an inverted tree:

```text
                    .
                  Root
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
       .com       .org       .in
        │
        ↓
     example
        │
        ↓
       www
```

The major levels are:

```text
Root
 ↓
Top-Level Domain (TLD)
 ↓
Domain
 ↓
Subdomain / Hostname
```

---

# 🌐 7. Root DNS Servers

The DNS root is represented by:

```text
.
```

Root DNS infrastructure does not normally contain the final IP address for every website.

Instead, it helps direct DNS resolution toward the appropriate top-level-domain infrastructure.

Conceptually:

```text
Client
  ↓
Resolver
  ↓
Root
  ↓
TLD
  ↓
Authoritative server
  ↓
Answer
```

---

# 🏷️ 8. Top-Level Domains

Examples include:

```text
.com
.org
.net
.in
.edu
```

A TLD is part of the DNS hierarchy above the registered domain.

For:

```text
www.example.com
```

```text
www    = hostname/label
example = domain label
com    = TLD
.      = root
```

---

# 🔎 9. What Happens When You Type a Website Name?

Suppose you enter:

```text
https://www.example.com
```

A simplified process is:

```text
1️⃣ Browser needs an address
        ↓
2️⃣ OS/client checks local information
        ↓
3️⃣ Query is sent to configured DNS resolver
        ↓
4️⃣ Resolver checks its cache
        ↓
5️⃣ If necessary, resolver queries DNS hierarchy
        ↓
6️⃣ Authoritative server supplies the record
        ↓
7️⃣ Resolver returns the answer
        ↓
8️⃣ Client connects to the returned IP
```

DNS happens before the application can normally establish the intended connection to the resolved address.

---

# 🔄 10. Recursive vs Iterative Queries

This distinction is important.

### Recursive Query

The client asks a resolver:

> “Please find the final answer for me.”

```text
Client
  │
  │ recursive request
  ▼
Resolver
  │
  │ does the lookup work
  ▼
Answer
```

### Iterative Query

A DNS server responds with the best information or referral it currently has, allowing the requester to continue the process.

Conceptually:

```text
Resolver → Root
Root → “Ask the TLD servers”
Resolver → TLD
TLD → “Ask this authoritative server”
Resolver → Authoritative server
Authoritative → Answer
```

---

# ⚡ 11. DNS Caching

DNS lookups do not have to start from zero every time.

Resolvers cache responses for a period determined largely by **TTL (Time To Live)**.

Example:

```text
www.example.com
        ↓
IP = 203.0.113.25
TTL = 300 seconds
```

While the cached information remains valid, the resolver can answer from cache instead of querying upstream servers again.

Benefits:

- ⚡ Faster responses
- 🌐 Less DNS traffic
- 🏗️ Lower load on authoritative infrastructure

---

# ⏳ 12. TTL Does Not Mean IP Lifetime

A common beginner mistake is thinking DNS TTL means:

> “The IP address expires after this time.”

It actually controls how long a DNS response may generally be cached before it needs to be refreshed according to DNS rules.

```text
DNS TTL
   ≠
IP address lease
```

Compare this with DHCP:

```text
DHCP lease → client address assignment
DNS TTL    → DNS response caching
```

---

# 📦 13. Important DNS Record Types

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| CNAME | Alias for another canonical name |
| MX | Identifies mail servers for a domain |
| NS | Identifies authoritative name servers |
| PTR | Used for reverse DNS mapping |
| SOA | Contains authoritative zone information |
| TXT | Stores text data used for various purposes |

You should know these at a practical CCNA level.

---

# 🅰️ 14. A Record

An **A record** maps a DNS name to an IPv4 address.

Example:

```text
server.example.com → 192.0.2.10
```

Conceptually:

```text
Hostname
   ↓
A record
   ↓
IPv4 address
```

---

# 6️⃣ 15. AAAA Record

An **AAAA record** maps a name to an IPv6 address.

Example:

```text
server.example.com
        ↓
AAAA
        ↓
2001:db8::10
```

Memory trick:

```text
A     → IPv4
AAAA  → IPv6
```

---

# 🔗 16. CNAME Record

A **CNAME** creates an alias to another DNS name.

Example:

```text
portal.example.com
        ↓
CNAME
        ↓
web01.example.com
```

The alias points to a name rather than directly storing the target IP in the CNAME itself.

---

# 📧 17. MX Record

An **MX (Mail Exchange)** record identifies mail servers responsible for receiving email for a domain.

Conceptually:

```text
example.com
     ↓
   MX
     ↓
mail.example.com
```

MX records include a priority value; lower preference values generally indicate higher priority.

---

# 🏛️ 18. NS Record

An **NS record** identifies name servers authoritative for a DNS zone.

```text
example.com
     ↓
    NS
     ↓
ns1.example.com
ns2.example.com
```

This is a key part of delegation and authoritative DNS operation.

---

# 🔄 19. Forward DNS Lookup

Forward lookup means:

```text
Name → Address
```

Example:

```text
server.example.com
        ↓
192.0.2.10
```

A records and AAAA records are commonly involved.

---

# 🔁 20. Reverse DNS Lookup

Reverse DNS asks:

```text
Address → Name
```

For IPv4, reverse DNS uses the special:

```text
in-addr.arpa
```

For IPv6, reverse DNS uses:

```text
ip6.arpa
```

The relevant record type is:

```text
PTR
```

Memory trick:

```text
Forward  → A / AAAA
Reverse  → PTR
```

---

# 🧪 21. DNS Query Example

Suppose a client asks:

```text
What is the IPv4 address of web.example.com?
```

A simplified exchange is:

```text
💻 Client
   │
   │ Query: web.example.com A
   ▼
🧑‍💻 Recursive Resolver
   │
   │ cache miss
   ▼
🌳 DNS hierarchy
   │
   ▼
🏛️ Authoritative Server
   │
   │ A = 192.0.2.20
   ▼
🧑‍💻 Resolver
   │
   ▼
💻 Client
```

The client can then use the returned address for the application connection.

---

# 🚪 22. DNS Ports and Transport

DNS traditionally uses:

```text
UDP 53
```

UDP is commonly used for ordinary DNS queries because it is lightweight.

DNS can also use:

```text
TCP 53
```

TCP is important in cases such as larger DNS responses and DNS zone transfers, among other situations.

📌 Do not memorize “DNS is only UDP.”

The exam-friendly rule is:

```text
DNS → Port 53
Usually UDP
TCP also exists
```

---

# 🔐 23. DNS and Security

DNS is critical infrastructure and can be abused.

Examples of security concerns include:

- 🎭 DNS spoofing
- 🕳️ DNS cache poisoning
- 🚨 Malicious domains
- 🎣 Phishing infrastructure
- 🛰️ DNS tunneling
- 🔄 Unauthorized DNS changes

For a SOC analyst, DNS logs can be extremely valuable because suspicious domain lookups can reveal malware activity, command-and-control infrastructure, or user interaction with malicious sites.

---

# 🏢 24. Enterprise DNS Design

A company may have:

```text
                  🌐 Internet
                       │
                Public DNS
                       │
             ┌─────────┴─────────┐
             │                   │
        External users       Internet
             │
             ▼
       Public services

          Internal network
                │
                ▼
        Internal DNS servers
          ┌─────┴─────┐
          ↓           ↓
       Servers      Clients
```

Enterprise DNS may provide:

- Internal hostname resolution
- Internet name resolution
- Split-horizon DNS designs
- Service discovery
- Centralized logging and security controls

---

# 💻 25. Windows DNS Commands

### `nslookup`

Basic DNS query tool:

```powershell
nslookup example.com
```

Query a specific record type:

```powershell
nslookup -type=MX example.com
```

Inspect the configured DNS server and query results:

```powershell
nslookup
```

---

# 🐧 26. Linux DNS Commands

### `dig`

```bash
dig example.com
```

Query an A record:

```bash
dig example.com A
```

Query AAAA:

```bash
dig example.com AAAA
```

Query MX:

```bash
dig example.com MX
```

Reverse lookup:

```bash
dig -x 192.0.2.10
```

Other useful commands can include:

```bash
host example.com
resolvectl status
```

---

# 🔵 27. Cisco IOS DNS-Related Commands

Cisco IOS devices can perform DNS lookups when DNS lookup functionality is enabled and a name server is configured.

Example:

```cisco
ip name-server 192.0.2.53
```

Then an administrator can use hostname-based commands where supported.

Useful verification includes:

```cisco
show running-config | include name-server
```

You may also encounter:

```cisco
show hosts
```

which displays locally known host information.

📌 DNS server configuration on Cisco IOS is different from configuring a full DNS server. Do not confuse the two.

---

# 🧪 28. Packet Tracer DNS Lab

## 🎯 Objective

Build a small network where a PC obtains its IPv4 configuration using DHCP and resolves an internal hostname through a DNS server.

## 🗺️ Topology

```text
💻 PC1
   │
   │
   ▼
🔀 Switch
   │
   ├──────────────► 🖥️ DHCP Router
   │
   └──────────────► 🖥️ DNS Server
```

## 📊 Example Addressing

| Device | Address |
|---|---|
| Router | 192.168.10.1/24 |
| DNS Server | 192.168.10.10/24 |
| PC1 | DHCP |
| DNS name | server.lab.local |
|

Configure the DNS server with an A record such as:

```text
server.lab.local → 192.168.10.10
```

Configure DHCP so the client receives:

```text
IP address
Subnet mask
Default gateway
DNS server = 192.168.10.10
```

---

# 🔎 29. Verify the DNS Lab

On the PC:

```text
IP address received?       ✅
Default gateway received?  ✅
DNS server received?       ✅
```

Then test connectivity:

```text
ping 192.168.10.10
```

Then test name resolution:

```text
ping server.lab.local
```

If the IP ping works but the hostname fails, suspect DNS rather than basic Layer 2/Layer 3 connectivity.

---

# 🚨 30. DNS Troubleshooting Method

Use this sequence:

```text
Client has an IP?
      ↓
Correct gateway?
      ↓
Correct DNS server configured?
      ↓
Can client reach DNS server?
      ↓
Does DNS server have the record?
      ↓
Is the record correct?
      ↓
Could stale cache/TTL be involved?
      ↓
Retest name resolution
```

This prevents blindly changing DNS settings when the actual problem is basic connectivity.

---

# 🐛 31. Common DNS Problems

### Problem 1 — Website name fails but IP works

Likely areas:

```text
DNS server configuration
DNS reachability
DNS record
Resolver problem
```

### Problem 2 — DNS server cannot be reached

Check:

```text
IP addressing
VLAN
Switch port
Routing
ACL/firewall
```

### Problem 3 — Wrong IP returned

Check:

```text
A/AAAA record
DNS zone
Cache
TTL
```

### Problem 4 — Internal name does not resolve

Check:

```text
Internal DNS zone
Client DNS settings
Search suffixes where relevant
Authoritative records
```

### Problem 5 — Some users resolve correctly and others do not

Investigate:

```text
Different DNS resolvers
Caching
DHCP-provided DNS settings
Network segmentation
Resolver health
```

---

# 🧠 32. Classic Troubleshooting Scenario

A user says:

> “The Internet is down.”

You test:

```text
ping 8.8.8.8
```

✅ Works.

Then:

```text
ping example.com
```

❌ Fails.

This strongly suggests that basic IP connectivity exists while DNS/name resolution may be the problem.

Next checks:

```text
DNS server configured?
DNS server reachable?
DNS service responding?
Record resolving?
```

This is a classic network troubleshooting distinction.

---

# ⚠️ 33. Common Beginner Mistakes

### ❌ Mistake 1

Thinking DNS assigns IP addresses.

✅ DHCP normally handles dynamic host configuration.

### ❌ Mistake 2

Thinking DNS is only for websites.

✅ DNS is used by many applications and internal services.

### ❌ Mistake 3

Thinking DNS only uses UDP.

✅ DNS can use both UDP and TCP on port 53.

### ❌ Mistake 4

Confusing CNAME with an IP address record.

✅ CNAME points to another DNS name.

### ❌ Mistake 5

Confusing PTR with A.

```text
A   → name to IPv4
PTR → reverse mapping
```

### ❌ Mistake 6

Changing DNS when the client cannot even reach the DNS server.

✅ Verify connectivity first.

---

# 🏢 34. Real-World Enterprise Scenario

A company has:

```text
🏢 Headquarters
🏭 Factory
🏬 Branch offices
☁️ Cloud services
🌐 Public web services
```

Users need to access:

```text
files.company.internal
hr.company.internal
vpn.company.com
www.company.com
```

A mature design separates internal and external DNS responsibilities appropriately and applies security controls to DNS infrastructure.

If DNS fails, users may report that:

```text
🌐 Websites don't work
📁 File servers disappear
📧 Email fails
🔐 VPN names don't resolve
🖥️ Applications cannot find services
```

Therefore DNS is a critical dependency even when the underlying IP network is healthy.

---

# 🎯 35. Scenario Challenge — DNS Detective

You are the network engineer.

A workstation has:

```text
IP:       192.168.10.50
Mask:     255.255.255.0
Gateway:  192.168.10.1
DNS:      192.168.10.53
```

The user reports:

```text
ping 192.168.10.1        ✅
ping 192.168.10.53       ❌
ping example.com         ❌
```

### Your task

Determine the most logical troubleshooting direction.

Do not immediately blame the DNS record.

Think:

```text
Can the client reach the DNS server?
        ↓
NO ❌
        ↓
Investigate connectivity to DNS
        ↓
Routing / VLAN / ACL / server availability
```

### Challenge Extension

Now suppose:

```text
ping 192.168.10.53       ✅
ping 192.168.10.1        ✅
ping example.com         ❌
```

Now investigate DNS service, configuration, records and resolver behavior.

---

# 🎤 36. CCNA Interview Questions

### 🟢 Beginner

**Q1. What is DNS?**

A distributed naming system used to resolve names into DNS information such as IP addresses.

**Q2. What port does DNS use?**

Port 53 using UDP and, when required, TCP.

**Q3. What is an A record?**

A record maps a name to an IPv4 address.

**Q4. What is an AAAA record?**

AAAA maps a name to an IPv6 address.

### 🟡 Intermediate

**Q5. What is a recursive resolver?**

A DNS resolver that performs the lookup work on behalf of a client when it needs to obtain an answer.

**Q6. What is DNS caching?**

Temporary storage of DNS responses so future queries can often be answered without repeating the complete lookup process.

**Q7. What is TTL?**

A value that controls how long DNS information may generally remain cached.

### 🔴 Troubleshooting

**Q8. A user can ping an IP but cannot access the hostname. What would you check?**

DNS configuration, DNS server reachability, DNS service availability and the relevant DNS record.

**Q9. What is reverse DNS?**

Resolving an IP address to a DNS name, commonly using PTR records.

**Q10. What is the difference between DHCP and DNS?**

DHCP provides host network configuration dynamically; DNS provides name-resolution services.

---

# ⚡ 37. Quick Revision

```text
DNS = Domain Name System

Port = 53

A     → IPv4
AAAA  → IPv6
CNAME → Alias
MX    → Mail
NS    → Name server
PTR   → Reverse DNS
SOA   → Zone authority information
TXT   → Text data

Forward:
Name → Address

Reverse:
Address → Name

UDP 53:
Common DNS queries

TCP 53:
Also used by DNS when required, including zone transfers

TTL:
Controls DNS caching lifetime

DHCP:
Provides IP configuration

DNS:
Resolves names / DNS records
```

---

# 📋 38. DNS Cheat Sheet

| Task | Command / Concept |
|---|---|
| Windows lookup | `nslookup example.com` |
| Windows MX lookup | `nslookup -type=MX example.com` |
| Linux lookup | `dig example.com` |
| Linux A record | `dig example.com A` |
| Linux AAAA | `dig example.com AAAA` |
| Linux MX | `dig example.com MX` |
| Reverse lookup | `dig -x <IP>` |
| Cisco DNS server | `ip name-server <IP>` |
| Cisco local hosts | `show hosts` |
| DNS port | UDP/TCP 53 |
| IPv4 record | A |
| IPv6 record | AAAA |
| Reverse record | PTR |
| Mail record | MX |
| Alias | CNAME |
| Name servers | NS |

---

# 🧠 39. One-Minute Mental Model

Remember DNS as:

```text
                 🌐 DNS
                   │
          ┌────────┴────────┐
          ↓                 ↓
       Naming             Records
          │                 │
          ↓                 ↓
   www.example.com       A / AAAA
          │              MX / NS
          ↓              PTR / CNAME
      Resolver
          │
          ↓
      IP / DNS data
          │
          ↓
     Application
```

The simplest mental model is:

> **DHCP tells the device how to participate in the network. DNS helps the device find services by name.**

---

# 🏁 40. Final Takeaways

Before moving on, make sure you can explain these without notes:

- What DNS is
- Why DNS exists
- Client vs resolver vs authoritative server
- DNS hierarchy
- Recursive vs iterative queries
- DNS caching and TTL
- A vs AAAA
- CNAME
- MX
- NS
- PTR
- Forward vs reverse lookup
- UDP/TCP port 53
- Basic `nslookup` and `dig`
- DNS troubleshooting methodology
- DHCP vs DNS

If you can explain the complete journey:

```text
💻 Client
   ↓
🔎 Stub Resolver
   ↓
🧑‍💻 Recursive Resolver
   ↓
🌳 DNS Hierarchy
   ↓
🏛️ Authoritative Server
   ↓
📦 DNS Answer
   ↓
💻 Client
   ↓
🌐 Application Connection
```

you have the foundation needed for the next network-service topic.

---

# 🚀 Next Module

```text
32 📖 DNS Fundamentals
        ↓
33 🔐 Access Control Lists (ACL)
        ↓
34 🌍 NAT
        ↓
35 📡 Wireless
        ↓
36 🛡️ Network Security
```

**Next stop: 🔐 ACL — controlling which traffic is allowed or denied.**

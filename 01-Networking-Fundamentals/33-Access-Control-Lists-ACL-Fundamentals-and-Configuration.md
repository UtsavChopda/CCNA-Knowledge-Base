# 🔐 33 — Access Control Lists (ACL) Fundamentals & Cisco Configuration

> **Welcome to the Network Security Gate.** 🚧🛡️
>
> A router can route traffic. An ACL decides whether specific traffic should be permitted or denied.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain what an ACL is and why it is used
- 🔍 Understand how Cisco ACLs process packets
- 🟢 Configure standard IPv4 ACLs
- 🔵 Configure extended IPv4 ACLs
- 🏷️ Understand numbered and named ACLs
- 🎯 Understand source/destination matching
- 🃏 Understand wildcard masks
- 🚫 Explain the implicit deny
- 📍 Choose appropriate ACL placement
- 🔎 Verify ACLs and packet matches
- 🐛 Troubleshoot ACL-related connectivity failures
- 🧪 Build practical Packet Tracer ACL labs
- 🏢 Apply ACLs to realistic enterprise scenarios
- 🎤 Answer CCNA ACL questions confidently

---

# 🧭 1. Where We Are

```text
31 📨 DHCP
      ↓
32 📖 DNS
      ↓
33 🔐 ACL  ← YOU ARE HERE
      ↓
34 🌍 NAT
      ↓
35 📡 Wireless
```

We are moving from **network services** into **traffic control**.

---

# 🧠 2. What Is an ACL?

**ACL = Access Control List.**

An ACL is an ordered list of rules used by a network device to classify traffic and take an action such as:

```text
✅ permit
❌ deny
```

A simple mental model:

```text
🚪 NETWORK GATE
       │
       ▼
   ┌─────────┐
   │   ACL   │
   └────┬────┘
        │
   ┌────┴────┐
   ↓         ↓
 ALLOW     BLOCK
  ✅          ❌
```

---

# 🏢 3. Real-World Analogy — Security Guard

Imagine an office entrance.

The guard checks the visitor against a list:

```text
👤 Employee → ✅ Allow
👤 Approved Vendor → ✅ Allow
👤 Unknown Visitor → ❌ Deny
```

An ACL works similarly by evaluating packet characteristics against configured rules.

---

# 🔥 4. Why Do We Need ACLs?

Without traffic filtering, devices may communicate more freely than the security policy allows.

Example:

```text
HR VLAN ───────► Finance VLAN
                 ❌ Not allowed

HR VLAN ───────► DNS Server
                 ✅ Allowed

HR VLAN ───────► Internet
                 ✅ Allowed
```

ACLs help enforce such policies.

Typical uses include:

- 🛡️ Restricting access to servers
- 🚫 Blocking unwanted traffic
- 🎯 Allowing selected protocols
- 🧩 Controlling inter-network communication
- 🔐 Implementing basic segmentation policies
- 🛠️ Filtering traffic at Layer 3/4

---

# 🧩 5. What Can an ACL Match?

The exact fields depend on the ACL type.

Extended ACLs can evaluate information such as:

```text
📍 Source IP
📍 Destination IP
🔌 Protocol
🚪 Source port
🚪 Destination port
```

Examples:

```text
TCP
UDP
ICMP
HTTP/HTTPS
SSH
DNS
```

---

# 🟢 6. Standard vs Extended ACL

The fundamental CCNA distinction:

| Type | Main matching focus |
|---|---|
| Standard | Source IPv4 address |
| Extended | Source, destination, protocol and ports |

Think:

```text
STANDARD
“Who is sending?”

EXTENDED
“Who is sending, where is it going,
and what kind of traffic is it?”
```

---

# 🟢 7. Standard ACL

A standard ACL primarily matches the **source IPv4 address**.

Example policy:

> Block 192.168.10.0/24.

Conceptually:

```text
192.168.10.0/24
       ↓
      ACL
       ↓
      DENY ❌
```

A standard ACL cannot distinguish HTTP from SSH based on destination port.

---

# 🔵 8. Extended ACL

Extended ACLs provide much finer control.

Example policy:

> Block HR users from SSH access to the server, but allow other traffic.

Conceptually:

```text
Source = HR subnet
Destination = Server
Protocol = TCP
Destination port = 22
              ↓
            DENY ❌
```

That level of specificity is why extended ACLs are commonly used when precise traffic control is required.

---

# 🔢 9. Numbered ACLs

Cisco supports numbered ACLs.

Common IPv4 ranges traditionally include:

```text
Standard ACL   → 1–99
Extended ACL   → 100–199
```

Additional expanded ranges exist on modern IOS platforms, but the classic ranges above remain important for CCNA fundamentals.

---

# 🏷️ 10. Named ACLs

Named ACLs use a meaningful name instead of only a number.

Example:

```cisco
ip access-list standard BLOCK-HR
```

Advantages include:

- 🧠 Easier identification
- 📋 Better documentation
- 🛠️ Easier management
- 🔄 Easier editing of individual entries on supported platforms

---

# 🃏 11. Wildcard Masks

Wildcard masks are one of the most important ACL concepts.

They tell the router which address bits must match and which bits can vary.

Basic rule:

```text
Wildcard bit 0 → MUST MATCH
Wildcard bit 1 → IGNORE / CAN VARY
```

Compare with a subnet mask:

```text
Subnet mask:
1 = network bit
0 = host bit

Wildcard mask:
0 = must match
1 = don't care
```

---

# 🧮 12. Wildcard Mask Examples

For one host:

```text
IP:       192.168.10.10
Wildcard: 0.0.0.0
```

Meaning:

> Match exactly this address.

For a `/24` network:

```text
Network:  192.168.10.0
Wildcard: 0.0.0.255
```

Meaning:

> Match any host inside 192.168.10.0/24.

Memory trick:

```text
/24 subnet mask = 255.255.255.0
Wildcard        =   0.  0.  0.255
```

---

# 🧠 13. Wildcard Calculation Shortcut

For a conventional subnet:

```text
Wildcard = 255.255.255.255 − Subnet Mask
```

Example:

```text
Subnet mask:
255.255.255.0

255.255.255.255
−255.255.255.0
----------------
  0.  0.  0.255
```

Therefore:

```text
/24 → 0.0.0.255
```

---

# 🚨 14. The Implicit Deny

This is critical.

Cisco ACL processing effectively ends with an implicit deny if no configured ACE matches.

Conceptually:

```text
Rule 1 → permit
Rule 2 → deny
Rule 3 → permit
...

No match
   ↓
❌ IMPLICIT DENY
```

It is not normally displayed as a literal configured line in a basic ACL, but you must account for it when designing rules.

---

# ⚠️ 15. ACLs Are Processed Top-to-Bottom

Suppose:

```text
1. deny host 192.168.10.10
2. permit 192.168.10.0/24
```

Traffic from:

```text
192.168.10.10
```

matches rule 1 first.

Therefore it is denied.

The router does not continue searching for a later permit after a matching ACE has decided the action.

Memory:

```text
TOP → DOWN
FIRST MATCH → ACTION
STOP
```

---

# 🔢 16. Sequence Matters

Consider:

```text
1. permit 192.168.10.0/24
2. deny host 192.168.10.10
```

The host `192.168.10.10` matches the first rule, so the later deny is never reached for that packet.

This is one of the most common ACL configuration mistakes.

---

# 📍 17. Where Should ACLs Be Applied?

ACLs can be applied to interfaces and directions.

Conceptually:

```text
             Router
        ┌──────────────┐
IN  →   │              │   → OUT
        │     ACL      │
        │              │
        └──────────────┘
```

Direction means:

```text
in    = packet enters the interface
out   = packet leaves the interface
```

It does **not** mean:

```text
in  = coming into the router from Internet only
out = going toward Internet only
```

Direction is relative to the interface.

---

# 🧠 18. Standard ACL Placement Principle

A classic design guideline is:

> Place standard ACLs closer to the destination.

Why?

Because standard ACLs primarily know the **source** and cannot distinguish the final destination.

If placed too close to the source, they may block that source from reaching multiple destinations unnecessarily.

---

# 🔵 19. Extended ACL Placement Principle

A classic guideline is:

> Place extended ACLs closer to the source.

Because extended ACLs can identify more traffic characteristics, unwanted traffic can be stopped earlier, reducing unnecessary network traversal.

These are design guidelines, not a substitute for understanding the actual traffic path and policy.

---

# ⚙️ 20. Standard ACL — Numbered Example

Policy:

> Deny host `192.168.10.10`, allow everyone else.

```cisco
Router(config)# access-list 10 deny host 192.168.10.10
Router(config)# access-list 10 permit any
```

Apply it to an interface:

```cisco
Router(config)# interface gigabitEthernet0/0
Router(config-if)# ip access-group 10 out
```

Verify:

```cisco
show access-lists
show ip interface gigabitEthernet0/0
```

---

# 🏷️ 21. Standard ACL — Named Example

```cisco
Router(config)# ip access-list standard BLOCK-HOST
Router(config-std-nacl)# deny host 192.168.10.10
Router(config-std-nacl)# permit any
Router(config-std-nacl)# exit
```

Apply:

```cisco
Router(config)# interface gigabitEthernet0/0
Router(config-if)# ip access-group BLOCK-HOST out
```

---

# 🔵 22. Extended ACL — Numbered Example

Policy:

> Block SSH from `192.168.10.0/24` to server `192.168.20.10`, but allow other traffic.

```cisco
Router(config)# access-list 110 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 22
Router(config)# access-list 110 permit ip any any
```

Apply according to the actual traffic path and security design.

Example:

```cisco
Router(config)# interface gigabitEthernet0/0
Router(config-if)# ip access-group 110 in
```

---

# 🏷️ 23. Extended ACL — Named Example

```cisco
Router(config)# ip access-list extended BLOCK-SSH
Router(config-ext-nacl)# deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 22
Router(config-ext-nacl)# permit ip any any
Router(config-ext-nacl)# exit
```

Then apply it to the appropriate interface and direction.

---

# 🚪 24. Common Protocol/Port Matches

Examples you should recognize:

| Service | Protocol | Common destination port |
|---|---|---:|
| HTTP | TCP | 80 |
| HTTPS | TCP | 443 |
| SSH | TCP | 22 |
| Telnet | TCP | 23 |
| DNS | UDP/TCP | 53 |
| DHCP server | UDP | 67 |
| DHCP client | UDP | 68 |
| FTP control | TCP | 21 |
| SMTP | TCP | 25 |

📌 ACL syntax and available keywords depend on IOS/platform/version, so verify the command on the device used in your lab.

---

# 🔎 25. ACL Verification Commands

Useful commands:

```cisco
show access-lists
show ip access-lists
show ip interface brief
show ip interface gigabitEthernet0/0
show running-config | include access-list
```

Look for:

```text
ACL exists?              ✅
Correct entries?         ✅
Correct order?           ✅
Correct interface?       ✅
Correct direction?       ✅
Counters increasing?     ✅
```

---

# 📊 26. ACL Match Counters

ACL output may show match counters beside entries.

Conceptually:

```text
deny tcp ... (25 matches)
permit ip any any (140 matches)
```

These counters can help determine whether traffic is actually reaching the rule.

If the expected rule shows zero matches, investigate:

```text
Traffic path
Interface
Direction
Source/destination
Protocol/port
```

---

# 🧪 27. Packet Tracer Lab — Standard ACL

## 🎯 Objective

Prevent one host from reaching a remote network while allowing other hosts.

Topology:

```text
💻 PC1 ──┐
💻 PC2 ──┼── 🔀 SW ── 🛣️ R1 ── 🛣️ R2 ── 🖥️ Server
💻 PC3 ──┘
```

Example:

```text
PC1 = 192.168.10.10
PC2 = 192.168.10.11
PC3 = 192.168.10.12
Server = 192.168.20.10
```

Policy:

```text
PC1 → Server ❌
PC2 → Server ✅
PC3 → Server ✅
```

Configure a standard ACL matching PC1's source address and apply it at a suitable point near the destination.

Example:

```cisco
access-list 10 deny host 192.168.10.10
access-list 10 permit any
```

---

# 🧪 28. Packet Tracer Lab — Extended ACL

## 🎯 Objective

Block HTTP/HTTPS or SSH traffic selectively while allowing other IP traffic.

Example policy:

```text
Users subnet → Web Server
HTTP/HTTPS = allowed
SSH = denied
```

Possible ACL concept:

```cisco
ip access-list extended USER-POLICY
 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 22
 permit ip any any
```

Test:

```text
SSH → should fail ❌
Other permitted traffic → should work ✅
```

---

# 🚨 29. ACL Troubleshooting Flow

When traffic unexpectedly fails:

```text
1️⃣ Is basic connectivity working?
          ↓
2️⃣ Is an ACL applied?
          ↓
3️⃣ Which interface?
          ↓
4️⃣ In or out?
          ↓
5️⃣ What is the packet's source?
          ↓
6️⃣ What is the destination?
          ↓
7️⃣ What protocol/port is used?
          ↓
8️⃣ Which ACE matches first?
          ↓
9️⃣ Is implicit deny catching the packet?
```

Do not immediately delete the ACL.

First determine which rule is responsible.

---

# 🐛 30. Common ACL Problems

### ❌ Wrong interface

ACL exists but is attached somewhere traffic never crosses.

### ❌ Wrong direction

The packet enters/exits differently than expected.

### ❌ Wrong wildcard

The ACL matches too much or too little.

### ❌ Wrong rule order

A broad permit appears before a specific deny.

### ❌ Missing permit

Traffic reaches the end and encounters implicit deny.

### ❌ Wrong protocol/port

The rule blocks TCP when the actual traffic is UDP, or vice versa.

### ❌ Testing the wrong traffic

An administrator changes the ACL while testing traffic that does not match the policy.

---

# 🧮 31. Wildcard Practice

Convert these:

| Prefix | Subnet Mask | Wildcard |
|---|---|---|
| /32 | 255.255.255.255 | 0.0.0.0 |
| /30 | 255.255.255.252 | 0.0.0.3 |
| /29 | 255.255.255.248 | 0.0.0.7 |
| /28 | 255.255.255.240 | 0.0.0.15 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /26 | 255.255.255.192 | 0.0.0.63 |
| /25 | 255.255.255.128 | 0.0.0.127 |
| /24 | 255.255.255.0 | 0.0.0.255 |

Mastering this makes ACL configuration much easier.

---

# 🏢 32. Enterprise Scenario

A company has:

```text
VLAN 10 → HR
VLAN 20 → Finance
VLAN 30 → IT
VLAN 40 → Servers
```

Security requirement:

```text
HR → Finance        ❌
HR → DNS            ✅
HR → Web            ✅
IT → Servers        ✅
Finance → HR        ❌
```

An engineer can translate the business policy into specific source/destination/protocol rules.

This is the important professional skill:

```text
Business requirement
        ↓
Traffic flow
        ↓
ACL match criteria
        ↓
Permit / deny policy
        ↓
Placement
        ↓
Verification
```

---

# 🎯 33. Scenario Challenge — ACL Detective

A server is reachable from every subnet except HR.

You discover:

```text
HR → Gateway       ✅
HR → DNS           ✅
HR → Server        ❌
Finance → Server   ✅
IT → Server        ✅
```

### Your task

Determine:

1. Which ACL could be involved?
2. Which interface should you inspect?
3. Is the ACL inbound or outbound?
4. Which source/destination should match?
5. Could implicit deny be responsible?
6. What command would you use to verify ACL counters?

Useful commands:

```cisco
show access-lists
show ip interface
show running-config
```

---

# 🎤 34. CCNA Interview Questions

### 🟢 Beginner

**Q1. What is an ACL?**

An ordered set of rules used to classify traffic and permit or deny it.

**Q2. Standard vs extended ACL?**

Standard ACLs primarily match source IPv4 addresses; extended ACLs can match source, destination, protocol and ports.

**Q3. What is implicit deny?**

If traffic does not match a configured ACE, it is effectively denied at the end of the ACL.

### 🟡 Intermediate

**Q4. What is a wildcard mask?**

It specifies which address bits must match and which may vary.

**Q5. What does wildcard 0.0.0.255 represent?**

It allows the last octet to vary, commonly matching a /24 network.

**Q6. What does `ip access-group` do?**

It applies an IPv4 ACL to an interface in the specified direction.

### 🔴 Troubleshooting

**Q7. Why can a correct ACL still cause unexpected blocking?**

Possible causes include incorrect placement, direction, rule order, wildcard mask, missing permit or implicit deny.

**Q8. Why is rule order important?**

ACL processing is sequential; the first matching ACE determines the action.

**Q9. How do you verify ACL operation?**

Use commands such as `show access-lists` and `show ip interface` and inspect match counters and interface application.

---

# ⚡ 35. Quick Revision

```text
ACL = Access Control List

STANDARD
→ Source IPv4

EXTENDED
→ Source
→ Destination
→ Protocol
→ Ports

Processing:
TOP → DOWN
FIRST MATCH → ACTION

Implicit end:
NO MATCH → DENY

Wildcard:
0 = MUST MATCH
1 = DON'T CARE

Classic ranges:
1–99     = Standard
100–199  = Extended

Interface direction:
IN  = entering interface
OUT = leaving interface

Guideline:
Standard → closer to destination
Extended → closer to source
```

---

# 📋 36. ACL Cheat Sheet

```cisco
! Standard numbered
access-list 10 deny host 192.168.10.10
access-list 10 permit any

! Named standard
ip access-list standard BLOCK-HOST
 deny host 192.168.10.10
 permit any

! Extended numbered
access-list 110 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.10 eq 22
access-list 110 permit ip any any

! Apply
interface g0/0
 ip access-group 110 in

! Verify
show access-lists
show ip access-lists
show ip interface g0/0
```

---

# 🏁 37. Final Takeaways

Before moving on, make sure you can explain:

- What an ACL does
- Standard vs extended ACLs
- Numbered vs named ACLs
- Wildcard masks
- Implicit deny
- First-match processing
- Inbound vs outbound application
- Standard/extended placement guidelines
- TCP/UDP port matching
- ACL verification
- ACL troubleshooting

The professional mental model is:

```text
🎯 REQUIREMENT
      ↓
📍 SOURCE
      ↓
📍 DESTINATION
      ↓
🔌 PROTOCOL / PORT
      ↓
🧾 ACL RULES
      ↓
📍 INTERFACE + DIRECTION
      ↓
🔎 VERIFY
      ↓
🐛 TROUBLESHOOT
```

---

# 🚀 Next Module

```text
33 🔐 ACL
      ↓
34 🌍 NAT
      ↓
35 📡 Wireless
      ↓
36 🛡️ Network Security
```

**Next stop: 🌍 NAT — understanding how private and public addressing interact.**

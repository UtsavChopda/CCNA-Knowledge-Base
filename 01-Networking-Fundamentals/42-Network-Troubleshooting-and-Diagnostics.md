# 🛠️🚨 Module 42 — Network Troubleshooting & Diagnostics

> **Mission:** Stop guessing. Start troubleshooting like a network engineer. 🔎🧠

![Troubleshooting](https://img.shields.io/badge/Networking-Troubleshooting-red?style=for-the-badge)
![CCNA](https://img.shields.io/badge/CCNA-Diagnostics-blue?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Beginner%20→%20Advanced-success?style=for-the-badge)

---

# 🗺️ WHERE WE ARE IN THE JOURNEY

You have learned how networks are **built**. Now we learn how professionals **fix them**.

```text
Networking Fundamentals
        ↓
Switching → VLANs → STP → EtherChannel
        ↓
Routing → OSPF
        ↓
DHCP → DNS → ACL → NAT
        ↓
Wireless → Security → Management
        ↓
QoS → Automation → APIs → Python
        ↓
🛠️ TROUBLESHOOTING & DIAGNOSTICS ← YOU ARE HERE
        ↓
Enterprise Labs
        ↓
SOC / Blue Team / Cybersecurity
```

### 🧠 The biggest mindset change

A beginner asks:

> ❌ “What command should I type?”

An engineer asks:

> ✅ “What exactly is failing, where is it failing, and what evidence proves it?”

That difference is **troubleshooting maturity**.

---

# 🎯 1. LEARNING OBJECTIVES

By the end of this module, you should be able to:

- 🧠 Explain a structured troubleshooting methodology
- 🗺️ Identify the scope and impact of an incident
- 🔎 Separate symptoms from root causes
- 🧱 Troubleshoot layer by layer
- 📡 Verify physical connectivity
- 🔌 Troubleshoot interfaces and Ethernet
- 🏷️ Troubleshoot VLANs and trunks
- 🌳 Troubleshoot STP
- 🔗 Troubleshoot EtherChannel
- 🌐 Troubleshoot IPv4 addressing and subnetting
- 🧭 Troubleshoot default gateways and routing
- 🛰️ Troubleshoot OSPF fundamentals
- 📨 Troubleshoot DHCP
- 🌍 Troubleshoot DNS
- 🧱 Troubleshoot ACLs
- 🔄 Troubleshoot NAT
- 📶 Troubleshoot wireless basics
- 🔐 Troubleshoot SSH and management access
- 📊 Use logs and monitoring evidence
- 🐍 Troubleshoot automation failures
- 🧪 Build a troubleshooting decision tree
- 🏢 Approach enterprise incidents professionally
- 🎤 Answer troubleshooting interview questions

---

# 🚨 2. WHAT IS NETWORK TROUBLESHOOTING?

**Network troubleshooting** is the systematic process of identifying, isolating, diagnosing, correcting, and verifying a network problem.

Think:

```text
PROBLEM
   ↓
OBSERVE
   ↓
DEFINE
   ↓
ISOLATE
   ↓
TEST
   ↓
FIX
   ↓
VERIFY
   ↓
DOCUMENT
```

### ⭐ Golden rule

> **Never change something just because you suspect it. Test the hypothesis first whenever practical.**

---

# 🧠 3. SYMPTOM ≠ ROOT CAUSE

This is one of the most important concepts in troubleshooting.

Imagine:

```text
User:
“Internet is not working!” 😭
```

That is a **symptom**, not a diagnosis.

Possible causes:

```text
Cable disconnected
      OR
VLAN mismatch
      OR
DHCP failure
      OR
Default gateway failure
      OR
Routing failure
      OR
DNS failure
      OR
ACL blocking
      OR
NAT problem
      OR
Remote service outage
```

Your job is to turn:

```text
“I can't access Google.”
```

into:

```text
DNS resolution succeeds
      ↓
Default gateway reachable
      ↓
Internet IP reachable
      ↓
TCP/HTTPS test succeeds
      ↓
Application works
```

---

# 🎯 4. DEFINE THE PROBLEM FIRST

Before touching the network, ask:

### 👤 Who is affected?

```text
One user?
One VLAN?
One floor?
One site?
Everyone?
```

### ⏰ When did it start?

```text
Just now?
After a change?
Every morning?
Randomly?
```

### 🌐 What is affected?

```text
One application?
Internet?
Internal servers?
Voice?
All network access?
```

### 📈 How severe is it?

```text
One user       → Low scope
One department → Medium scope
Entire site    → High scope
Entire company → Critical 🚨
```

### 🧠 Troubleshooting principle

> **Define the blast radius before changing anything.**

---

# 🧭 5. THE TROUBLESHOOTING METHODOLOGY

A practical methodology:

```text
1️⃣ Identify the problem
        ↓
2️⃣ Gather information
        ↓
3️⃣ Form a hypothesis
        ↓
4️⃣ Test the hypothesis
        ↓
5️⃣ Implement a controlled fix
        ↓
6️⃣ Verify the result
        ↓
7️⃣ Document what happened
```

Another useful model is:

```text
Observe → Hypothesize → Test → Fix → Verify
```

---

# 🔬 6. THE SCIENTIFIC METHOD 🧪

Network troubleshooting is basically applied science.

Example:

### Observation

```text
PC cannot reach server.
```

### Hypothesis

```text
Maybe the PC has the wrong default gateway.
```

### Test

```text
ipconfig
ping <gateway>
```

### Evidence

```text
Gateway = unreachable
```

### Next hypothesis

```text
Maybe VLAN or switchport configuration is wrong.
```

You are narrowing the problem.

---

# 🧱 7. TROUBLESHOOT FROM THE BOTTOM UP

A classic approach:

```text
Layer 1 → Physical
   ↓
Layer 2 → Switching
   ↓
Layer 3 → IP/Routing
   ↓
Layer 4 → TCP/UDP
   ↓
Layer 7 → Application
```

Example:

```text
Is cable connected? 🔌
       ↓
Is interface up? 🟢
       ↓
Correct VLAN? 🏷️
       ↓
Correct IP? 🌐
       ↓
Gateway reachable? 🚪
       ↓
Route exists? 🧭
       ↓
Port/service reachable? 🔌
       ↓
Application working? 💻
```

### ⚠️ But don't blindly use bottom-up every time

Experienced engineers choose the methodology based on evidence.

You can also troubleshoot:

- Top-down
- Divide-and-conquer
- Follow-the-path
- Compare with a working device

---

# 🔌 8. LAYER 1 — PHYSICAL TROUBLESHOOTING

Start with the physical layer when the symptoms suggest it.

Check:

- Cable connected?
- Correct cable?
- Interface administratively down?
- Link light present?
- SFP/transceiver compatible?
- Fiber polarity correct?
- Interface errors?
- Speed/duplex issues?

### Cisco commands

```cisco
show interfaces status
show interfaces
show ip interface brief
```

---

# 🟢 9. UP/UP — WHAT DOES IT MEAN?

For a routed interface, you may see:

```text
Gig0/0   192.168.1.1   YES manual   up   up
```

Interpretation:

```text
First up  → physical/link layer
Second up → line protocol
```

### Common states

```text
administratively down/down
→ interface is shut down

up/down
→ physical link exists, protocol is down

down/down
→ physical/link problem or no active connection

up/up
→ operational
```

Always investigate the actual platform output rather than relying only on memorized labels.

---

# 🚨 10. INTERFACE ERROR COUNTERS

Use:

```cisco
show interfaces gigabitEthernet0/1
```

Look for:

```text
input errors
CRC
frame errors
output errors
collisions
drops
```

### CRC errors may suggest

- Physical cabling issue
- Faulty transceiver
- Signal integrity problem
- Duplex/physical mismatch in relevant environments

Do not immediately replace hardware.

First:

```text
Observe
 ↓
Compare
 ↓
Test
 ↓
Replace if evidence supports it
```

---

# 🏷️ 11. VLAN TROUBLESHOOTING

User says:

> “I can reach users on my switch but not users in another department.”

Possible VLAN issue.

Check:

```cisco
show vlan brief
show interfaces switchport
show interfaces status
```

Questions:

```text
Is VLAN present?
Is access port in correct VLAN?
Is interface operational?
```

---

# 🧪 12. VLAN TROUBLESHOOTING SCENARIO

Topology:

```text
PC-A
 │
 │ VLAN 10
 ▼
SW1 ===== trunk ===== SW2
                         │
                         │ VLAN 10
                         ▼
                        PC-B
```

PC-A cannot reach PC-B.

Check:

```text
VLAN 10 exists on SW1?     ✅/❌
VLAN 10 exists on SW2?     ✅/❌
PC-A access VLAN = 10?     ✅/❌
PC-B access VLAN = 10?     ✅/❌
Trunk operational?         ✅/❌
VLAN 10 allowed on trunk?  ✅/❌
```

### 🧠 Troubleshooting ladder

```cisco
show vlan brief
show interfaces status
show interfaces trunk
show interfaces switchport
```

---

# 🚧 13. TRUNK TROUBLESHOOTING

If VLANs work locally but fail across switches, investigate the trunk.

```cisco
show interfaces trunk
```

Look for:

- Trunk status
- Native VLAN
- Allowed VLANs
- VLANs active
- VLANs forwarding

### Classic mistake

```text
VLAN 20 exists
        ↓
Access port correct
        ↓
But VLAN 20 is not allowed on trunk
        ↓
💥 Communication fails
```

---

# 🌳 14. STP TROUBLESHOOTING

STP can prevent loops, but an unexpected topology state can cause connectivity problems.

Useful commands:

```cisco
show spanning-tree
show spanning-tree vlan 10
show spanning-tree summary
```

Ask:

```text
Who is root bridge?
Which ports are forwarding?
Which ports are blocking/alternate?
Are there topology changes?
```

### 🧠 Root bridge mindset

Don't ask:

> “Why is this port blocked?”

Ask:

> “What path is STP intentionally preventing, and why?”

---

# 🔗 15. ETHERCHANNEL TROUBLESHOOTING

If an EtherChannel is expected but links are not bundling:

```cisco
show etherchannel summary
```

Also inspect:

```cisco
show interfaces port-channel 1
show lacp neighbor
```

Depending on platform/protocol, verify:

```text
Speed
Duplex
VLAN mode
Allowed VLANs
Native VLAN
LACP mode
Channel-group configuration
```

### 🚨 Common mistake

One physical member has a different configuration.

```text
Gi0/1 → trunk
Gi0/2 → access
```

That inconsistency can prevent proper bundling or produce unexpected behavior.

---

# 🌐 16. LAYER 3 — IP TROUBLESHOOTING

First check the host configuration.

Windows:

```powershell
ipconfig /all
```

Linux:

```bash
ip addr
ip route
```

Cisco:

```cisco
show ip interface brief
```

Check:

```text
IP address
Subnet mask/prefix
Default gateway
DNS server
Interface state
```

---

# 🧮 17. SUBNET MASK MISTAKES

Example:

```text
PC-A
IP:      192.168.10.10
Mask:    255.255.255.0
Gateway: 192.168.10.1
```

If another host is:

```text
192.168.20.10/24
```

they are in different IPv4 subnets.

That means communication generally requires a Layer 3 device.

### 🧠 Always calculate

```text
Network
Broadcast
Valid host range
Gateway
```

Do not guess from the first three octets unless the prefix actually supports that conclusion.

---

# 🚪 18. DEFAULT GATEWAY TROUBLESHOOTING

The default gateway is the host's next-hop router for destinations outside its local subnet.

Typical test:

```text
PC
 ↓
ping gateway
```

If this fails, investigate locally before blaming the internet.

```text
PC
 │
 ├── NIC
 ├── Switchport
 ├── VLAN
 └── Gateway interface
```

---

# 📡 19. PING — YOUR FIRST FRIEND

`ping` commonly uses ICMP Echo Request/Echo Reply.

Basic logic:

```text
PC ───── ICMP Echo ─────► Server
PC ◄──── ICMP Reply ───── Server
```

Use it carefully.

A failed ping does **not always mean the destination is down**.

Possible causes:

- ICMP filtering
- ACL
- Host firewall
- Routing problem
- Interface failure
- Destination genuinely unavailable

### 🧠 Important

> **Ping tests reachability using ICMP; it does not prove every application is working.**

---

# 🛣️ 20. TRACEROUTE / TRACERT

Traceroute helps reveal the Layer 3 path toward a destination.

Windows:

```powershell
tracert 8.8.8.8
```

Linux/macOS:

```bash
traceroute 8.8.8.8
```

Conceptually:

```text
PC
 ↓
R1
 ↓
R2
 ↓
R3
 ↓
Server
```

### ⚠️ Don't interpret every `*` as failure

Routers may rate-limit or filter traceroute responses while still forwarding traffic.

---

# 🧭 21. ROUTING TABLE — THE NETWORK'S GPS

Cisco:

```cisco
show ip route
```

Ask:

```text
Do I have a route?
What is the next hop?
Which interface is used?
What is the route source?
Is there a more specific route?
```

Example:

```text
O 10.10.20.0/24 [110/2] via 10.0.0.2
```

Breakdown conceptually:

```text
O        → OSPF
10.10... → destination
110      → administrative distance
2        → metric/cost
10.0...  → next hop
```

---

# 🧠 22. ROUTING DECISION LOGIC

When a router receives a packet:

```text
Destination IP
      ↓
Routing table lookup
      ↓
Longest prefix match
      ↓
Best route
      ↓
Next hop / outgoing interface
```

### ⭐ Longest Prefix Match

Suppose the router knows:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
```

Destination:

```text
10.10.20.50
```

The `/24` is the most specific match.

---

# 🛰️ 23. OSPF TROUBLESHOOTING

You learned OSPF deeply earlier. Now use it diagnostically.

Start with:

```cisco
show ip ospf neighbor
show ip ospf interface brief
show ip ospf
show ip route ospf
```

If there is no neighbor:

```text
Check interface
      ↓
IP addressing
      ↓
Area
      ↓
Network type
      ↓
Hello/dead timers
      ↓
Authentication
      ↓
Passive-interface
      ↓
ACL/firewall
```

---

# 🤝 24. OSPF NEIGHBOR STATE CLUES

Useful conceptual states:

```text
Down
 ↓
Init
 ↓
2-Way
 ↓
ExStart
 ↓
Exchange
 ↓
Loading
 ↓
Full
```

If neighbors become stuck, the state itself provides a clue.

### Example

```text
ExStart/Exchange problem
```

Investigate things such as:

- MTU mismatch
- Network-type mismatch
- Duplicate router ID or other addressing/design errors
- Interface problems

Don't randomly reset OSPF before understanding the symptom.

---

# 📨 25. DHCP TROUBLESHOOTING

Client:

```text
“I have no IP address.” 😭
```

DHCP process:

```text
Discover
   ↓
Offer
   ↓
Request
   ↓
ACK
```

Check:

```text
Is client VLAN correct?
Is DHCP server reachable?
Is relay configured if needed?
Is DHCP scope exhausted?
Are UDP ports permitted?
```

Cisco relay configuration concept:

```cisco
interface vlan 10
 ip helper-address <DHCP-SERVER-IP>
```

Verify:

```cisco
show running-config interface vlan 10
show ip interface vlan 10
```

---

# 🌍 26. DNS TROUBLESHOOTING

Very common incident:

```text
Internet IP works
Website name doesn't work
```

That strongly suggests a DNS-related problem rather than basic IP reachability.

Test:

```bash
nslookup example.com
```

or:

```bash
dig example.com
```

### Diagnostic split

```text
ping 1.1.1.1       → works
ping example.com   → fails
```

Possible DNS issue.

But remember:

```text
ICMP behavior ≠ application behavior
```

---

# 🧱 27. ACL TROUBLESHOOTING

ACL problems are dangerous because traffic can be silently blocked by policy.

Cisco:

```cisco
show access-lists
show running-config | include access-group
```

Remember:

> **ACLs are processed in order.**

And:

> **There is an implicit deny at the end of an ACL when no earlier entry permits the traffic.**

### Scenario

```text
PC → Server
     ↓
❌ Connection fails
```

Check:

```text
Source IP
Destination IP
Protocol
Port
Direction
Interface
ACL order
```

---

# 🔄 28. NAT TROUBLESHOOTING

Cisco:

```cisco
show ip nat translations
show ip nat statistics
```

Ask:

```text
Is NAT configured?
Are inside/outside interfaces correct?
Does traffic match the NAT rule?
Does routing work?
Is the translated address available?
```

### Critical concept

NAT cannot fix a missing route by itself.

Troubleshoot:

```text
Host
 ↓
Gateway
 ↓
Routing
 ↓
NAT
 ↓
Remote network
```

---

# 📶 29. WIRELESS TROUBLESHOOTING

A user says:

> “Wi-Fi is connected but internet doesn't work.”

Do not immediately blame Wi-Fi.

Break it down:

```text
Association? 📡
    ↓
Authentication? 🔐
    ↓
DHCP address? 📨
    ↓
Gateway reachable? 🚪
    ↓
DNS works? 🌍
    ↓
Internet/application works? 🌐
```

This separates RF/authentication problems from IP/DNS/upstream problems.

---

# 🔐 30. SSH TROUBLESHOOTING

SSH fails.

Don't say:

> “SSH is broken.”

Break it down.

```text
Can I reach the device IP?
        ↓
Is TCP/22 reachable?
        ↓
Is SSH enabled?
        ↓
Are VTY lines configured?
        ↓
Is authentication correct?
        ↓
Is authorization permitted?
```

Cisco commands:

```cisco
show ip ssh
show running-config | section line vty
show running-config | include username
show access-lists
```

---

# 🧪 31. `show` COMMANDS = YOUR NETWORK MICROSCOPE 🔬

Cisco troubleshooting heavily depends on operational commands.

Useful commands include:

```cisco
show running-config
show startup-config
show interfaces
show ip interface brief
show vlan brief
show interfaces trunk
show spanning-tree
show etherchannel summary
show mac address-table
show arp
show ip route
show ip protocols
show ip ospf neighbor
show access-lists
show ip nat translations
show logging
```

### 🧠 Don't memorize commands as a giant list.

Memorize them by question.

```text
What interfaces exist?
→ show ip interface brief

What VLANs exist?
→ show vlan brief

Who is my L2 neighbor?
→ show cdp neighbors / show lldp neighbors

What routes do I know?
→ show ip route

What OSPF neighbors exist?
→ show ip ospf neighbor
```

---

# 📊 32. LOGS — THE NETWORK'S BLACK BOX

Cisco:

```cisco
show logging
```

Logs can reveal:

- Interface state changes
- Authentication failures
- Routing changes
- Configuration events
- Hardware warnings
- System messages

### Incident timeline

```text
10:01:02 → Interface down
10:01:05 → OSPF neighbor lost
10:01:07 → Route removed
10:01:12 → Users report outage
```

The timeline can reveal that the user complaint was **a consequence**, not the root cause.

---

# ⏰ 33. TIME SYNCHRONIZATION MATTERS

If devices have different clocks:

```text
Router → 10:00
Switch → 10:07
Firewall → 09:54
SIEM → 10:02
```

Correlation becomes difficult.

NTP helps establish a consistent time base.

This is critical for both:

- Network troubleshooting
- Cybersecurity investigations 🛡️

---

# 🔍 34. PACKET CAPTURE — SEE WHAT IS ACTUALLY HAPPENING

When commands are not enough, inspect packets.

Tools include:

- Wireshark
- tcpdump
- Embedded packet capture features where supported

You can observe:

```text
ARP
DHCP
DNS
ICMP
TCP
UDP
HTTP/HTTPS metadata
Routing protocol traffic where observable
```

### Example: DHCP issue

Capture traffic.

Expected:

```text
DHCP Discover →
             ← DHCP Offer
DHCP Request →
             ← DHCP ACK
```

If you see:

```text
DHCP Discover →
(no Offer)
```

You have evidence that the problem is somewhere after the client's request leaves—or that the response is not reaching the client.

---

# 🧠 35. ARP TROUBLESHOOTING

ARP maps IPv4 addresses to MAC addresses on a local network.

Cisco:

```cisco
show arp
```

Host:

```bash
arp -a
```

If a host cannot reach its local gateway, investigate whether ARP resolution is occurring.

Conceptually:

```text
PC:
“Who has 192.168.1.1?”
        ↓
Gateway:
“I do — MAC = xx:xx:xx...”
```

### Possible issues

- Wrong subnet
- VLAN mismatch
- Interface down
- Duplicate IP
- ARP filtering/security feature
- Gateway unavailable

---

# 🚨 36. DUPLICATE IP ADDRESS

Two devices accidentally use:

```text
192.168.10.50
```

Symptoms can be strange:

- Intermittent connectivity
- ARP table changes
- Users randomly reach different devices
- Application sessions fail unpredictably

### Troubleshooting mindset

```text
Intermittent ≠ automatically wireless
Intermittent ≠ automatically routing
```

Investigate evidence.

---

# 🧭 37. DIVIDE AND CONQUER

Suppose:

```text
PC ─ SW ─ R1 ─ R2 ─ Server
```

Don't test everything randomly.

Divide the path:

```text
PC → SW
SW → R1
R1 → R2
R2 → Server
```

Find the last known good point.

Example:

```text
PC → SW     ✅
SW → R1     ✅
R1 → R2     ❌
R2 → Server ?
```

Focus on the R1–R2 segment.

This dramatically reduces the search space.

---

# 🧭 38. FOLLOW THE PATH

A packet does not magically teleport.

For every failure, map:

```text
SOURCE
  ↓
ACCESS
  ↓
DISTRIBUTION
  ↓
CORE
  ↓
EDGE
  ↓
DESTINATION
```

At each hop ask:

```text
Does it know the destination?
Does the interface work?
Does policy allow it?
Does the next hop respond?
```

---

# 🧠 39. COMPARE WITH A WORKING DEVICE ⭐

One of the most powerful troubleshooting techniques is comparison.

Suppose:

```text
PC-A ❌
PC-B ✅
```

Both should have identical access requirements.

Compare:

```text
IP address
Subnet mask
Gateway
DNS
VLAN
Switchport
DHCP lease
ACL path
```

### Engineer trick

> **A working device is often your best reference configuration.**

Be careful: don't blindly copy it. Identify the meaningful difference.

---

# 📝 40. CONFIGURATION DIFF THINKING

Imagine:

```text
Working SW1
interface Gi0/10
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
```

Broken SW2:

```text
interface Gi0/10
 switchport mode access
 switchport access vlan 30
```

The difference jumps out.

```text
Expected VLAN = 20
Actual VLAN   = 30
```

That is evidence.

---

# 🧯 41. NEVER MAKE 10 CHANGES AT ONCE

Bad troubleshooting:

```text
Change VLAN
Change ACL
Restart OSPF
Reload switch
Change IP
Change gateway
```

Then:

> “It works now! 🎉”

But you don't know why.

Worse:

> It might break again tomorrow. 😭

Better:

```text
Hypothesis
   ↓
One controlled change
   ↓
Test
   ↓
Observe
   ↓
Next step
```

---

# 🚦 42. CHANGE CONTROL

Production networks are not laboratories.

Before significant changes:

```text
Understand impact
      ↓
Backup / capture current state
      ↓
Plan change
      ↓
Define rollback
      ↓
Execute
      ↓
Verify
      ↓
Document
```

### 🔥 Golden production rule

> **Always know how you will undo a change before you make it.**

---

# 🧪 43. TROUBLESHOOTING LAB #1 — DEAD INTERFACE

### Topology

```text
💻 PC ───────── 🔀 SW1
```

User says:

> “My PC has no network.”

### Step 1

```cisco
show interfaces status
```

### Step 2

```cisco
show interfaces gigabitEthernet0/1
```

### Step 3

Check:

```text
Physical cable
Interface status
Administrative shutdown
Errors
VLAN
```

### Challenge

The interface shows:

```text
administratively down
```

What is your likely first action?

Answer:

```cisco
interface gigabitEthernet0/1
 no shutdown
```

Then verify.

---

# 🧪 44. LAB #2 — WRONG VLAN

Topology:

```text
PC-A
 │
SW1
 │
VLAN 10
```

Expected:

```text
PC-A → VLAN 10
```

Actual:

```text
PC-A → VLAN 20
```

Commands:

```cisco
show vlan brief
show interfaces gigabitEthernet0/1 switchport
```

Correct the access VLAN in the lab.

Then verify end-to-end connectivity.

---

# 🧪 45. LAB #3 — TRUNK ALLOWED VLAN

Topology:

```text
PC-A ─ SW1 ===== SW2 ─ PC-B
          trunk
```

VLAN 20 exists on both switches.

But VLAN 20 is missing from the trunk allowed list.

Verify:

```cisco
show interfaces trunk
```

Find the mismatch.

Correct it in the lab, then verify:

```text
PC-A → PC-B
```

---

# 🧪 46. LAB #4 — ROUTING FAILURE

Topology:

```text
LAN-A ─ R1 ─ R2 ─ LAN-B
```

Users in LAN-A cannot reach LAN-B.

Check in order:

```cisco
show ip interface brief
show ip route
```

On R1:

```text
Does R1 know LAN-B?
```

On R2:

```text
Does R2 know LAN-A?
```

Then test:

```text
PC → Gateway
Gateway → Remote router
Remote router → Destination
```

---

# 🧪 47. LAB #5 — DNS IS THE REAL PROBLEM

User says:

> “Internet is down.”

Test:

```bash
ping 1.1.1.1
```

Result:

```text
Success ✅
```

Then:

```bash
nslookup example.com
```

Result:

```text
DNS timeout ❌
```

### Diagnosis

Basic IP connectivity exists.

Investigate DNS configuration/reachability rather than replacing the router.

---

# 🧪 48. LAB #6 — ACL BLOCKING TRAFFIC

Topology:

```text
PC ─ R1 ─ Server
```

Ping fails.

Routing exists.

Check:

```cisco
show ip route
show access-lists
show running-config | include access-group
```

You discover:

```text
deny icmp host <PC-IP> host <SERVER-IP>
```

### Challenge

Explain:

1. Why routing can be correct.
2. Why ping can still fail.
3. Why removing the entire ACL may be a bad production fix.

Correct approach:

> Modify the policy according to approved requirements rather than blindly disabling security controls.

---

# 🧪 49. LAB #7 — OSPF NEIGHBOR FAILURE

Topology:

```text
R1 ───────── R2
     OSPF
```

Expected:

```text
FULL
```

Actual:

```text
No neighbor
```

Run:

```cisco
show ip ospf neighbor
show ip ospf interface
show ip interface brief
```

Compare:

```text
IP addressing
Area
Hello timer
Dead timer
Network type
Authentication
MTU
Passive-interface
```

Do not reset the process before identifying the mismatch.

---

# 🧪 50. LAB #8 — THE ENTERPRISE INCIDENT 🔥

### Scenario

At 10:15 AM:

```text
Users report:
“CRM is unreachable.”
```

But:

```text
Internet → works
Email    → works
DNS      → works
CRM      → fails
```

### Your mission

Don't touch anything yet.

Build hypotheses:

```text
CRM server down?
Route missing?
ACL change?
NAT issue?
TCP port unavailable?
Application issue?
```

### Evidence collection

```text
1. Ping server IP
2. Test route
3. Check ACL
4. Test relevant TCP port
5. Check logs
6. Compare with working user
7. Check server/application status
```

### Boss lesson 👑

A network engineer does not automatically own every application outage.

Sometimes the correct conclusion is:

> **Network path is healthy; escalate to the application/server team with evidence.**

That is professional troubleshooting.

---

# 🛡️ 51. TROUBLESHOOTING + CYBERSECURITY

Troubleshooting skills transfer directly into SOC work.

Consider:

```text
User reports:
“Network is extremely slow.”
```

Possible security causes:

- Malware traffic
- Command-and-control communication
- Network scanning
- DDoS activity
- Compromised host
- Rogue DHCP
- ARP spoofing
- DNS manipulation

Now combine:

```text
Network knowledge
      +
Packet analysis
      +
Logs
      +
SIEM
      +
Automation
      ↓
🛡️ Blue Team Investigation
```

---

# 🔎 52. NETWORK TROUBLESHOOTING FOR SOC ANALYSTS

A SOC analyst may receive:

```text
🚨 Alert:
Unusual traffic from 10.10.20.55
```

Questions:

```text
Who owns the IP?
Which VLAN?
Which switchport?
Which MAC address?
What destination is contacted?
What ports are used?
When did it start?
What other devices saw the traffic?
```

Possible commands/data sources:

```text
ARP tables
MAC address tables
DHCP logs
DNS logs
Firewall logs
NetFlow/telemetry
Syslog
Packet captures
SIEM searches
```

This is where CCNA knowledge becomes a cybersecurity advantage.

---

# 🐍 53. PYTHON TROUBLESHOOTING

Automation itself can fail.

Example:

```text
Python script
    ↓
SSH connection
    ↓
❌ Timeout
```

Do not immediately blame Python.

Break the chain:

```text
DNS/IP resolution?
      ↓
TCP connectivity?
      ↓
SSH service?
      ↓
Credentials?
      ↓
Authorization?
      ↓
Library behavior?
      ↓
Script logic?
```

### 🧠 Same troubleshooting philosophy

> **Follow the dependency chain.**

---

# 🧪 54. AUTOMATION FAILURE EXAMPLE

Suppose Python reports:

```text
Connection timed out
```

Possible causes:

```text
Wrong IP
Device offline
Routing issue
Firewall/ACL
Wrong port
SSH service unavailable
Network latency
```

A timeout is evidence, not a final diagnosis.

---

# 🧾 55. TROUBLESHOOTING EVIDENCE TABLE

Create a simple table during incidents:

| Test | Result | Meaning |
|---|---|---|
| Ping gateway | Pass | Local L3 path likely works |
| Ping server | Fail | Continue investigation |
| Route lookup | Present | Routing path exists |
| ACL review | Deny found | Strong evidence |
| TCP test | Fail | Service/path issue remains |
| DNS lookup | Pass | DNS likely not primary issue |

This prevents circular troubleshooting.

---

# 🧠 56. LAST KNOWN GOOD STATE

Ask:

> **“When did this work?”**

Then identify what changed.

```text
Working at 09:55
      ↓
Configuration changed 10:02
      ↓
Failure 10:04
```

The change is a valuable investigation lead.

### ⚠️ But correlation is not automatically causation.

Verify.

---

# 🏢 57. ENTERPRISE TROUBLESHOOTING WORKFLOW

A professional incident can look like:

```text
🚨 Alert / User report
        ↓
🎯 Scope + severity
        ↓
📊 Evidence collection
        ↓
🧠 Hypothesis
        ↓
🧪 Controlled testing
        ↓
🔧 Remediation
        ↓
✅ Verification
        ↓
📝 Documentation
        ↓
📚 Root Cause Analysis
```

---

# 📋 58. INCIDENT NOTES TEMPLATE

During a real incident, record:

```text
Incident ID:
Date/time:
Reported by:
Affected service:
Affected users:
Impact:
Start time:
Last known good:
Recent changes:
Tests performed:
Evidence:
Root cause:
Fix:
Verification:
Rollback required?:
Follow-up actions:
```

This turns troubleshooting into an auditable engineering process.

---

# 🧠 59. ROOT CAUSE ANALYSIS — 5 WHYS

Example:

### Problem

```text
Users lost connectivity.
```

### Why 1?

```text
Access switch uplink went down.
```

### Why 2?

```text
Interface was administratively shut down.
```

### Why 3?

```text
A maintenance change disabled it.
```

### Why 4?

```text
Engineer used the wrong interface.
```

### Why 5?

```text
Change procedure lacked peer verification.
```

Now the root cause is not simply:

> “Interface was down.”

The deeper process problem has been identified.

---

# 🚫 60. COMMON TROUBLESHOOTING MISTAKES

## ❌ Mistake 1 — Guessing

“Maybe OSPF is broken.”

Without evidence, that's only a hypothesis.

---

## ❌ Mistake 2 — Changing Everything

Creates new variables and destroys your baseline.

---

## ❌ Mistake 3 — Ignoring Layer 1

A bad cable can look like a routing problem from the user's perspective.

---

## ❌ Mistake 4 — Trusting Ping Completely

Ping success does not prove the application works.

---

## ❌ Mistake 5 — Ignoring Logs

The device may already be telling you what happened.

---

## ❌ Mistake 6 — Forgetting Security Controls

ACLs, firewalls, authentication, and endpoint security can intentionally block traffic.

---

## ❌ Mistake 7 — No Verification

A change is not complete until the expected result is verified.

---

## ❌ Mistake 8 — No Documentation

If you don't document the fix, the next engineer may repeat the same investigation.

---

# ⚡ 61. THE TROUBLESHOOTING COMMAND CHEAT WALL

## Layer 1 🔌

```cisco
show interfaces
show interfaces status
show ip interface brief
```

## Layer 2 🏷️

```cisco
show vlan brief
show interfaces switchport
show interfaces trunk
show mac address-table
show spanning-tree
show etherchannel summary
```

## Layer 3 🌐

```cisco
show arp
show ip route
show ip protocols
show ip interface
```

## OSPF 🛰️

```cisco
show ip ospf
show ip ospf interface
show ip ospf neighbor
show ip route ospf
```

## Security 🛡️

```cisco
show access-lists
show running-config
```

## NAT 🔄

```cisco
show ip nat translations
show ip nat statistics
```

## Management 📊

```cisco
show logging
show clock
show ntp status
show cdp neighbors
show lldp neighbors
```

---

# 🧭 62. THE UNIVERSAL TROUBLESHOOTING TREE

When you are stuck, return here.

```text
                 🚨 PROBLEM
                     │
                     ▼
              Define the scope
                     │
                     ▼
             Is the link alive?
                /          \
              NO            YES
              │              │
          Layer 1       Check Layer 2
                             │
                             ▼
                       VLAN / STP / LAG
                             │
                             ▼
                       Check Layer 3
                             │
                             ▼
                     IP / Gateway / Route
                             │
                             ▼
                       Check policy
                             │
                             ▼
                       ACL / Firewall
                             │
                             ▼
                     Check service
                             │
                             ▼
                    TCP/UDP/Application
                             │
                             ▼
                         VERIFY ✅
                             │
                             ▼
                      DOCUMENT 📝
```

---

# 🧠 63. THE 10 QUESTIONS EVERY ENGINEER SHOULD ASK

When facing a network problem:

1. **What exactly is broken?**
2. **Who is affected?**
3. **When did it start?**
4. **What changed recently?**
5. **What is the last known good state?**
6. **Where is the last known good point?**
7. **What evidence do I have?**
8. **What hypothesis does that evidence support?**
9. **What is the smallest safe test I can perform?**
10. **How will I verify and document the fix?**

Memorize these.

They are more valuable than memorizing 500 random commands.

---

# 🎯 64. SCENARIO-BASED CHALLENGE — LEVEL 1 🟢

A user cannot access the internet.

You discover:

```text
IP address     → valid
Gateway        → reachable
DNS            → working
Internet IP    → reachable
```

Question:

> What should you investigate next?

Think about:

```text
Application
TCP port
Proxy
Firewall
Browser
Remote service
```

Do not automatically reboot the switch. 😎

---

# 🎯 65. LEVEL 2 🟡

Two switches are connected.

Users on VLAN 10 work locally but cannot communicate across the switches.

You verify:

```text
VLAN 10 exists on both
Access ports correct
Interfaces up
```

What next?

> 🔥 Investigate the trunk and whether VLAN 10 is allowed/active across it.

---

# 🎯 66. LEVEL 3 🟠

OSPF neighbor is stuck in an unexpected state.

You discover:

```text
IP connectivity → works
Area            → matches
Hello timers    → match
Authentication  → matches
MTU             → mismatch
```

What is the strongest clue?

> 🧠 **MTU mismatch.**

Now investigate the appropriate interface settings and platform behavior before making changes.

---

# 🎯 67. LEVEL 4 🔴

A business application is unreachable from one branch.

Evidence:

```text
Branch gateway → reachable
Core → reachable
Server IP → reachable
DNS → works
TCP application port → fails
Other applications → work
```

What does this suggest?

Potentially:

```text
Application service
Server firewall
Network ACL
TCP port policy
Load balancer/service issue
```

The network path is not automatically the culprit.

---

# 👑 68. BOSS LEVEL — FULL ENTERPRISE INCIDENT

### 🏢 NH Technologies

Topology:

```text
                🌐 Internet
                    │
                 Edge R1
                    │
                 Core SW
               /         \
          Access SW1     Access SW2
           /    \          /    \
         PC-A   PC-B     PC-C   PC-D
```

At 11:20 AM:

```text
PC-A → everything works
PC-B → internet works
PC-C → internet fails
PC-D → internet fails
```

Additional information:

```text
PC-C and PC-D are on VLAN 30
PC-A and PC-B are on VLAN 10
```

### Your mission 🔎

Build the investigation tree.

```text
Affected users
      ↓
Common VLAN?
      ↓
VLAN 30 exists?
      ↓
Access ports correct?
      ↓
Trunk carries VLAN 30?
      ↓
SVI operational?
      ↓
Default gateway reachable?
      ↓
Route exists?
      ↓
NAT/ACL policy?
      ↓
Internet test
```

### 🚨 Don't jump to NAT.

First prove the packet reaches the edge.

### Portfolio challenge

Document:

```text
Symptoms
Scope
Hypotheses
Commands
Evidence
Root cause
Fix
Verification
Preventive action
```

This can become a **real troubleshooting case study in your cybersecurity/network portfolio.** 🔥

---

# 📚 69. PRACTICE EXERCISES

## 🧩 Exercise 1

Explain the difference between:

```text
Symptom
Cause
Root cause
```

---

## 🧩 Exercise 2

A host can ping its gateway but cannot ping a remote subnet.

List five possible causes.

---

## 🧩 Exercise 3

A host can ping `1.1.1.1` but cannot resolve domain names.

What subsystem should you investigate first?

---

## 🧩 Exercise 4

Two switches can ping each other's management IPs, but VLAN 20 users cannot communicate.

What Layer 2 checks would you perform?

---

## 🧩 Exercise 5

OSPF adjacency fails.

Create a checklist containing at least 10 verification points.

---

## 🧩 Exercise 6

A production change fixes the outage, but you don't know why.

Why is this dangerous?

---

# 🎤 70. INTERVIEW QUESTIONS

### Q1. What is network troubleshooting?

A systematic process of identifying, isolating, diagnosing, resolving, and verifying network problems.

### Q2. What is the difference between a symptom and root cause?

A symptom is an observed effect; the root cause is the underlying reason the failure occurred.

### Q3. What is the first thing you should do during an incident?

Define the problem and scope before making changes.

### Q4. What does `show ip interface brief` help you understand?

Interface IP addressing and high-level operational/admin status.

### Q5. What does `show ip route` show?

The router's IP routing table and the routes it currently knows.

### Q6. What does `show vlan brief` help verify?

VLAN existence and access-port membership at a high level.

### Q7. Why can ping fail even when a server is running?

ICMP can be filtered, routing can fail, ACL/firewall policy can block it, or another network problem can prevent the echo exchange.

### Q8. Why is `show logging` useful?

It provides event information and helps establish a timeline.

### Q9. Why should you avoid changing many things at once?

You lose the ability to identify which change affected the outcome and may introduce additional problems.

### Q10. What is divide-and-conquer troubleshooting?

Testing sections of a path to isolate where the failure begins.

### Q11. What is a last known good state?

The most recent confirmed state in which the service worked normally.

### Q12. What should you do after applying a fix?

Verify the expected behavior, monitor for stability, and document the resolution.

---

# ⚡ 71. QUICK REVISION

```text
Troubleshooting
      ↓
Define problem
      ↓
Gather evidence
      ↓
Hypothesis
      ↓
Test
      ↓
Controlled fix
      ↓
Verify
      ↓
Document
```

### Layer mindset

```text
L1 → Cable / Link / Interface
L2 → VLAN / STP / EtherChannel / MAC
L3 → IP / ARP / Routing / OSPF
L4 → TCP / UDP / Ports
L7 → DNS / HTTP / Application
```

### Core commands

```text
show interfaces
show ip interface brief
show vlan brief
show interfaces trunk
show spanning-tree
show etherchannel summary
show mac address-table
show arp
show ip route
show ip ospf neighbor
show access-lists
show ip nat translations
show logging
```

---

# 🧠 72. THE GOLDEN TROUBLESHOOTING FORMULA

Remember this forever:

```text
             🔎 OBSERVE
                 ↓
             🎯 DEFINE
                 ↓
             🧠 THINK
                 ↓
             🧪 TEST
                 ↓
             🔧 FIX
                 ↓
             ✅ VERIFY
                 ↓
             📝 DOCUMENT
```

Or even shorter:

> **Evidence → Hypothesis → Test → Fix → Verify.**

---

# 🏆 73. FINAL ENGINEER MINDSET

A junior engineer says:

> “The network is down.”

A better engineer says:

> “VLAN 30 clients cannot reach their default gateway, while VLAN 10 remains healthy.”

An even better engineer says:

> “VLAN 30 clients receive DHCP addresses, but ARP resolution for the SVI fails. The access ports are correct, and VLAN 30 is absent from the uplink trunk. Evidence points to a Layer 2 VLAN propagation issue.”

🔥 **That is engineering.**

The goal is not to know every command.

The goal is to turn:

```text
CONFUSION 😵
     ↓
OBSERVATION 👀
     ↓
EVIDENCE 🔎
     ↓
REASONING 🧠
     ↓
DIAGNOSIS 🎯
     ↓
RESOLUTION 🔧
```

---

# 🚀 74. LEARNER CHALLENGE

Complete these without looking at the answers:

### 🟢 Rookie

- Explain bottom-up troubleshooting.
- Explain symptom vs root cause.
- Memorize the core `show` commands.

### 🟡 Operator

- Troubleshoot VLAN/trunk problems.
- Troubleshoot routing problems.
- Diagnose DNS vs IP connectivity.

### 🟠 Engineer

- Diagnose OSPF adjacency problems.
- Analyze ACL/NAT failures.
- Build evidence tables.

### 🔴 Senior Engineer

- Troubleshoot multi-layer incidents.
- Build a timeline from logs.
- Identify the last known good state.
- Write a root-cause analysis.

### 👑 Boss Level

Take a broken Packet Tracer/EVE-NG topology and intentionally introduce **10 different faults**.

Then:

```text
1. Give the lab to yourself without the fault list.
2. Troubleshoot from symptoms.
3. Record every hypothesis.
4. Record every command.
5. Identify each root cause.
6. Fix one issue at a time.
7. Verify the entire network.
8. Write a final incident report.
```

If you can do this independently, your troubleshooting ability has moved to another level. 🔥

---

# 🎯 FINAL TAKEAWAY

> **Troubleshooting is not command memorization. It is structured reasoning supported by evidence.**

You now have the bridge between:

```text
📚 CCNA THEORY
      ↓
🛠️ REAL NETWORK OPERATIONS
      ↓
🔎 DIAGNOSTICS
      ↓
🐍 AUTOMATION
      ↓
🛡️ CYBERSECURITY / SOC
```

**You don't just know how a network works anymore. You are learning how to prove why it doesn't.** 🚀

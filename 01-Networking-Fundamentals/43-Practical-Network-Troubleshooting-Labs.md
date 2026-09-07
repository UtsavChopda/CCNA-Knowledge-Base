# 🧪🔥 Module 43 — Practical Network Troubleshooting Labs

> **Mission:** Stop reading about broken networks. Break them, investigate them, fix them, and prove they work. 🛠️🔎

![CCNA](https://img.shields.io/badge/CCNA-Practical%20Labs-blue?style=for-the-badge)
![Labs](https://img.shields.io/badge/Labs-Hands--On-orange?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Rookie%20→%20Boss-success?style=for-the-badge)

---

# 🗺️ WHERE WE ARE

Module 42 taught the **method**.

Now Module 43 gives you the **workbench**.

```text
Theory 📚
   ↓
Configuration ⚙️
   ↓
Troubleshooting Methodology 🧠
   ↓
🧪 PRACTICAL TROUBLESHOOTING LABS ← YOU ARE HERE
   ↓
Enterprise Failure Simulation 🏢
   ↓
Automation + Monitoring 🤖
   ↓
SOC / Blue Team 🛡️
```

This module is intentionally different from Module 42:

- Module 42 = **How to think**
- Module 43 = **How to practice**

---

# 🎯 1. LAB RULES

Before every lab, follow this workflow:

```text
🚨 Observe
   ↓
🎯 Define the symptom
   ↓
📋 Record baseline
   ↓
🧠 Form hypothesis
   ↓
🔎 Collect evidence
   ↓
🧪 Test ONE thing
   ↓
🔧 Fix
   ↓
✅ Verify
   ↓
📝 Document
```

### 🚫 Golden rule

**Do not open the answer immediately.**

Try to solve each fault yourself first.

---

# 🧰 2. LAB ENVIRONMENT

Recommended platforms:

- Cisco Packet Tracer 🟢
- GNS3 🟡
- EVE-NG 🔥
- CML if available
- Physical Cisco equipment if available

Suggested topology:

```text
                 🌐 ISP
                  │
                 R1
                  │
              CORE-SW
             /       \
          SW1         SW2
         /   \       /   \
       PC1   PC2   PC3   PC4
```

You will progressively introduce failures into this environment.

---

# 🟢 3. LAB 01 — ADMINISTRATIVELY DOWN

## 🎯 Objective

Identify a Layer 1/interface-state problem.

### Symptom

PC1 suddenly has no connectivity.

### Investigate

```cisco
show ip interface brief
show interfaces status
```

### Fault to introduce

```cisco
interface gigabitEthernet0/1
 shutdown
```

### Questions

1. What is the interface state?
2. Is the failure physical or administrative?
3. What command restores it in a lab?

### Expected fix

```cisco
interface gigabitEthernet0/1
 no shutdown
```

### Verify

```cisco
show ip interface brief
```

Then test the endpoint.

---

# 🟢 4. LAB 02 — WRONG ACCESS VLAN

### Topology

```text
PC1
 │
Gi0/1
 │
SW1
```

Expected:

```text
PC1 → VLAN 10
```

Fault:

```text
PC1 → VLAN 20
```

### Evidence

```cisco
show vlan brief
show interfaces gigabitEthernet0/1 switchport
```

### Mission

Find the mismatch without changing the trunk or router.

### Fix

Configure the access port for the intended lab VLAN.

### Verification

```text
PC1
 ↓
Gateway
 ↓
Remote host
```

---

# 🟢 5. LAB 03 — VLAN DOES NOT EXIST

### Scenario

A new department was assigned VLAN 30.

Users cannot communicate.

Check:

```cisco
show vlan brief
```

### Fault

VLAN 30 was never created on the required switch.

### Mission

Determine:

```text
Does VLAN 30 exist?
Are ports assigned correctly?
Is the VLAN carried upstream?
```

### Lesson

> A port cannot meaningfully participate in a VLAN that is not available as required by the switch configuration.

---

# 🟢 6. LAB 04 — TRUNK ALLOWED VLAN MISSING

Topology:

```text
PC1 ─ SW1 ===== SW2 ─ PC2
          trunk
```

Both PCs belong to VLAN 20.

Local communication works.

Cross-switch communication fails.

### Check

```cisco
show interfaces trunk
```

### Fault

VLAN 20 is excluded from the trunk's allowed VLAN list.

### Investigation

```text
VLAN exists?       ✅
Access ports?       ✅
Trunk up?           ✅
VLAN allowed?       ❌
```

### Mission

Correct the trunk configuration in the lab and prove VLAN 20 traverses the link.

---

# 🟢 7. LAB 05 — NATIVE VLAN MISMATCH

Two switches report a trunk warning.

### Check

```cisco
show interfaces trunk
```

Compare both sides.

### Fault

```text
SW1 native VLAN = 99
SW2 native VLAN = 100
```

### Questions

- Why is consistency important?
- What traffic is associated with the native VLAN concept?
- What evidence appears on the devices?

### Mission

Make the lab intentionally inconsistent, observe the symptoms, then restore a consistent design.

---

# 🟡 8. LAB 06 — MAC ADDRESS TABLE INVESTIGATION

### Scenario

PC2 can reach the gateway, but another host cannot reach PC2.

Run:

```cisco
show mac address-table
```

### Mission

Find the MAC address associated with the endpoint.

Then ask:

```text
Which port learned it?
Is the port expected?
Is the MAC moving?
```

### Bonus

Move the endpoint to another port and observe how the switch learns the MAC address.

---

# 🟡 9. LAB 07 — STP BLOCKING PATH

Topology:

```text
        SW1
       /   \
      /     \
    SW2-----SW3
```

There is redundancy.

### Check

```cisco
show spanning-tree
show spanning-tree vlan 10
```

### Mission

Identify:

- Root bridge
- Root port
- Designated ports
- Alternate/blocking path

### Challenge

Explain why a blocked path is **not automatically a fault**.

> STP may be doing exactly what it was designed to do.

---

# 🟡 10. LAB 08 — ETHERCHANNEL MEMBER MISMATCH

Topology:

```text
SW1
 ║
 ║ 2 physical links
 ║
SW2
```

Expected:

```text
Port-Channel1 🟢
```

### Fault

Configure one member differently from the other.

### Investigate

```cisco
show etherchannel summary
show interfaces port-channel 1
```

Compare:

```text
Mode
VLAN configuration
Trunk state
Native VLAN
Allowed VLANs
```

### Mission

Find why the bundle is not operating as expected.

---

# 🟡 11. LAB 09 — WRONG IP ADDRESS

Topology:

```text
PC1 ─ SW1 ─ R1 ─ R2 ─ Server
```

PC1 should be:

```text
192.168.10.10/24
Gateway 192.168.10.1
```

Fault:

```text
192.168.20.10/24
```

### Tests

Windows:

```powershell
ipconfig /all
ping 192.168.10.1
```

### Mission

Identify the addressing mismatch.

---

# 🟡 12. LAB 10 — WRONG DEFAULT GATEWAY

PC configuration:

```text
IP:      192.168.10.50
Mask:    255.255.255.0
Gateway: 192.168.20.1   ❌
```

Correct gateway:

```text
192.168.10.1
```

### Investigation

```text
Local IP → looks valid
Mask     → looks valid
Gateway  → wrong subnet
```

### Mission

Explain why local communication may still behave differently from remote communication.

---

# 🟡 13. LAB 11 — MISSING ROUTE

Topology:

```text
LAN-A ─ R1 ─ R2 ─ LAN-B
```

### Symptom

LAN-A can reach R1.

LAN-A cannot reach LAN-B.

### Check

On R1:

```cisco
show ip route
```

On R2:

```cisco
show ip route
```

### Mission

Determine whether both routers know how to reach the opposite LAN.

### Key lesson

> Routing is bidirectional in the practical sense that return traffic also needs a valid path.

---

# 🟡 14. LAB 12 — WRONG STATIC ROUTE NEXT HOP

### Fault

R1 has a route toward LAN-B, but the next hop points to an incorrect address.

### Evidence

```cisco
show ip route
show ip interface brief
```

### Mission

Trace the path:

```text
PC
 ↓
R1
 ↓
next hop
 ↓
R2
 ↓
Server
```

Test each stage instead of only testing the final destination.

---

# 🟠 15. LAB 13 — OSPF NETWORK STATEMENT / INTERFACE ISSUE

### Scenario

R1 and R2 are physically connected.

IP addresses are correct.

But:

```text
show ip ospf neighbor
→ no neighbor
```

### Investigate

```cisco
show ip ospf neighbor
show ip ospf interface
show ip protocols
show ip interface brief
```

### Fault candidates

Introduce one at a time:

```text
Wrong area
Passive interface
Wrong network selection
Authentication mismatch
Hello/dead mismatch
Network-type mismatch
```

### Mission

For every fault, write:

```text
Symptom
Evidence
Root cause
Fix
Verification
```

---

# 🟠 16. LAB 14 — OSPF MTU INVESTIGATION

### Scenario

OSPF neighbors appear but do not reach the expected Full state.

### Check

```cisco
show ip ospf neighbor
show interfaces
```

Compare the relevant interface MTU values.

### Mission

Understand why MTU can affect OSPF database exchange.

> Don't memorize “MTU = OSPF problem.” Understand **why** a mismatch can matter.

---

# 🟠 17. LAB 15 — DHCP FAILURE

Topology:

```text
Client VLAN 10
      ↓
Access SW
      ↓
Router/L3 SW
      ↓
DHCP Server
```

### Symptom

Client receives no usable IPv4 configuration.

### Investigation

Check:

```text
Client VLAN
DHCP scope
Relay configuration
Server reachability
ACL/policy
```

Cisco examples:

```cisco
show ip dhcp binding
show ip dhcp pool
show running-config | include helper
```

### Packet-thinking challenge

Explain:

```text
Discover
 ↓
Offer
 ↓
Request
 ↓
ACK
```

At which stage do you think the transaction stops?

---

# 🟠 18. LAB 16 — DNS VS INTERNET FAILURE

### Scenario

User says:

> “Internet is completely down.”

Tests:

```text
ping gateway      → ✅
ping 1.1.1.1      → ✅
nslookup example  → ❌
```

### Diagnosis direction

Investigate DNS.

### Mission

Do not touch the router configuration until your evidence supports a routing problem.

---

# 🟠 19. LAB 17 — ACL BLOCKS ONLY ONE SERVICE

Topology:

```text
Client ─ R1 ─ Server
```

Symptoms:

```text
Ping       → works
SSH        → fails
HTTPS      → works
```

### Think

This is more specific than “server unreachable.”

Possible cause:

```text
TCP/22 filtering
```

### Investigate

```cisco
show access-lists
show running-config | include access-group
```

### Mission

Identify the policy entry responsible.

Then verify other permitted traffic remains functional.

---

# 🔴 20. LAB 18 — NAT TRANSLATION FAILURE

Topology:

```text
LAN → Edge Router → ISP
```

### Symptom

Inside hosts can reach the gateway but cannot access an external test destination.

### Investigate

```cisco
show ip nat translations
show ip nat statistics
show ip route
show ip interface brief
```

### Questions

```text
Are inside/outside interfaces correct?
Does traffic match the NAT rule?
Is routing available?
Are translations appearing?
```

### Mission

Separate:

```text
Routing problem
vs
NAT problem
vs
Policy problem
```

---

# 🔴 21. LAB 19 — SSH ACCESS FAILURE

### Symptom

```text
ping device IP → works
SSH             → fails
```

### Investigate

```cisco
show ip ssh
show running-config | section line vty
show access-lists
```

### Possible faults

```text
SSH not enabled
VTY restriction
ACL blocking TCP/22
Authentication problem
Wrong username/password
```

### Mission

Prove which layer of the SSH journey is failing.

---

# 🔴 22. LAB 20 — INTERMITTENT CONNECTIVITY

### Symptom

```text
Ping:
✅
❌
✅
❌
```

### Don't assume

```text
“It must be Wi-Fi.”
```

Investigate:

```text
Interface errors
ARP behavior
Duplicate IP
STP changes
Routing instability
Physical media
CPU/resource issues
```

Useful commands may include:

```cisco
show interfaces
show arp
show spanning-tree
show logging
show ip route
```

### Mission

Find a fault that explains the **intermittent** nature, not merely a fault that could theoretically cause an outage.

---

# 🔴 23. LAB 21 — DUPLICATE IP INVESTIGATION

Two endpoints are configured with:

```text
192.168.10.50
```

### Symptoms

```text
Connectivity changes randomly
ARP entries change
Application sessions behave strangely
```

### Investigation

Compare:

```text
IP
MAC
ARP
DHCP lease
Switchport
```

### Mission

Identify the two physical endpoints associated with the conflicting address.

---

# 🔴 24. LAB 22 — SYSLOG TIMELINE

Create a failure and inspect:

```cisco
show logging
```

Build a timeline:

```text
10:00 → normal
10:03 → interface event
10:03 → routing adjacency change
10:04 → route disappears
10:05 → user outage
```

### Mission

Determine which event happened first.

### 🧠 Lesson

> **Time ordering is evidence.**

---

# 🔴 25. LAB 23 — NTP / INCIDENT CORRELATION

Give two lab devices different clocks.

Then simulate an event.

Compare their logs.

### Mission

Explain why inconsistent timestamps make incident investigation harder.

Restore synchronized time in the lab and compare the difference.

---

# 🔴 26. LAB 24 — PACKET CAPTURE: DHCP

Use Wireshark or another capture tool where supported.

Capture a DHCP exchange.

Identify:

```text
DHCP Discover
DHCP Offer
DHCP Request
DHCP ACK
```

### Challenge

Find:

- Source/destination addressing
- UDP ports
- Transaction identifier
- Offered address information

### Bonus

Break DHCP and capture the failure.

Compare:

```text
Healthy capture
vs
Broken capture
```

---

# 🔥 27. LAB 25 — PACKET CAPTURE: DNS

Capture a DNS lookup.

Observe:

```text
Client
 ↓ UDP DNS request
DNS server
 ↓ response
Client
```

Identify:

```text
Query name
Query type
Response
Answer
```

Then introduce a DNS configuration error and compare the packets.

---

# 🔥 28. LAB 26 — PACKET CAPTURE: TCP HANDSHAKE

Capture a TCP connection.

Look for:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

### Then break the path

Introduce a policy blocking the destination port.

Compare the capture.

### Mission

Understand the difference between:

```text
No response
RST
Successful handshake
```

---

# 🏢 29. LAB 27 — NH TECHNOLOGIES BRANCH OUTAGE

### Enterprise topology

```text
                         🌐 Internet
                              │
                            EDGE-R1
                              │
                         CORE-SW1
                        /         \
                   DIST-1        DIST-2
                  /   \           /   \
               SW-A   SW-B     SW-C   SW-D
                │       │        │       │
              Users   Users    Users   Users
```

### Incident

```text
Branch users report:
“CRM is slow.”
```

But:

```text
Internet → works
Email    → works
DNS      → works
CRM      → slow
```

### Your mission

Do not conclude “network is slow.”

Measure:

```text
Latency
Packet loss
Path
TCP connectivity
Application port
Interface errors
CPU/resource indicators
```

### Deliverable

Write a mini incident report.

---

# 👑 30. LAB 28 — 10 FAULTS, ONE TOPOLOGY

This is the **ultimate lab**.

Start with:

```text
                 R1
                 │
             CORE-SW
             /     \
           SW1     SW2
          /  \     /  \
        PC1 PC2  PC3 PC4
```

Introduce exactly **10 faults**.

Suggested fault bank:

```text
1. Shutdown interface
2. Wrong access VLAN
3. Missing VLAN
4. Trunk allowed-list error
5. Native VLAN mismatch
6. Wrong host IP
7. Wrong default gateway
8. Missing route
9. OSPF mismatch
10. ACL policy error
```

### Rules

You are not allowed to look at the fault list after introducing the faults.

Then troubleshoot from scratch.

For each issue record:

```text
#
Symptom
Evidence
Hypothesis
Test
Root cause
Fix
Verification
```

### 🏆 Passing target

```text
10/10 root causes identified
10/10 fixed
0 unnecessary configuration resets
Complete documentation
```

---

# 🧠 31. TROUBLESHOOTING SCORECARD

Score yourself honestly.

| Skill | Rookie | Operator | Engineer | Expert |
|---|---:|---:|---:|---:|
| Define scope | 1 | 2 | 3 | 4 |
| Gather evidence | 1 | 2 | 3 | 4 |
| Layer isolation | 1 | 2 | 3 | 4 |
| Cisco show commands | 1 | 2 | 3 | 4 |
| Packet analysis | 1 | 2 | 3 | 4 |
| Root-cause analysis | 1 | 2 | 3 | 4 |
| Change discipline | 1 | 2 | 3 | 4 |
| Documentation | 1 | 2 | 3 | 4 |

### Interpretation

```text
8–14   → Keep practicing 🟢
15–22  → Solid operator 🟡
23–28  → Engineer mindset 🟠
29–32  → Troubleshooting beast 🔥
```

---

# 📝 32. LAB REPORT TEMPLATE

For every serious lab, create:

```markdown
# Incident: <name>

## Symptom

## Scope

## Topology

## Baseline

## Hypotheses

## Tests Performed

## Evidence

## Root Cause

## Remediation

## Verification

## Preventive Action

## Lessons Learned
```

This turns Packet Tracer practice into portfolio evidence.

---

# 🛡️ 33. SECURITY VERSION OF THE LABS

Repeat selected labs from an attacker/defender perspective.

Examples:

### Rogue DHCP

Ask:

```text
What happens if a malicious DHCP server answers clients?
```

### ARP spoofing

Ask:

```text
How would ARP behavior change?
What logs/telemetry could reveal it?
```

### ACL mistake

Ask:

```text
Could a security rule accidentally block business traffic?
```

### Port security

Ask:

```text
Could a security violation cause an interface outage?
```

The goal is to connect **network troubleshooting → defensive security thinking**.

---

# 🐍 34. AUTOMATE THE LAB

Once you can troubleshoot manually, automate evidence collection.

Example workflow:

```text
Python 🐍
   ↓
SSH to device
   ↓
Collect show commands
   ↓
Save outputs
   ↓
Parse results
   ↓
Flag anomalies
   ↓
Generate report
```

Example evidence checklist:

```text
show ip interface brief
show interfaces
show vlan brief
show interfaces trunk
show spanning-tree
show etherchannel summary
show arp
show ip route
show logging
```

### 🧠 Important

Automation should collect evidence first.

Do not build a script that blindly “fixes” production devices.

---

# 🎯 35. FINAL BOSS CHALLENGE

Build a complete lab with:

```text
2 Routers
3 Switches
4 VLANs
OSPF
DHCP
DNS concept
ACL
NAT
SSH
Syslog
NTP
EtherChannel
STP
```

Then introduce **15 faults**.

Your goal:

```text
🕵️ Investigate
🧠 Reason
🧪 Test
🔧 Fix
📊 Verify
📝 Document
```

### Final deliverables

Create:

```text
01-topology.png
02-addressing-table.md
03-fault-list-private.md
04-troubleshooting-report.md
05-command-output.txt
06-lessons-learned.md
```

Keep the fault list private until you finish the challenge.

---

# 🎤 36. INTERVIEW CONNECTION

After completing the labs, you should be able to answer:

### “How do you troubleshoot a network outage?”

Not with:

> “I run ping.”

Instead:

```text
I first define scope and impact.
Then establish a baseline and identify the last known good state.
I follow the traffic path and isolate the failure domain.
I collect evidence using device state, routing information,
logs, packet captures, and endpoint tests.
I form and test hypotheses systematically.
After remediation I verify end-to-end behavior and document the root cause.
```

🔥 That sounds like an engineer.

---

# ⚡ 37. QUICK COMMAND WALL

```cisco
show ip interface brief
show interfaces
show interfaces status
show vlan brief
show interfaces switchport
show interfaces trunk
show mac address-table
show spanning-tree
show etherchannel summary
show arp
show ip route
show ip protocols
show ip ospf neighbor
show ip ospf interface
show access-lists
show ip nat translations
show ip nat statistics
show logging
show clock
show ntp status
show cdp neighbors
show lldp neighbors
```

Endpoint tools:

```text
Windows:
ipconfig /all
ping
tracert
nslookup

Linux:
ip addr
ip route
ping
traceroute
dig
ss
```

Packet analysis:

```text
Wireshark
 tcpdump
```

---

# 🏆 38. FINAL MENTAL MODEL

When the network breaks:

```text
                    🚨 SYMPTOM
                        │
                        ▼
                  🎯 DEFINE SCOPE
                        │
                        ▼
                  📊 BASELINE
                        │
                        ▼
                  🧠 HYPOTHESIS
                        │
                        ▼
                  🔎 EVIDENCE
                        │
                        ▼
                    🧪 TEST
                        │
                        ▼
                     🔧 FIX
                        │
                        ▼
                    ✅ VERIFY
                        │
                        ▼
                   📝 DOCUMENT
```

Then repeat until the system is understood.

---

# 🚀 39. WHAT YOU SHOULD BE ABLE TO DO NOW

After completing Modules 42 + 43, you should no longer see a broken topology as:

```text
😵 “Something is wrong.”
```

You should see:

```text
🧠 A finite set of possible failure domains.
```

You should be able to move from:

```text
User complaint
      ↓
Network symptom
      ↓
Layer isolation
      ↓
Evidence
      ↓
Root cause
      ↓
Safe remediation
      ↓
Verified recovery
```

### 🔥 This is the skill that separates configuration practice from real network engineering.

---

# 🧭 NEXT STOP

You have now moved from:

```text
📚 Learn networking
        ↓
⚙️ Configure networking
        ↓
🛠️ Troubleshoot networking
        ↓
🧪 Break & repair networking
```

The next stage is to turn all of this into **repeatable enterprise-grade workflows, projects, and portfolio evidence**.

> **Don't just build a network. Build the ability to understand it when everything goes wrong.** 🛡️🔥

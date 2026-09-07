# 15. VLAN Trunking Protocols and Dynamic Trunking (DTP)

> **Learning path:** Networking Fundamentals → VLANs → Trunking → **DTP & Dynamic Trunking**

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain what Dynamic Trunking Protocol (DTP) is.
- Explain why Cisco introduced DTP.
- Distinguish between static trunking and dynamically negotiated trunking.
- Understand `access`, `trunk`, `dynamic auto`, and `dynamic desirable` modes.
- Predict whether two Cisco switch ports will form a trunk.
- Explain `switchport nonegotiate` and when it should be used.
- Verify DTP and trunk status using Cisco IOS commands.
- Troubleshoot trunk negotiation failures.
- Recognize security risks associated with automatic trunk negotiation.
- Build and troubleshoot practical DTP labs in Packet Tracer.

---

## 1. What Is DTP?

**Dynamic Trunking Protocol (DTP)** is a **Cisco proprietary Layer 2 protocol** used by compatible Cisco switch interfaces to negotiate whether a link should operate as a trunk.

Instead of manually configuring both ends as trunks, DTP can allow compatible interfaces to negotiate trunking.

### Simple analogy 🏢

Imagine two office buildings connected by a road.

- **Access link:** the road carries traffic for one department.
- **Trunk link:** the road is shared by many departments, and each vehicle carries a department label.
- **DTP:** the two buildings discuss whether their connecting road should become a shared multi-department road.

DTP helps the switches negotiate the trunk relationship, but in production networks **explicit configuration is generally preferred** because it is more predictable and secure.

---

## 2. Why Does DTP Exist?

Suppose two switches need to carry VLAN 10, VLAN 20, and VLAN 30 across their inter-switch link.

A trunk is required.

Without dynamic negotiation, an administrator can explicitly configure:

```cisco
Switch(config-if)# switchport mode trunk
```

DTP provides another possibility: compatible switch ports can negotiate trunking using their configured DTP modes.

### Important

DTP is **not** the same thing as VLAN Trunking Protocol (VTP).

| Protocol | Main purpose |
|---|---|
| DTP | Negotiates trunking state |
| VTP | Advertises/manages VLAN information in supported Cisco environments |
| STP | Prevents Layer 2 loops |
| CDP | Discovers Cisco neighbors |
| LLDP | Vendor-neutral neighbor discovery |
| 802.1Q | Defines VLAN tagging on Ethernet trunks |

---

# 3. DTP Modes

The major switchport modes you should understand are:

| Mode | Basic behavior |
|---|---|
| `access` | Forces the interface to operate as an access port |
| `trunk` | Forces the interface to operate as a trunk |
| `dynamic auto` | Passively waits for the neighbor to request trunking |
| `dynamic desirable` | Actively attempts to negotiate trunking |
| `nonegotiate` | Stops DTP negotiation frames on a statically configured interface |

> **Platform note:** Exact defaults and available commands vary by Cisco platform and IOS/IOS-XE release. Always verify behavior on the actual switch with `show interfaces switchport` and `show dtp interface` when available.

---

## 4. `switchport mode access`

This mode forces an interface to be an access port.

Example:

```cisco
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

The port is intended for an endpoint such as:

- PC
- Printer
- IP phone access/data configuration
- Camera
- Access point in a specific design

An access port normally carries traffic for one data VLAN without carrying multiple VLANs using an 802.1Q trunk.

### Security recommendation 🔐

User-facing ports should normally be explicitly configured as access ports instead of being left in a negotiating mode.

---

# 5. `switchport mode trunk`

This explicitly configures the interface as a trunk.

```cisco
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
```

On platforms that support multiple trunk encapsulations, you may encounter:

```cisco
Switch(config-if)# switchport trunk encapsulation dot1q
```

Modern platforms commonly use 802.1Q directly and may not provide that encapsulation command.

### Verify

```cisco
Switch# show interfaces trunk
Switch# show interfaces gigabitEthernet 0/1 switchport
```

---

# 6. `dynamic auto`

`dynamic auto` is a **passive** DTP mode.

The port does not aggressively try to create a trunk. It can become a trunk if the neighboring compatible port actively negotiates trunking.

Example:

```cisco
Switch(config-if)# switchport mode dynamic auto
```

### Key idea

> **Auto waits. Desirable asks.**

---

# 7. `dynamic desirable`

`dynamic desirable` actively attempts to negotiate a trunk with a compatible neighboring port.

Example:

```cisco
Switch(config-if)# switchport mode dynamic desirable
```

If the other end is configured with a compatible negotiating mode, the interfaces can form a trunk.

### Key idea

> **Desirable actively tries to make a trunk.**

---

# 8. DTP Negotiation Matrix ⭐

This is one of the most important parts of the topic.

| Side A | Side B | Expected result |
|---|---|---|
| `dynamic desirable` | `dynamic desirable` | Trunk |
| `dynamic desirable` | `dynamic auto` | Trunk |
| `dynamic auto` | `dynamic desirable` | Trunk |
| `dynamic auto` | `dynamic auto` | Non-trunk/access |
| `trunk` | `dynamic auto` | Trunk |
| `trunk` | `dynamic desirable` | Trunk |
| `trunk` | `trunk` | Trunk |
| `access` | `dynamic desirable` | Access/non-trunk |
| `access` | `dynamic auto` | Access/non-trunk |
| `access` | `access` | Access |

### Easy memory trick 🧠

```text
Desirable = actively negotiates
Auto      = passively negotiates

Desirable + Auto      → Trunk
Desirable + Desirable → Trunk
Auto + Auto           → No dynamic trunk
```

> Do not memorize this table blindly for every Cisco platform. Use the actual platform documentation and verification commands when behavior differs.

---

# 9. Static Trunking vs Dynamic Trunking

| Feature | Static trunk | Dynamic trunk |
|---|---|---|
| Configuration | Explicit | Negotiated |
| Typical command | `switchport mode trunk` | `dynamic auto/desirable` |
| Predictability | High | Lower |
| Operational simplicity | Clear | Can be convenient |
| Security | Easier to harden | More negotiation exposure |
| Recommended for production | Commonly preferred | Use only when intentionally designed |

### Practical recommendation

For an important switch-to-switch link, configure both sides intentionally:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
```

Then harden the trunk according to the network design.

---

# 10. What Is `switchport nonegotiate`?

`switchport nonegotiate` prevents the interface from sending DTP negotiation frames.

Example:

```cisco
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport nonegotiate
```

The key concept is:

> **The trunk is statically configured; DTP negotiation is disabled.**

This is useful when the peer is also statically configured and you do not need DTP negotiation.

### Important caution ⚠️

Do not simply add `nonegotiate` without understanding the peer configuration.

A statically configured trunk on one side should connect to a peer that is intentionally configured to operate as a trunk. Verify the resulting state.

---

# 11. DTP and 802.1Q Are Different Things

This distinction is extremely important.

### DTP

DTP answers approximately:

> “Should this link operate as a trunk?”

### 802.1Q

802.1Q defines how VLAN information is represented on Ethernet trunks.

For example, a trunk can carry:

```text
VLAN 10 → tagged
VLAN 20 → tagged
VLAN 30 → tagged
```

The protocols solve different problems.

---

# 12. Native VLAN and DTP

A trunk can have a native VLAN.

Example:

```cisco
Switch(config-if)# switchport trunk native vlan 99
```

Traffic belonging to the native VLAN is normally sent untagged on an 802.1Q trunk.

### Security consideration

Use an intentionally selected native VLAN and keep native VLAN configuration consistent across the trunk.

Example:

```text
Switch A native VLAN 99
        │
        │ trunk
        │
Switch B native VLAN 99
```

A native VLAN mismatch can cause warnings and unexpected traffic handling.

---

# 13. Allowed VLANs on a Trunk

A trunk does not necessarily need to carry every VLAN.

Example:

```cisco
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

Verify:

```cisco
Switch# show interfaces trunk
```

### Why restrict VLANs?

It can:

- Reduce unnecessary broadcast traffic.
- Limit VLAN propagation.
- Improve operational clarity.
- Reduce the attack surface of a trunk.

---

# 14. Important Cisco Verification Commands ⭐

## 14.1 Check switchport state

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

Useful for checking:

- Administrative mode
- Operational mode
- Access VLAN
- Native VLAN
- Trunking status
- Negotiation information

---

## 14.2 Check active trunks

```cisco
show interfaces trunk
```

Useful for:

- Trunk ports
- Allowed VLANs
- Active VLANs
- Native VLAN
- VLANs forwarding on the trunk

---

## 14.3 Check DTP information

On platforms that support the command:

```cisco
show dtp interface gigabitEthernet 0/1
```

This can help identify DTP status and negotiation information.

---

## 14.4 Check running configuration

```cisco
show running-config interface gigabitEthernet 0/1
```

Look for:

```text
switchport mode trunk
switchport mode dynamic auto
switchport mode dynamic desirable
switchport nonegotiate
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30
```

---

## 14.5 Check VLANs

```cisco
show vlan brief
```

---

## 14.6 Check neighbors

Cisco discovery:

```cisco
show cdp neighbors
show cdp neighbors detail
```

Vendor-neutral discovery, where enabled:

```cisco
show lldp neighbors
show lldp neighbors detail
```

These commands help confirm that the device connected to the interface is actually the device you expect.

---

# 15. Endpoint Commands

DTP is a switch-to-switch protocol, so Windows and Linux do **not** configure DTP.

Therefore, DTP configuration commands on endpoint operating systems are:

> **Not applicable.**

However, endpoints can help verify the final result.

### Windows

```powershell
ipconfig /all
ping 192.168.10.20
arp -a
```

### Linux

```bash
ip addr
ip route
ip neigh
ping 192.168.10.20
```

These commands do not tell you the DTP negotiation state directly. Use Cisco switch commands for that.

---

# 16. Topology 1 — Auto + Auto

```text
      dynamic auto
SW1 ---------------- SW2
                     dynamic auto
```

### Expected behavior

The two ports are passive and do not dynamically establish a trunk with each other.

### Learning point

> Auto + Auto does not provide an active negotiation request.

---

# 17. Topology 2 — Desirable + Auto

```text
      dynamic desirable
SW1 ---------------- SW2
                       dynamic auto
```

### Expected behavior

The desirable side actively negotiates and the auto side can respond.

The link can become a trunk on compatible Cisco platforms/configurations.

---

# 18. Topology 3 — Desirable + Desirable

```text
      desirable
SW1 ---------------- SW2
      desirable
```

Both sides actively negotiate.

### Expected result

Trunk.

---

# 19. Topology 4 — Static Trunk + Auto

```text
      trunk
SW1 ---------------- SW2
                       auto
```

The statically configured trunk can establish the trunk relationship with the compatible auto side.

### Better production approach

Prefer intentional static configuration on both ends when possible.

---

# 20. Topology 5 — Static Trunk + `nonegotiate`

```text
        trunk + nonegotiate
SW1 ---------------- SW2
        trunk + nonegotiate
```

No DTP negotiation is required.

Both sides are explicitly configured as trunks.

This provides a predictable configuration.

---

# 21. Topology 6 — Access + Desirable

```text
       access
SW1 ---------------- SW2
                    desirable
```

The access port is not intended to form a trunk.

The link remains non-trunking.

### Lesson

An explicitly configured access port should not be treated as a dynamically negotiated trunk.

---

# 22. Topology 7 — Multi-Switch Chain

```text
PCs
 │
SW1 ===== SW2 ===== SW3
       trunks
```

Each inter-switch link can have its own DTP/static configuration.

Example:

```text
SW1 ↔ SW2 = trunk
SW2 ↔ SW3 = trunk
```

For VLAN 10 to travel from SW1 to SW3, VLAN 10 must be permitted and correctly configured along the required trunk path.

---

# 23. Topology 8 — Redundant Trunks + STP

```text
          +---------+
          |         |
         SW1=======SW2
          |         |
          +---------+
            trunk
```

If multiple Layer 2 paths exist, STP is responsible for preventing loops.

DTP does **not** replace STP.

Useful verification:

```cisco
show interfaces trunk
show spanning-tree
show spanning-tree vlan 10
```

---

# 24. Topology 9 — Rogue/Untrusted Device Risk

```text
Employee PC
     │
     │
Access switch port
     │
Untrusted device
```

Leaving user-facing interfaces in a dynamic negotiation mode can create unnecessary risk.

A malicious or unauthorized device may attempt to negotiate trunking on platforms/configurations where DTP is available.

### Defensive principle 🔐

User-facing ports should normally be:

```cisco
switchport mode access
```

and unused ports should be administratively disabled where appropriate:

```cisco
shutdown
```

Additional security controls depend on the environment.

---

# 25. Practical Lab 1 — DTP Mode Matrix ⭐

## Objective

Observe how different DTP modes affect trunk formation.

## Topology

```text
PC1 --- SW1 ================= SW2 --- PC2
```

## VLANs

| VLAN | Name |
|---:|---|
| 10 | USERS |
| 20 | SERVERS |

## Configure VLANs

On both switches:

```cisco
enable
configure terminal
vlan 10
 name USERS
vlan 20
 name SERVERS
end
```

## Test 1: Auto + Auto

SW1:

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

SW2:

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

Verify:

```cisco
show interfaces trunk
show interfaces gigabitEthernet 0/1 switchport
```

### Expected result

The link should not dynamically become a trunk.

---

## Test 2: Desirable + Auto

SW1:

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic desirable
```

SW2:

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

Verify:

```cisco
show interfaces trunk
show interfaces gigabitEthernet 0/1 switchport
```

### Expected result

The compatible interfaces should negotiate a trunk.

---

## Test 3: Desirable + Desirable

```cisco
SW1 = dynamic desirable
SW2 = dynamic desirable
```

Expected:

```text
Trunk
```

---

# 26. Practical Lab 2 — Static Trunk

## Objective

Configure an explicit trunk without relying on dynamic mode selection.

### SW1

```cisco
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
end
```

### SW2

```cisco
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
end
```

Verify:

```cisco
show interfaces trunk
```

### Expected result

The inter-switch link is operational as a trunk.

---

# 27. Practical Lab 3 — Static Trunk + Nonegotiate

## Objective

Disable DTP on a statically configured trunk.

### SW1

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport nonegotiate
```

### SW2

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport nonegotiate
```

Verify:

```cisco
show interfaces gigabitEthernet 0/1 switchport
show interfaces trunk
```

Where supported:

```cisco
show dtp interface gigabitEthernet 0/1
```

### Expected result

The interfaces operate as statically configured trunks without DTP negotiation.

---

# 28. Practical Lab 4 — VLAN Connectivity Across a Trunk

## Addressing Table

| Device | Interface | IP address | Mask | VLAN |
|---|---|---|---|---:|
| PC1 | NIC | 192.168.10.10 | 255.255.255.0 | 10 |
| PC2 | NIC | 192.168.10.20 | 255.255.255.0 | 10 |

## Topology

```text
PC1
 │
 │ VLAN 10
SW1 ================= SW2
          trunk
                     │
                     │ VLAN 10
                    PC2
```

Configure VLAN 10 on both switches and assign the endpoint ports to VLAN 10.

### SW1

```cisco
vlan 10
 name USERS

interface gigabitEthernet 0/2
 switchport mode access
 switchport access vlan 10

interface gigabitEthernet 0/1
 switchport mode trunk
```

### SW2

```cisco
vlan 10
 name USERS

interface gigabitEthernet 0/2
 switchport mode access
 switchport access vlan 10

interface gigabitEthernet 0/1
 switchport mode trunk
```

Configure the PCs with the addresses in the table.

Test:

```text
PC1 → ping 192.168.10.20
```

Verify on the switches:

```cisco
show vlan brief
show interfaces trunk
show mac address-table vlan 10
```

### Expected result

PC1 should be able to reach PC2 if the physical links, VLANs, trunk, and endpoint addressing are correct.

---

# 29. Practical Lab 5 — DTP Troubleshooting Challenge 🔧

## Scenario

You have:

```text
PC1 --- SW1 ===== SW2 --- PC2
```

PC1 and PC2 are supposed to be in VLAN 10.

The administrator says:

> “The switches are connected, but the VLAN is not passing between them.”

### SW1

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

### SW2

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

### Your task

1. Determine whether the link is a trunk.
2. Identify the reason VLAN 10 is not being carried as expected.
3. Change the configuration intentionally.
4. Verify the final state.

Useful commands:

```cisco
show interfaces gigabitEthernet 0/1 switchport
show interfaces trunk
show running-config interface gigabitEthernet 0/1
show vlan brief
```

### One possible correction

Use a static trunk:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
```

Configure the peer consistently and verify again.

---

# 30. Troubleshooting Methodology

When a trunk does not form, do not immediately change random commands.

Use this sequence:

```text
1. Check physical link
        ↓
2. Check interface status
        ↓
3. Check administrative mode
        ↓
4. Check operational mode
        ↓
5. Check DTP state
        ↓
6. Check trunk status
        ↓
7. Check allowed VLANs
        ↓
8. Check native VLAN
        ↓
9. Check VLAN existence
        ↓
10. Test end-to-end connectivity
```

---

# 31. Common DTP Problems

## Problem 1 — Auto + Auto

### Symptom

The expected trunk does not appear.

### Cause

Both sides are passive.

### Fix

Use intentional trunk configuration or a compatible negotiation mode.

---

## Problem 2 — One Side Access

### Symptom

The link does not operate as a trunk.

### Cause

One interface is explicitly forced to access mode.

### Fix

Check:

```cisco
show interfaces gigabitEthernet 0/1 switchport
```

---

## Problem 3 — `nonegotiate` Misunderstood

### Symptom

An administrator expects `nonegotiate` to create a trunk.

### Cause

`nonegotiate` disables DTP; it does not by itself turn an interface into a trunk.

### Correct concept

```cisco
switchport mode trunk
switchport nonegotiate
```

---

## Problem 4 — Native VLAN Mismatch

Check:

```cisco
show interfaces trunk
```

Ensure both sides intentionally use the same native VLAN when the design requires it.

---

## Problem 5 — VLAN Not Allowed

A trunk may be working while a particular VLAN is not being carried.

Check:

```cisco
show interfaces trunk
```

Then inspect:

```cisco
switchport trunk allowed vlan
```

---

## Problem 6 — VLAN Does Not Exist

Check:

```cisco
show vlan brief
```

Remember that a trunk carrying a VLAN does not automatically mean every switch has the correct local VLAN configuration for the intended design.

---

# 32. DTP vs STP

These protocols solve different problems.

| Protocol | Question it answers |
|---|---|
| DTP | Should this interface negotiate trunking? |
| STP | How do we prevent Layer 2 loops? |
| 802.1Q | How is VLAN information carried on a trunk? |
| VTP | How can VLAN information be distributed in supported Cisco designs? |

Example:

```text
DTP → trunk negotiation
802.1Q → VLAN tagging
STP → loop prevention
```

---

# 33. DTP Security Best Practices 🔐

A professional network should avoid unnecessary automatic behavior.

### User-facing ports

Prefer:

```cisco
interface range gigabitEthernet 0/2-24
 switchport mode access
```

Assign the correct VLAN explicitly.

### Inter-switch trunks

Prefer intentional configuration:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
```

Where appropriate and supported:

```cisco
switchport nonegotiate
```

### Additional trunk hardening

Use only required VLANs:

```cisco
switchport trunk allowed vlan 10,20,30
```

Use an intentionally selected native VLAN:

```cisco
switchport trunk native vlan 99
```

### Unused ports

Where appropriate:

```cisco
interface range gigabitEthernet 0/20-24
 shutdown
```

> Security design is broader than DTP. Consider port security, DHCP snooping, Dynamic ARP Inspection, BPDU Guard, VLAN design, management-plane protection, and other controls as you progress through CCNA.

---

# 34. Real-World Scenario — College Network 🏫

Imagine a college has:

```text
        Core/Distribution
             |
       +-----+-----+
       |           |
      SW1         SW2
       |           |
     PCs         PCs
```

VLANs:

| VLAN | Purpose |
|---:|---|
| 10 | Students |
| 20 | Faculty |
| 30 | Administration |
| 99 | Network management/native design example |

The inter-switch links must carry multiple VLANs.

### Professional approach

Rather than allowing user-facing interfaces to dynamically decide their role, the administrator explicitly defines:

```text
Endpoint port → access
Switch-to-switch → trunk
```

Then verifies:

```cisco
show interfaces trunk
show interfaces switchport
show vlan brief
```

This makes the network easier to audit and troubleshoot.

---

# 35. Scenario Challenge 🚨

You are the junior network engineer.

A new switch is connected to the existing network.

The administrator reports:

> “The physical link is up, but VLAN 20 users cannot communicate across the switches.”

You discover:

```text
SW1 Gi0/1 → dynamic desirable
SW2 Gi0/1 → dynamic auto
```

Then you check:

```cisco
show interfaces trunk
```

Suppose VLAN 20 is missing from the allowed VLAN list.

### Your tasks

1. Is DTP negotiation the only thing you need to check?
2. Is the link actually a trunk?
3. Is VLAN 20 created on both switches?
4. Is VLAN 20 allowed on the trunk?
5. Is the native VLAN consistent?
6. Are endpoint ports assigned to VLAN 20?
7. Can the hosts communicate after the correction?

### Lesson

A **trunk problem is not always a DTP problem**.

Always troubleshoot the entire VLAN path.

---

# 36. Practice Exercises ✍️

### Beginner

1. What is DTP?
2. Is DTP an open standard or Cisco proprietary protocol?
3. What does `dynamic auto` mean?
4. What does `dynamic desirable` mean?
5. What does `switchport mode trunk` do?
6. What does `switchport nonegotiate` do?
7. What command shows active trunks?

### Intermediate

8. What happens with desirable + auto?
9. What happens with auto + auto?
10. Why is static trunking usually easier to secure?
11. What is the difference between DTP and 802.1Q?
12. Why might a trunk be operational while VLAN 20 still fails?

### Advanced

13. Explain why user-facing ports should not normally rely on dynamic trunk negotiation.
14. Explain how DTP, 802.1Q, and STP work together on an inter-switch link.
15. Design a secure trunk between two switches carrying VLANs 10, 20, and 30.

### Troubleshooting

16. A port is configured as dynamic auto on both switches. No trunk appears. Explain.
17. `show interfaces trunk` shows the trunk, but VLAN 20 is missing. What do you check?
18. The trunk works, but users see unexpected native-VLAN behavior. What should you investigate?
19. One switch has `switchport nonegotiate`; the other is configured inconsistently. What should you verify?
20. A new device connected to an edge port appears to negotiate trunking. What security controls should you review?

---

# 37. Interview Questions 🎤

## Beginner

### Q1. What is DTP?

DTP is a Cisco proprietary protocol used by compatible switch interfaces to negotiate trunking.

### Q2. What is the difference between dynamic auto and dynamic desirable?

`dynamic auto` is passive; `dynamic desirable` actively attempts to negotiate trunking.

### Q3. What happens with auto + auto?

They do not actively request trunking, so they normally remain non-trunking.

### Q4. What command verifies a trunk?

```cisco
show interfaces trunk
```

---

## Intermediate

### Q5. What does `switchport nonegotiate` do?

It disables DTP negotiation frames on the interface. It is normally used with an intentionally configured static trunk/access design where negotiation is not required.

### Q6. Is DTP responsible for VLAN tagging?

No. DTP negotiates trunking. 802.1Q defines VLAN tagging on Ethernet trunks.

### Q7. Does DTP prevent Layer 2 loops?

No. STP performs loop prevention.

---

## Advanced

### Q8. Why is dynamic trunking considered a security concern?

Automatic trunk negotiation can allow unintended trunk formation on environments where an untrusted device can participate in negotiation. Explicitly configuring edge ports as access ports reduces this risk.

### Q9. How would you harden an inter-switch trunk?

A typical approach is to configure both ends explicitly as trunks, disable unnecessary negotiation where appropriate, restrict allowed VLANs, use an intentional native VLAN, and verify the final state.

### Q10. Is DTP available on every Cisco switch and interface?

No. Behavior and support vary by platform and software release. Verify the actual platform capabilities and configuration.

---

# 38. Quick Revision ⚡

```text
DTP = Dynamic Trunking Protocol
Cisco proprietary

Access      → fixed access behavior
Trunk       → fixed trunk behavior
Auto        → passive negotiation
Desirable   → active negotiation
Nonegotiate → disable DTP negotiation

Desirable + Auto      → Trunk
Desirable + Desirable → Trunk
Auto + Auto           → No dynamic trunk

DTP      → negotiates trunking
802.1Q   → VLAN tagging
STP      → prevents Layer 2 loops
```

---

# 39. Cheat Sheet 📋

## Configure access port

```cisco
interface gigabitEthernet 0/2
 switchport mode access
 switchport access vlan 10
```

## Configure static trunk

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
```

## Configure dynamic auto

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic auto
```

## Configure dynamic desirable

```cisco
interface gigabitEthernet 0/1
 switchport mode dynamic desirable
```

## Disable DTP on a static interface

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport nonegotiate
```

## Restrict allowed VLANs

```cisco
switchport trunk allowed vlan 10,20,30
```

## Set native VLAN

```cisco
switchport trunk native vlan 99
```

## Verify

```cisco
show interfaces trunk
show interfaces gigabitEthernet 0/1 switchport
show running-config interface gigabitEthernet 0/1
show vlan brief
show dtp interface gigabitEthernet 0/1
```

---

# 40. Common Beginner Mistakes ❌

- Thinking DTP and VTP are the same protocol.
- Thinking DTP performs VLAN tagging.
- Thinking DTP prevents Layer 2 loops.
- Assuming every Cisco platform supports every DTP command.
- Assuming `nonegotiate` creates a trunk automatically.
- Forgetting that auto + auto does not actively negotiate a trunk.
- Checking only whether the link is physically up.
- Forgetting to verify allowed VLANs.
- Forgetting native VLAN configuration.
- Leaving user-facing ports in dynamic negotiation modes.
- Changing configuration repeatedly without checking `show interfaces switchport`.

---

# 41. Industry Connection 🌐

In a professional network, the goal is not merely to make a trunk work. The goal is to make its behavior:

- predictable,
- documented,
- secure,
- easy to audit,
- easy to troubleshoot.

That is why you will often see designs where:

```text
End devices
    ↓
Explicit access ports
    ↓
Explicit VLAN assignment

Switch-to-switch links
    ↓
Explicit trunks
    ↓
Restricted VLAN list
    ↓
Intentional native VLAN
```

DTP is still important to understand because you may encounter it in existing Cisco networks and troubleshooting scenarios.

---

# 42. Connection to Previous Topics 🔗

This lesson builds directly on:

- **VLAN Fundamentals** → VLANs create separate Layer 2 broadcast domains.
- **Trunking Fundamentals** → trunks carry multiple VLANs between switches.
- **Ethernet Fundamentals** → Ethernet frames carry the traffic.
- **MAC Addressing** → switches forward frames using MAC addresses.
- **Switching Fundamentals** → switches learn and forward frames.
- **Collision and Broadcast Domains** → VLANs separate broadcast domains.

---

# 43. Connection to Next Topics 🔗

DTP should now be understood before moving deeper into Cisco VLAN-management concepts and **VTP**.

You should be comfortable with:

```text
VLAN
  ↓
Access Port
  ↓
Trunk
  ↓
802.1Q
  ↓
DTP
  ↓
VTP and VLAN management
```

---

# 44. Completion Checklist ✅

- [ ] I understand what DTP is.
- [ ] I know DTP is Cisco proprietary.
- [ ] I understand access mode.
- [ ] I understand trunk mode.
- [ ] I understand dynamic auto.
- [ ] I understand dynamic desirable.
- [ ] I understand the DTP negotiation matrix.
- [ ] I understand `switchport nonegotiate`.
- [ ] I can verify trunk status.
- [ ] I can inspect switchport operational mode.
- [ ] I can verify DTP where supported.
- [ ] I understand native VLANs.
- [ ] I understand allowed VLANs.
- [ ] I can troubleshoot a trunk systematically.
- [ ] I understand DTP vs 802.1Q vs STP.
- [ ] I understand why edge ports should normally be explicitly configured as access ports.
- [ ] I completed the DTP labs.
- [ ] I can explain DTP in an interview.

---

## 🏁 Final Takeaway

> **DTP negotiates whether compatible Cisco switch ports should form a trunk.**

Remember the core relationship:

```text
DTP       → trunk negotiation
802.1Q    → VLAN tagging
STP       → loop prevention
VTP       → VLAN information management
```

And the most important operational principle:

> **Understand dynamic negotiation, but prefer intentional, explicit configuration when designing secure and predictable networks.**

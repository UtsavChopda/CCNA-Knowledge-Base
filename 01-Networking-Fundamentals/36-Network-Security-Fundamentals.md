# 🛡️ 36 — Network Security Fundamentals

> **Welcome to the Network Security Control Room.** 🔐🌐🚨
>
> A network can be perfectly connected and still be unsafe. This module turns basic connectivity into controlled, authenticated, and monitored connectivity.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- 🧠 Explain the goals of network security
- 🔐 Distinguish authentication, authorization, and accounting
- 🧱 Explain Layer 2 attack surfaces
- 🔒 Configure basic switch port security
- 🚫 Understand MAC-address violations
- 🕵️ Explain DHCP Snooping
- 🛡️ Explain Dynamic ARP Inspection (DAI)
- 🧭 Explain IP Source Guard
- 🔑 Configure secure remote management with SSH
- 👤 Understand AAA concepts
- 🔎 Verify security features on Cisco IOS
- 🐛 Troubleshoot common security misconfigurations
- 🧪 Build practical Packet Tracer security labs
- 🏢 Apply controls to an enterprise access layer
- 🎤 Answer CCNA interview questions

---

# 🧭 1. Where We Are

```text
31 📨 DHCP
      ↓
32 🌐 DNS
      ↓
33 🧱 ACL
      ↓
34 🔄 NAT
      ↓
35 📡 Wireless
      ↓
36 🛡️ Network Security  ← YOU ARE HERE
```

The earlier modules focused on **communication**. This module asks a different question:

> **Who is allowed to communicate, from where, and under what conditions?**

---

# 🧠 2. What Does Network Security Mean?

Network security is the collection of technologies, configurations, policies, and processes used to protect network devices, traffic, identities, and resources.

A useful mental model is:

```text
             🛡️ NETWORK SECURITY
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    🔐 Identity   🚧 Access    👁️ Visibility
       │            │            │
      AAA       Port Security   Logs
      SSH       DHCP Snooping   Monitoring
                DAI
                IP Source Guard
```

Security is therefore not one command or one appliance.

---

# 🎯 3. CIA Triad

Three classic security objectives are:

### 🔒 Confidentiality
Only authorized parties should access information.

### 🧱 Integrity
Information should not be modified improperly.

### 🟢 Availability
Authorized users should be able to access services when required.

```text
        🔐 Confidentiality
              /\
             /  \
            /    \
           /      \
          /________\
   Integrity       Availability
```

### 🌍 Example

A company file server should:

- prevent unauthorized reading → **Confidentiality**
- prevent unauthorized modification → **Integrity**
- remain reachable for employees → **Availability**

---

# 👤 4. Authentication vs Authorization vs Accounting

These three terms are extremely important.

```text
WHO ARE YOU?
     ↓
Authentication 🔑
     ↓
WHAT CAN YOU DO?
     ↓
Authorization 🎫
     ↓
WHAT DID YOU DO?
     ↓
Accounting 🧾
```

### Authentication
Verifies identity.

Examples:

- Username/password
- Certificate
- MFA
- TACACS+/RADIUS-backed identity

### Authorization
Determines permitted actions.

Example:

```text
Junior admin → show commands + limited configuration
Senior admin → broader configuration privileges
```

### Accounting
Records activity for auditing and accountability.

Memory trick:

> **A-A-A = Authenticate → Authorize → Account**

---

# 🚪 5. Why the Access Layer Matters

Imagine an office switch with 48 access ports.

```text
                🔀 ACCESS SWITCH
       ┌────┬────┬────┬────┬────┐
       │    │    │    │    │    │
      💻   💻   🖨️   📷   💻   ❓
```

If an attacker connects an unauthorized laptop to an unused wall jack, the switch may otherwise treat it as another endpoint.

This is why Layer 2 security controls matter.

---

# 🔒 6. Port Security — Core Idea

**Port security** restricts which MAC addresses can use a switchport.

Without a control:

```text
Wall jack → any connected device → switch
```

With port security:

```text
Wall jack
   ↓
🔐 Port Security
   ↓
Allowed MAC? ── YES → Forward
       │
       └── NO → Violation action
```

It is especially useful on user-facing access ports.

---

# 🧩 7. Port Security Concepts

Important terms:

| Term | Meaning |
|---|---|
| Secure MAC | MAC address permitted on the port |
| Maximum | Maximum number of secure MAC addresses |
| Sticky MAC | IOS can learn MAC addresses and place them into configuration |
| Violation | What happens when an unauthorized MAC is detected |
| Shutdown | Err-disable the port on violation |
| Restrict | Drop violating traffic and record/count the violation |
| Protect | Drop violating traffic without the same level of notification |

⚠️ Exact behavior can vary by platform/software release; verify on the IOS image used in your lab.

---

# ⚙️ 8. Basic Port Security Configuration

Example: `GigabitEthernet0/1` is an access port.

```cisco
Switch(config)# interface gigabitEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security violation shutdown
```

The logic is:

```text
Access port
    ↓
Enable port security
    ↓
Allow 1 MAC
    ↓
Learn sticky MAC
    ↓
Unexpected MAC → violation
```

---

# 🔎 9. Verify Port Security

Useful commands:

```cisco
show port-security
show port-security interface gigabitEthernet0/1
show port-security address
show interfaces status
show interfaces gigabitEthernet0/1 status
```

Look for:

```text
Port status
Maximum MAC addresses
Current secure addresses
Security violation count
Violation mode
```

---

# 🚨 10. What Is an Err-Disabled Port?

A security violation can place a port into an **err-disabled** state depending on configuration.

Think:

```text
Unauthorized device 🚫
       ↓
Port-security violation
       ↓
🚨 Err-disabled
       ↓
Traffic stops
```

Verification:

```cisco
show interfaces status
show interfaces status err-disabled
```

Recovery depends on the cause and configured recovery policy. Do not simply re-enable a port before understanding why it was disabled.

---

# 🕵️ 11. DHCP Snooping

DHCP is trusted only when DHCP responses come from legitimate DHCP infrastructure.

An attacker could connect a rogue DHCP server:

```text
              🔀 Switch
              /      \
             /        \
       💻 Client    😈 Rogue DHCP
                         │
                         └── Fake gateway/DNS
```

DHCP Snooping helps distinguish **trusted** and **untrusted** switchports for DHCP messages.

---

# 🟢 12. Trusted vs Untrusted Ports

Typical design:

```text
                 🔵 Legit DHCP Server
                         │
                    [ TRUSTED ]
                         │
                    🔀 Switch
                   /      |      \
                  /       |       \
          [UNTRUSTED] [UNTRUSTED] [UNTRUSTED]
              💻          💻          💻
```

The server/uplink-facing path is trusted.

User-facing access ports are generally untrusted.

```cisco
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
Switch(config)# interface gigabitEthernet0/24
Switch(config-if)# ip dhcp snooping trust
```

⚠️ Do not blindly trust every port. Trust only the path where legitimate DHCP server responses should originate.

---

# 📋 13. DHCP Snooping Binding Table

One valuable result of DHCP Snooping is a binding database containing information such as:

```text
MAC address
IP address
VLAN
Interface
Lease information
```

This information can support other security mechanisms.

Verify with:

```cisco
show ip dhcp snooping
show ip dhcp snooping binding
```

---

# 🛡️ 14. Dynamic ARP Inspection (DAI)

ARP has an important weakness: hosts can receive forged ARP information.

Example attack concept:

```text
Victim 💻
   │
   │ “Who has gateway IP?”
   ▼
😈 Attacker
   │
   └── forged ARP information
```

This can support man-in-the-middle attacks.

DAI validates ARP packets on supported Cisco platforms.

---

# 🔗 15. DAI and DHCP Snooping Work Together

A common design is:

```text
        DHCP Snooping
             ↓
      Binding Database
             ↓
            DAI
             ↓
       Validate ARP
```

Conceptually:

```text
DHCP Snooping = “Who received which IP?”
DAI           = “Does this ARP claim make sense?”
```

Basic configuration example:

```cisco
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
Switch(config)# ip arp inspection vlan 10
```

For a trusted uplink:

```cisco
Switch(config)# interface gigabitEthernet0/24
Switch(config-if)# ip dhcp snooping trust
Switch(config-if)# ip arp inspection trust
```

---

# 🧭 16. IP Source Guard

IP Source Guard helps prevent a host from using an unauthorized source IP address on an access port.

Conceptually:

```text
💻 Host
   │
   │ Source IP = 192.168.10.50
   ▼
🔐 IP Source Guard
   │
   ├── Valid binding → allow
   └── Invalid source → drop
```

It commonly uses DHCP Snooping information as part of the validation mechanism.

Example:

```cisco
Switch(config)# interface gigabitEthernet0/1
Switch(config-if)# ip verify source
```

---

# 🧠 17. The Layer 2 Security Chain

Remember this relationship:

```text
             🛡️ ACCESS-LAYER SECURITY
                       │
       ┌───────────────┼───────────────┐
       ▼               ▼               ▼
 🔒 Port Security  🕵️ DHCP Snooping  🛡️ DAI
       │               │               │
   MAC control     DHCP trust      ARP validation
                       │
                       ▼
                🧭 IP Source Guard
                  source validation
```

These controls solve different problems; they are complementary rather than interchangeable.

---

# 🔑 18. Secure Remote Management — SSH

Never treat Telnet as the preferred secure management protocol.

Telnet sends management traffic without the cryptographic protection expected from SSH.

SSH provides encrypted remote management.

```text
Administrator 💻
       │
       │ 🔐 SSH
       ▼
   🔀 Switch / Router
```

---

# ⚙️ 19. Basic Cisco SSH Configuration

Example:

```cisco
Router(config)# hostname R1
Router(config)# ip domain-name lab.local
Router(config)# username admin privilege 15 secret StrongPassword123
Router(config)# crypto key generate rsa modulus 2048
Router(config)# ip ssh version 2
Router(config)# line vty 0 4
Router(config-line)# login local
Router(config-line)# transport input ssh
Router(config-line)# end
```

Verify:

```cisco
show ip ssh
show running-config | section line vty
```

From another device:

```text
ssh -l admin <router-ip>
```

Use a strong secret in real environments; the example above is for lab syntax only.

---

# 👥 20. AAA Architecture

For larger environments, local usernames on every device do not scale well.

AAA can centralize authentication and authorization using protocols such as:

- **RADIUS**
- **TACACS+**

Conceptual design:

```text
                 🧑‍💻 Admin
                    │
                    │ Login
                    ▼
              🔀 Network Device
                    │
                    │ AAA
                    ▼
             🖥️ AAA Server
```

Benefits include:

✅ Centralized identity
✅ Consistent policy
✅ Better auditing
✅ Easier account lifecycle management

---

# ⚖️ 21. RADIUS vs TACACS+

At CCNA level, remember the broad distinction:

| Feature | RADIUS | TACACS+ |
|---|---|---|
| Common use | Network access authentication | Device administration |
| Transport | UDP | TCP |
| AAA handling | Authentication + authorization information commonly combined | Separates authentication, authorization, accounting |
| Cisco device administration | Common | Particularly common |

Do not reduce the protocols to “one is secure and the other is not.” Both are designed for AAA use; deployment depends on architecture and requirements.

---

# 🧪 22. Practical Lab — Port Security

## 🎯 Objective

Allow one known endpoint on an access port and observe a security violation.

### Topology

```text
💻 PC1
  │
  │ G0/1
  ▼
🔀 Switch
```

### Configuration

```cisco
enable
configure terminal
interface gigabitEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
end
```

Connect PC1 and generate traffic.

Verify:

```cisco
show port-security interface gigabitEthernet0/1
show port-security address
```

Then replace PC1 with another endpoint and observe what happens.

### Learning goal

Do not just memorize the commands. Observe the relationship:

```text
MAC learned
   ↓
MAC becomes secure
   ↓
Different MAC appears
   ↓
Violation
```

---

# 🧪 23. Practical Lab — DHCP Snooping + DAI

## 🎯 Objective

Protect VLAN 10 from rogue DHCP and forged ARP traffic.

### Design

```text
                 🖥️ Legit DHCP Server
                         │
                       G0/24
                         │
                    🔀 Switch
                   /      \
                G0/1      G0/2
                 │          │
                💻 PC1     💻 PC2
```

### Configuration

```cisco
ip dhcp snooping
ip dhcp snooping vlan 10
ip arp inspection vlan 10

interface gigabitEthernet0/24
 ip dhcp snooping trust
 ip arp inspection trust
```

Access ports remain untrusted by default unless your platform/configuration dictates otherwise.

Verify:

```cisco
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection statistics
```

---

# 🚨 24. Troubleshooting Decision Tree

When security breaks connectivity, avoid randomly removing commands.

```text
Traffic failing?
      ↓
Is interface up?
      ↓
Correct VLAN?
      ↓
Port-security violation?
      ↓
DHCP Snooping blocking DHCP?
      ↓
Is legitimate server path trusted?
      ↓
Does binding table contain the client?
      ↓
Is DAI dropping ARP?
      ↓
Is IP Source Guard rejecting source IP?
      ↓
Re-test
```

---

# 🐛 25. Common Failure Scenarios

### ❌ Problem 1 — Port unexpectedly err-disabled

Check:

```cisco
show interfaces status
show port-security interface gigabitEthernet0/1
```

Possible cause:

- Unauthorized MAC
- Maximum MAC limit exceeded
- Security violation

---

### ❌ Problem 2 — Clients stop receiving DHCP

Check:

```cisco
show ip dhcp snooping
show ip dhcp snooping binding
```

Likely design error:

```text
DHCP server path accidentally untrusted
```

---

### ❌ Problem 3 — ARP connectivity fails after DAI is enabled

Check:

```cisco
show ip arp inspection statistics
show ip dhcp snooping binding
```

Possible causes:

- Missing DHCP Snooping binding
- Incorrect trust configuration
- Static addressing requiring an appropriate DAI design
- Incorrect VLAN configuration

---

### ❌ Problem 4 — SSH does not work

Check:

```cisco
show ip ssh
show ip interface brief
show running-config | section username
show running-config | section line vty
```

Also verify:

```text
Hostname
Domain name
RSA keys
VTY configuration
Local credentials / AAA
IP reachability
SSH version
```

---

# 🏢 26. Enterprise Scenario

A company has:

```text
🏢 3 floors
💻 300 employees
📱 100 phones
🖨️ 20 printers
```

Access switches connect employee devices.

Security requirements:

```text
1️⃣ Prevent unauthorized devices on sensitive ports
2️⃣ Prevent rogue DHCP servers
3️⃣ Reduce ARP spoofing risk
4️⃣ Validate source addresses
5️⃣ Secure administrator access
6️⃣ Centralize administrator authentication
```

Possible architecture:

```text
Port Security
      ↓
DHCP Snooping
      ↓
DAI
      ↓
IP Source Guard
      ↓
SSH
      ↓
AAA
```

The important engineering principle is **layered defense**.

---

# 🎯 27. Scenario Challenge

You are the network engineer for a 100-user office.

A user reports:

> “My laptop stopped getting an IP address after the security upgrade.”

You discover:

```text
DHCP Snooping = enabled
DAI = enabled
Access port = untrusted
DHCP server = connected through G0/24
```

### Your task

Determine:

1. Which path should normally be trusted?
2. Which verification commands would you run?
3. What evidence would confirm DHCP Snooping is the problem?
4. How would you verify the fix?
5. Why should you avoid trusting all switchports?

### Expected reasoning

```text
Client
 ↓
Untrusted access port
 ↓
Switch
 ↓
Trusted legitimate DHCP path
 ↓
DHCP server
```

The goal is not merely to make DHCP work; it is to preserve the security boundary.

---

# 🧪 28. Practice Questions

### 🟢 Beginner

1. What is port security?
2. What is AAA?
3. What is SSH used for?
4. What is DHCP Snooping?
5. What is DAI?

### 🟡 Intermediate

6. Why would an access port normally be untrusted for DHCP Snooping?
7. How does DAI use DHCP Snooping information?
8. What is the purpose of sticky MAC learning?
9. Why can port security cause an interface to become err-disabled?
10. Why is centralized AAA useful?

### 🔴 Advanced

11. Why might DAI fail for a statically addressed host?
12. Why should you not blindly trust an uplink?
13. How would you troubleshoot a DHCP failure immediately after enabling DHCP Snooping?
14. How can Layer 2 controls complement ACLs?
15. Why is layered defense stronger than relying on a single feature?

---

# 🎤 29. Interview Questions

### Q1. What is the difference between authentication and authorization?

**Answer:** Authentication verifies identity; authorization determines permitted actions.

### Q2. What does port security protect against?

**Answer:** It can restrict MAC addresses permitted on a switchport and respond to unauthorized MAC activity according to the configured violation mode.

### Q3. What is DHCP Snooping?

**Answer:** A switch security feature that classifies DHCP ports as trusted/untrusted and helps block unauthorized DHCP server responses while maintaining DHCP bindings.

### Q4. What is DAI?

**Answer:** Dynamic ARP Inspection validates ARP packets against trusted information, commonly DHCP Snooping bindings, to help mitigate ARP spoofing.

### Q5. Why use SSH instead of Telnet?

**Answer:** SSH provides encrypted remote management; Telnet does not provide equivalent cryptographic protection.

### Q6. What are RADIUS and TACACS+?

**Answer:** AAA protocols used to centralize authentication, authorization, and accounting for network access or device administration.

---

# ⚡ 30. Quick Revision

```text
🔒 PORT SECURITY
→ Controls MAC addresses on switchports

🕵️ DHCP SNOOPING
→ Controls DHCP trust + builds bindings

🛡️ DAI
→ Validates ARP

🧭 IP SOURCE GUARD
→ Validates source addressing on access ports

🔐 SSH
→ Secure remote device management

👥 AAA
→ Authentication
→ Authorization
→ Accounting
```

---

# 🧠 31. One-Minute Memory Map

```text
        🛡️ NETWORK SECURITY
                 │
      ┌──────────┴──────────┐
      │                     │
   ACCESS                  ADMIN
      │                     │
      ├─ Port Security      ├─ SSH
      ├─ DHCP Snooping      └─ AAA
      ├─ DAI
      └─ IP Source Guard
```

### Security questions to ask

```text
👤 Who is connecting?
📍 From which port?
📨 Is DHCP legitimate?
📡 Is ARP legitimate?
🧭 Is the source IP legitimate?
🔐 Is management encrypted?
🧾 Can administrator activity be audited?
```

---

# 📋 32. Cisco Security Cheat Sheet

| Task | Command |
|---|---|
| Enable port security | `switchport port-security` |
| Set maximum MACs | `switchport port-security maximum 1` |
| Sticky MAC | `switchport port-security mac-address sticky` |
| Check port security | `show port-security` |
| Check interface security | `show port-security interface g0/1` |
| Enable DHCP Snooping | `ip dhcp snooping` |
| Enable on VLAN | `ip dhcp snooping vlan 10` |
| Trust DHCP path | `ip dhcp snooping trust` |
| Show DHCP Snooping | `show ip dhcp snooping` |
| Show bindings | `show ip dhcp snooping binding` |
| Enable DAI | `ip arp inspection vlan 10` |
| Trust DAI path | `ip arp inspection trust` |
| Show DAI | `show ip arp inspection` |
| Show DAI statistics | `show ip arp inspection statistics` |
| Enable IP Source Guard | `ip verify source` |
| Show SSH | `show ip ssh` |

---

# 🏁 33. Final Takeaway

A secure network is not simply a network where packets can move.

It is a network where packet movement is controlled by identity, trust, source information, policy, and secure management.

```text
CONNECTIVITY
     ↓
CONTROL
     ↓
VALIDATION
     ↓
AUTHENTICATION
     ↓
MONITORING
     ↓
🛡️ DEFENSE IN DEPTH
```

> **CCNA mindset:** Don't ask only “Can it ping?” Ask **“Why is it allowed to communicate?”** 🔥

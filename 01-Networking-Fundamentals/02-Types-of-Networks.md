# 🏷️ 02 — Types of Networks

> **Learn how networks are classified by size, geographic coverage, purpose, and technology.**

---

## 🎯 Learning Objectives

By the end of this lesson, you should be able to:

- Explain why networks are classified into different types.
- Understand PAN, LAN, WLAN, CAN, MAN and WAN.
- Understand SAN and data-center network concepts at a high level.
- Distinguish between LAN and WAN.
- Identify real-world examples of each network type.
- Understand the relationship between network size and network technologies.
- Identify which network types commonly appear in enterprise environments.
- Answer common interview questions about network types.

---

# 1. 🤔 Why Are Networks Classified?

Not every network is the same.

A network connecting your phone to your smartwatch is very different from a network connecting offices across different countries.

Networks can therefore be classified based on factors such as:

- 📏 Geographic coverage
- 👥 Number of connected devices
- 🏢 Organizational scope
- 🎯 Purpose
- ⚙️ Technology used

A classification helps network engineers describe the size and purpose of a network quickly.

---

# 2. 🌐 Network Types at a Glance

| Type | Full Form | Typical Scope | Example |
|---|---|---|---|
| PAN | Personal Area Network | Very small / personal | Phone + smartwatch |
| LAN | Local Area Network | Room, home, office/building | Office Ethernet network |
| WLAN | Wireless LAN | Wireless local area | Office Wi-Fi |
| CAN | Campus Area Network | Campus / multiple buildings | University network |
| MAN | Metropolitan Area Network | City / metropolitan area | City-wide organizational network |
| WAN | Wide Area Network | Large geographic area | Global company network |
| SAN | Storage Area Network | Storage infrastructure | Data-center storage network |

> 📌 These categories can overlap in real-world network designs. They are useful conceptual classifications rather than completely isolated technologies.

---

# 3. 📱 PAN — Personal Area Network

**PAN** stands for **Personal Area Network**.

It connects devices around an individual, usually over a very short distance.

### Example

```text
          📱 Smartphone
           /        \
          /          \
         ▼            ▼
   ⌚ Smartwatch    🎧 Earbuds
```

Common technologies can include:

- Bluetooth
- USB
- Other short-range personal connectivity technologies

### 🌍 Real-world example

You connect wireless earbuds to your smartphone using Bluetooth.

That is a simple example of a personal-area network.

### Key point

> **PAN = very small personal network.**

---

# 4. 🏠 LAN — Local Area Network

**LAN** stands for **Local Area Network**.

A LAN connects devices within a relatively limited area such as:

- Home
- Office
- Classroom
- Computer lab
- Single building

Example:

```text
                 🔀 Switch
              /      |      \
             /       |       \
            ▼        ▼        ▼
          💻 PC    🖨️ Printer  🖥️ Server
```

A wired LAN commonly uses Ethernet.

### Characteristics

- Limited geographic area
- Usually under one organization's or user's control
- High-speed local communication is common
- Can contain switches, routers, access points, servers and end devices

### Key point

> **LAN = local network.**

---

# 5. 📡 WLAN — Wireless Local Area Network

**WLAN** stands for **Wireless Local Area Network**.

It provides LAN connectivity using wireless communication, commonly Wi-Fi.

Example:

```text
              📡 Access Point
              /     |      \
             /      |       \
            ▼       ▼        ▼
          💻       📱       💻
```

A WLAN is essentially a wireless form of local networking.

### Example

A college classroom has a Wi-Fi access point and students connect their laptops and phones to it.

### Key point

> **WLAN = wireless LAN.**

---

# 6. 🏫 CAN — Campus Area Network

**CAN** stands for **Campus Area Network**.

It connects multiple LANs across a campus or group of nearby buildings, usually under a common organization.

Example:

```text
                 🏢 Core Network
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
      🏫 Building A 🏫 Building B 🏫 Building C
          │            │            │
          ▼            ▼            ▼
       🔀 LAN         🔀 LAN        🔀 LAN
```

### 🌍 Example

A university may have separate LANs in:

- Engineering building
- Library
- Administration building
- Hostel
- Computer laboratories

The campus network interconnects them.

### Key point

> **CAN = multiple local networks connected across a campus.**

---

# 7. 🏙️ MAN — Metropolitan Area Network

**MAN** stands for **Metropolitan Area Network**.

It generally describes a network covering a larger area than a typical LAN/CAN, often within a metropolitan or city-scale region.

Example concept:

```text
🏢 Office A
     │
     ├──────────────┐
     │              │
     ▼              ▼
🏢 Office B      🏢 Office C
     \              /
      \            /
       ▼          ▼
       🌆 Metropolitan Network
```

MAN is less commonly used as a precise modern engineering label than LAN/WAN, but it remains an important networking classification and interview concept.

### Key point

> **MAN = metropolitan/city-scale networking.**

---

# 8. 🌎 WAN — Wide Area Network

**WAN** stands for **Wide Area Network**.

A WAN connects networks across large geographic distances.

For example:

```text
🏢 Pune Office
      │
      ▼
   🌐 WAN
      │
      ├──────────────► 🏢 Bangalore Office
      │
      ├──────────────► 🏢 Delhi Office
      │
      └──────────────► 🏢 London Office
```

WAN connectivity may use services and technologies provided by telecommunications providers.

### Common WAN concepts

- Leased connectivity
- MPLS
- Internet-based VPNs
- SD-WAN
- Broadband
- Cellular connectivity

### Key point

> **WAN = connects networks over large geographic areas.**

---

# 9. 🗄️ SAN — Storage Area Network

**SAN** stands for **Storage Area Network**.

A SAN is a specialized high-speed network designed to provide servers with access to storage resources.

Simplified example:

```text
💻 Server 1 ───┐
               │
💻 Server 2 ───┼──► 🗄️ Storage Infrastructure
               │
💻 Server 3 ───┘
```

SANs are commonly associated with enterprise and data-center environments.

Technologies may include Fibre Channel and other storage networking approaches.

### Key point

> **SAN = specialized network for storage connectivity.**

---

# 10. 🏢 Enterprise Network Example

A large organization may use several network types together.

```text
                         🌐 Internet
                              │
                              ▼
                         🛣️ WAN Edge
                              │
                              ▼
                       🏢 Headquarters
                         │         │
                         ▼         ▼
                      🔀 LAN     📡 WLAN
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
          💻 Users    🖨️ Printers  🗄️ Servers

             ╔══════════════════════════╗
             ║      WAN Connectivity    ║
             ╚══════════════════════════╝
                    │          │
                    ▼          ▼
               🏢 Pune      🏢 Bangalore
```

This demonstrates an important idea:

> **Network classifications can coexist in the same enterprise.**

---

# 11. 🔍 LAN vs WAN

This is a very common CCNA and interview question.

| Feature | LAN | WAN |
|---|---|---|
| Geographic scope | Small/local | Large/geographically distributed |
| Typical example | Office floor | Inter-city company network |
| Common technologies | Ethernet, Wi-Fi | MPLS, VPN, SD-WAN, provider links |
| Ownership | Often organization/user | May involve service providers |
| Main purpose | Local connectivity | Connect geographically separated networks |

### Simple way to remember

```text
LAN → Local
WAN → Wide
```

---

# 12. 📡 LAN vs WLAN

| Feature | LAN | WLAN |
|---|---|---|
| Meaning | Local Area Network | Wireless Local Area Network |
| Medium | Often Ethernet/copper/fiber | Wireless radio |
| Example | Wired office PCs | Office Wi-Fi |
| Mobility | Limited by cable | Greater mobility |
| Common access device | Switch | Wireless Access Point |

A WLAN is still a local network. The important distinction is that the access is wireless.

---

# 13. 🧠 Network Size Hierarchy

A simplified way to visualize geographic scope is:

```text
📱 PAN
   ↓
🏠 LAN / 📡 WLAN
   ↓
🏫 CAN
   ↓
🏙️ MAN
   ↓
🌎 WAN
```

Remember that this is a conceptual progression, not a strict rule that every network must follow.

---

# 14. 🌍 Real-World Examples

## 🏠 Home

```text
📱 Phone
💻 Laptop
📺 Smart TV
⌚ Watch
      │
      ▼
📡 Home Wi-Fi / Router
      │
      ▼
🌐 Internet
```

Likely classifications:

- PAN for personal device connections
- WLAN/LAN for the home network
- WAN for the Internet connection beyond the local network

## 🏫 University

```text
🏫 Building A ──┐
🏫 Building B ──┼──► 🏢 Campus Network
🏫 Building C ──┘
```

Likely classification:

- LAN/WLAN inside buildings
- CAN across the campus
- WAN for external connectivity

## 🏢 Multinational Company

```text
🇮🇳 India Offices ──┐
🇬🇧 UK Offices    ──┼──► 🌎 Enterprise WAN
🇺🇸 US Offices    ──┘
```

Likely classification:

- LAN/WLAN at individual sites
- WAN between sites

---

# 💻 15. Commands and Verification

Network classification itself does not require a special Cisco configuration command.

### Cisco IOS commands useful for identifying the network environment

```cisco
show ip interface brief
show interfaces
show cdp neighbors
show lldp neighbors
show running-config
```

### What they help you understand

| Command | Useful Information |
|---|---|
| `show ip interface brief` | Interface/IP status |
| `show interfaces` | Interface details and counters |
| `show cdp neighbors` | Cisco neighbor information |
| `show lldp neighbors` | Neighbor discovery information |
| `show running-config` | Current device configuration |

> 📌 The commands above do not tell you "this is a WAN" or "this is a LAN" directly. They provide information that helps you understand the device's role and connectivity.

---

# 🧪 16. Practical Exercise

## 🎯 Objective

Identify the network types present in a real environment.

### Step 1

Look at your home network.

Identify:

- Phones
- Laptops
- Smart devices
- Wi-Fi router/access point
- Wired devices
- Internet connection

### Step 2

Classify them.

Example:

```text
Phone ↔ Earbuds
→ PAN

Laptop ↔ Wi-Fi Router
→ WLAN

Home devices ↔ Local network
→ LAN/WLAN

Home network ↔ ISP/Internet
→ WAN
```

### Step 3

Write your own network diagram.

```text
[Your Device]
      │
      ▼
[Your Local Network]
      │
      ▼
[Router]
      │
      ▼
[ISP]
      │
      ▼
[Internet]
```

---

# 🚨 17. Troubleshooting Perspective

When troubleshooting, first determine **what kind of network path is failing**.

For example:

```text
💻 Laptop
   ↓
📡 Wi-Fi
   ↓
🏠 Local Network
   ↓
🛣️ Router
   ↓
🌐 WAN/Internet
```

If the laptop cannot communicate with the router, the problem may be local.

If the laptop can reach the router but cannot reach an external destination, investigate the next part of the path.

### Structured approach

```text
❌ Destination unreachable
        ↓
📡 Check local connection
        ↓
📍 Check IP configuration
        ↓
🛣️ Test default gateway
        ↓
🌐 Test external destination
        ↓
🔎 Determine where the failure begins
```

---

# ⚠️ 18. Common Beginner Mistakes

### ❌ Thinking LAN always means wired

LAN describes a local network. A WLAN is a wireless LAN.

### ❌ Thinking WAN means only the Internet

The Internet is a global interconnection of networks. A private enterprise WAN can connect company sites without simply being "the Internet."

### ❌ Assuming network types are completely separate

A single enterprise can contain LANs, WLANs, CAN-style campus networks, WAN connections and specialized networks such as SANs.

### ❌ Memorizing abbreviations without understanding scope

Remember the basic idea:

```text
PAN → Personal
LAN → Local
CAN → Campus
MAN → Metropolitan
WAN → Wide
SAN → Storage
```

---

# 🎯 19. Scenario-Based Challenge

## 🏦 Bank Network

A bank has:

- 20 computers in one branch
- 5 wireless access points
- 2 servers
- Network printers
- 50 branches across India
- A central data center

### Your task

Identify which network classifications could describe:

1. Computers inside one branch
2. Wireless users inside the branch
3. Connectivity between nearby buildings in a bank campus
4. Connectivity between branches in different cities
5. Specialized storage connectivity in the data center

### Think before checking

```text
Branch
  ↓
Local Connectivity
  ↓
Campus / Regional Connectivity
  ↓
Wide-Area Connectivity
  ↓
Data-Center Storage
```

There can be more than one valid description depending on the exact architecture.

---

# 🧪 20. Practice Questions

### Question 1
What type of network connects a smartwatch to a smartphone over Bluetooth?

### Question 2
What type of network commonly connects computers within an office building?

### Question 3
What is a WLAN?

### Question 4
What type of network can connect multiple buildings belonging to one university campus?

### Question 5
What type of network connects geographically separated company offices?

### Question 6
What does SAN specialize in?

### Question 7
Can a company use LAN, WLAN and WAN at the same time?

### Question 8
What is the main difference between LAN and WAN?

---

# 🎤 21. Interview Questions

## 🟢 Beginner

### 1. What is LAN?

LAN stands for Local Area Network and generally refers to a network covering a limited geographic area such as a home, office or building.

### 2. What is WAN?

WAN stands for Wide Area Network and connects networks over large geographic distances.

### 3. What is WLAN?

WLAN stands for Wireless Local Area Network and provides local network connectivity using wireless technology.

### 4. What is PAN?

PAN stands for Personal Area Network and is used for short-range connectivity between personal devices.

---

## 🟡 Intermediate

### 5. What is the difference between LAN and WAN?

LAN covers a relatively small local area, while WAN connects networks over larger geographic distances.

### 6. What is a campus network?

A campus network connects multiple local networks across a campus or group of nearby buildings, commonly under one organization.

### 7. Can WLAN be considered a LAN?

Yes. WLAN is a wireless local area network and is a form of local networking.

### 8. Is the Internet a WAN?

The Internet is a global network of interconnected networks. It is not simply one organization's WAN, although Internet connectivity is a common WAN transport/service for organizations.

---

## 🔴 Scenario / Troubleshooting

### 9. A user can connect to office Wi-Fi but cannot access a server in another city. Where would you investigate?

Start by confirming local WLAN/LAN connectivity, IP configuration and default gateway, then investigate routing and the WAN path between sites.

### 10. A company has offices in Pune, Bangalore and Delhi. What network concept connects these locations?

A WAN or enterprise WAN architecture can connect geographically separated offices.

---

# ⚡ 22. Quick Revision

```text
📱 PAN
→ Personal, very short range.

🏠 LAN
→ Local network within a limited area.

📡 WLAN
→ Wireless local area network.

🏫 CAN
→ Connects networks across a campus.

🏙️ MAN
→ Metropolitan/city-scale network classification.

🌎 WAN
→ Connects networks across large geographic areas.

🗄️ SAN
→ Specialized network for storage connectivity.
```

---

# 📋 23. Cheat Sheet

| Network | Remember |
|---|---|
| PAN | Personal devices / short range |
| LAN | Local area |
| WLAN | Wireless LAN |
| CAN | Campus |
| MAN | Metropolitan |
| WAN | Wide geographic area |
| SAN | Storage |

### Memory Trick

```text
P → Personal
L → Local
C → Campus
M → Metropolitan
W → Wide
S → Storage
```

---

# 🔗 24. Connection to the Next Topic

Now you know how networks can be classified by scope and purpose.

Next, we will learn **network topologies** — how network devices and links can be physically or logically arranged.

```text
🏷️ Types of Networks
          ↓
🗺️ Network Topologies
          ↓
🔌 Network Devices
          ↓
🔗 Cables & Connectors
```

---

# ✅ Lesson Completion Checklist

- [ ] I understand PAN.
- [ ] I understand LAN.
- [ ] I understand WLAN.
- [ ] I understand CAN.
- [ ] I understand MAN.
- [ ] I understand WAN.
- [ ] I understand SAN at a high level.
- [ ] I can explain LAN vs WAN.
- [ ] I can identify network types in real-world environments.
- [ ] I understand that multiple network types can coexist.
- [ ] I completed the practical exercise.
- [ ] I attempted the scenario challenge.
- [ ] I reviewed the interview questions.

---

## 🏁 Key Takeaway

> **Network types describe networks by their scope, purpose and environment. In real enterprise networks, multiple types can work together to provide complete connectivity.**

---

## 🔗 Navigation

⬅️ [Previous — What Is Computer Networking?](01-What-is-Networking.md)

🏠 [Networking Fundamentals Overview](README.md)

➡️ [Next — Network Topologies](03-Network-Topologies.md)

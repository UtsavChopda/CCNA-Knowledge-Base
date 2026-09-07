# 🚨🛡️ Module 49 — Enterprise Network Security Operations & Defense

> **From designing security controls → operating them every day → detecting attacks → containing incidents.**
>
> Welcome to the operations room of NH Technologies. 🖥️🌐🔎

---

## 🧭 Where We Are

Modules 36 and 48 taught us how to secure the network and design security architecture.

Now we answer the operational question:

> **"The controls are deployed. Something suspicious is happening. What do I do next?"**

The journey becomes:

```text
🏗️ Architecture
      ↓
🛡️ Controls
      ↓
📊 Telemetry
      ↓
🚨 Detection
      ↓
🔎 Investigation
      ↓
🧠 Threat Hunting
      ↓
🚧 Containment
      ↓
🔧 Eradication / Recovery
      ↓
📋 Lessons Learned
      ↓
🏗️ Improve Architecture
```

---

# 🎯 1. Module Mission

By the end of this module, you should be able to:

- Explain what a network security operation does
- Understand SOC/NOC collaboration
- Build a security monitoring pipeline
- Distinguish event, alert, incident, and case
- Understand detection engineering
- Investigate network indicators
- Use logs, flows, DNS, DHCP, authentication and packet evidence together
- Recognize reconnaissance and lateral movement patterns
- Understand command-and-control traffic at a high level
- Triage suspicious network behavior
- Build an incident timeline
- Contain a compromised host safely
- Preserve evidence
- Understand false positives and alert fatigue
- Build useful security dashboards
- Map network evidence to attacker behavior
- Design practical detection use cases
- Perform network-focused threat hunting

---

# 🧠 2. NOC vs SOC

These teams often work together but have different primary missions.

### 🌐 NOC — Network Operations Center

Primary question:

> **"Is the network available and performing correctly?"**

Typical concerns:

- Link failures
- Routing failures
- High latency
- Packet loss
- Device health
- Capacity
- Configuration problems

### 🛡️ SOC — Security Operations Center

Primary question:

> **"Is something malicious or suspicious happening?"**

Typical concerns:

- Malware
- Credential abuse
- Reconnaissance
- Lateral movement
- Command and control
- Data exfiltration
- Unauthorized access

### 🤝 Real enterprise workflow

```text
          NOC 🌐
            |
            | Network evidence
            ↓
          SOC 🛡️
            |
            | Security analysis
            ↓
       Incident Response 🚨
            |
            ↓
       Network + Endpoint
          Remediation
```

A network engineer and SOC analyst should not operate in separate worlds.

---

# 📦 3. Event → Alert → Incident → Case

These terms are easy to mix up.

### 📌 Event

Something happened.

```text
User logged in.
DNS query occurred.
TCP connection was created.
```

### 🚨 Alert

A system believes an event or collection of events deserves attention.

```text
Possible port scan detected.
```

### 🔥 Incident

An event or group of events is determined to represent a security issue requiring response.

### 📁 Case

The operational record containing investigation details, evidence, decisions and actions.

Mental model:

```text
Millions of Events
       ↓
Interesting Events
       ↓
Alerts
       ↓
Validated Security Incident
       ↓
Case / Investigation
```

---

# 📊 4. The Security Telemetry Pipeline

Security operations depend on visibility.

```text
Switches ───────┐
Routers ────────┤
Firewalls ──────┤
VPN ────────────┤
DNS ────────────┤
DHCP ───────────┤
IDS/IPS ────────┤
Endpoints ──────┤
Authentication ─┤
Cloud ──────────┘
        ↓
   Collectors
        ↓
   SIEM / Data Lake
        ↓
Correlation + Analytics
        ↓
       Alerts 🚨
```

### Key lesson

> **You cannot reliably investigate what you cannot observe.**

---

# 🧾 5. Syslog as Security Evidence

From Module 37, remember Cisco syslog severity levels.

Security operations use logs to reconstruct what happened.

Example:

```text
10:01:22 Interface Gi0/1 changed state
10:02:15 SSH login failure
10:02:17 SSH login failure
10:02:19 SSH login failure
10:03:01 Successful login
```

The sequence matters more than one line.

### Investigation question

> What happened immediately before and after the successful login?

---

# ⏰ 6. Time Synchronization Is Security Infrastructure

Imagine these timestamps:

```text
Firewall: 10:01:03
Router:   09:58:42
Server:   10:04:11
Endpoint: 10:00:17
```

Correlation becomes painful. 😵

With NTP:

```text
           NTP
            |
     ┌──────┼──────┐
     ↓      ↓      ↓
 Firewall Router Endpoint
```

Now investigators can build a much more reliable timeline.

> **NTP is not merely a convenience. Accurate time improves incident reconstruction.**

---

# 🔎 7. Detection Engineering

A security detection should answer:

> **What suspicious behavior are we trying to identify?**

Weak detection:

```text
"Alert on lots of traffic."
```

Better detection:

```text
A workstation contacts an unusually large number
of internal hosts over a short period using multiple
administrative/service ports.
```

A useful detection should define:

```text
Behavior
   ↓
Data Source
   ↓
Condition
   ↓
Threshold / Context
   ↓
Alert
   ↓
Investigation Path
```

---

# 🎯 8. Indicators vs Behaviors

### Indicator

A known observable value.

Examples:

- IP address
- Domain
- Hash
- URL
- MAC address

### Behavior

What a system is doing.

Examples:

- Scanning many hosts
- Repeated authentication failures
- Unusual DNS volume
- Sudden outbound data transfer
- New administrative connections

### 🧠 Blue Team lesson

Indicators can change quickly.

Behavior-based detection can remain useful even when attackers change infrastructure.

---

# 🛰️ 9. Network Security Data Sources

| Source | Useful Evidence |
|---|---|
| Firewall | Allowed/blocked connections |
| Router | Routing/interface behavior |
| Switch | MAC/VLAN/port information |
| DNS | Domain lookups |
| DHCP | IP-to-device assignments |
| VPN | Remote access/authentication |
| Proxy | Web activity |
| IDS/IPS | Suspicious signatures/behavior |
| NetFlow/IPFIX | Communication patterns |
| Endpoint | Process/user/device evidence |
| AAA/RADIUS | Authentication events |
| Syslog | Device events |

The strongest investigation usually combines multiple sources.

---

# 🧩 10. Correlation — The Real Power of a SIEM

One event:

```text
Failed login ❓
```

Another:

```text
Port scan ❓
```

Another:

```text
Suspicious DNS query ❓
```

Together:

```text
Port Scan
    ↓
DNS Resolution
    ↓
Credential Attempts
    ↓
Successful Login
    ↓
Internal Connections
```

Now the story becomes much more interesting. 🚨

Correlation turns isolated data into context.

---

# 🧠 11. Alert Triage

When an alert arrives, do not immediately panic. 😄

Use a repeatable process.

```text
ALERT
  ↓
Validate
  ↓
Identify Asset
  ↓
Identify User
  ↓
Understand Normal Behavior
  ↓
Check Related Events
  ↓
Determine Severity
  ↓
Escalate / Contain / Close
```

### Five quick questions

1. What happened?
2. Which asset was involved?
3. Who was involved?
4. When did it happen?
5. Is it expected?

---

# 🚦 12. Severity and Priority

Not every alert deserves the same response.

Consider:

```text
Impact × Confidence × Asset Criticality
```

Example:

### Low

```text
Single failed login
```

### Medium

```text
Repeated failures against one user
```

### High

```text
Successful privileged login from unusual source
```

### Critical

```text
Confirmed compromise of critical infrastructure
```

> Severity should be based on evidence and business impact, not simply scary-looking logs.

---

# 🕵️ 13. Baseline — Know What Normal Looks Like

Suppose a backup server normally transfers:

```text
02:00–04:00
Large internal traffic
```

A huge transfer at 02:30 may be normal.

The same transfer from a receptionist workstation at 14:20 could be highly unusual.

Therefore:

> **Anomaly = deviation from relevant normal behavior.**

Baseline dimensions:

- Time
- User
- Device
- Destination
- Protocol
- Volume
- Frequency

---

# 🚨 14. False Positives and False Negatives

### False Positive

Alert says malicious.

Reality = benign.

```text
🚨 Alert
   ↓
Backup server scanning known systems
   ↓
Actually legitimate
```

### False Negative

Malicious activity occurs.

Detection misses it.

```text
😈 Attack
   ↓
No alert
   ↓
Compromise continues
```

Security teams must reduce both.

But blindly increasing alerts creates another problem:

> 🔥 **Alert fatigue**

---

# 🧠 15. Alert Fatigue

Imagine an analyst receives:

```text
08:00 → 200 alerts
09:00 → 350 alerts
10:00 → 280 alerts
11:00 → 410 alerts
```

If almost everything is noisy, the important alert can disappear inside the noise.

Better approach:

```text
Raw Events
   ↓
Filtering
   ↓
Enrichment
   ↓
Correlation
   ↓
Prioritization
   ↓
Actionable Alerts
```

---

# 🔍 16. Investigation Enrichment

An IP address alone is weak context.

Instead:

```text
10.10.10.55
    ↓
DHCP lookup
    ↓
MAC address
    ↓
Switch port
    ↓
VLAN
    ↓
Hostname
    ↓
User
    ↓
Endpoint status
```

Now the analyst can answer:

> **Who/what actually generated this traffic?**

---

# 🔌 17. DHCP + Switch + Security Correlation

Suppose SIEM reports:

```text
10.10.10.55 suspicious scanning
```

DHCP tells you:

```text
10.10.10.55 → MAC AA:BB:CC:DD:EE:55
```

Switch tells you:

```text
MAC AA:BB:CC:DD:EE:55 → Gi0/17
```

VLAN information tells you:

```text
Gi0/17 → VLAN 10 USER
```

Now you know where the physical/logical source lives.

This is why networking knowledge is extremely valuable in a SOC. 🔥

---

# 🧭 18. Attack Surface Thinking

An enterprise has many possible entry points:

```text
             🌍 Internet
                 |
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     VPN      Web App     Email
       |         |         |
       └─────────┼─────────┘
                 ↓
              Internal
                 |
       ┌─────────┼──────────┐
       ↓         ↓          ↓
     Users     Servers      IoT
```

Security operations asks:

> Which entry point was used?

Then:

> Where did the attacker go next?

---

# 🛰️ 19. Reconnaissance Detection

Reconnaissance often creates patterns.

Example:

```text
Host A
 ↓
Host B:22
Host C:22
Host D:22
Host E:22
...
```

Or:

```text
Host A
 ↓
Many destination ports
 ↓
Many internal systems
```

Potential signs:

- High destination diversity
- Sequential addressing patterns
- Many connection attempts
- Many closed/rejected connections
- Unusual source host

But remember:

> Vulnerability scanners and network monitoring systems can also generate similar traffic.

Context matters.

---

# 🧨 20. Lateral Movement Detection

After compromise, attackers may attempt to move internally.

Conceptual path:

```text
Compromised User PC
        ↓
Credential Access
        ↓
Internal Discovery
        ↓
Server Connection
        ↓
Administrative Protocol
        ↓
New Host
```

Network evidence may include:

- New internal destinations
- Administrative protocols
- Unusual authentication
- Unusual source-to-server relationships
- New east-west traffic

---

# 🎯 21. Command and Control — C2

A compromised host may communicate with attacker infrastructure.

Conceptually:

```text
Victim Host
    |
    | periodic communication
    ↓
External Infrastructure
```

Potential clues:

- Regular beacon-like timing
- Unusual destinations
- Suspicious DNS activity
- Unexpected encrypted connections
- Rare domains/IPs
- New outbound destinations

One clue alone does not prove compromise.

Correlation is key.

---

# 🌐 22. DNS as a Hunting Signal

DNS is especially useful because many applications depend on it.

Look for:

```text
Rare domain
New domain
High query frequency
Unusual subdomain patterns
Unexpected external resolver
```

Example:

```text
PC-55
 ↓
DNS
 ↓
random-looking-domain.example
 ↓
External IP
 ↓
Repeated HTTPS connections
```

Investigate the chain rather than only the domain.

---

# 📦 23. Data Exfiltration Concepts

An attacker may attempt to move data out of the organization.

Potential clues:

```text
Normal:
Workstation → Cloud App
Small/expected transfers

Suspicious:
Workstation → Rare External Destination
Very Large Transfer
Unexpected Time
```

Useful telemetry:

- Firewall logs
- Proxy logs
- NetFlow/IPFIX
- DNS
- Endpoint telemetry
- DLP systems where available

Avoid assuming every large transfer is malicious.

Backups, software updates, cloud sync and video can all produce large volumes.

---

# 🧠 24. Network Threat Hunting

Threat hunting is proactive.

Instead of waiting for:

```text
🚨 Alert
```

the analyst asks:

> **"What suspicious behavior could be hiding without triggering an alert?"**

Hunting loop:

```text
Hypothesis
   ↓
Collect Data
   ↓
Query
   ↓
Investigate
   ↓
Validate
   ↓
New Detection
```

### Example hypothesis

> "A compromised workstation may be scanning internal systems before lateral movement."

Search flow data for:

- High destination count
- Short time window
- Multiple ports
- Unusual source

If validated:

> Turn the discovery into a detection rule. 🔥

---

# 📐 25. Detection Rule Design

A useful detection should include:

```text
Name
Purpose
Data Source
Logic
Threshold
Exceptions
Severity
Investigation Steps
Response
Owner
```

Example:

```text
Name:
Internal Port Sweep

Data:
NetFlow/IPFIX

Logic:
One workstation → many internal destinations
within a short interval

Exceptions:
Approved scanners

Severity:
Medium/High depending on context

Investigation:
DHCP → MAC → switch port → user → endpoint
```

---

# 🧪 26. Practical Lab — Build an Investigation Timeline

Given:

```text
10:00:10 DNS query
10:00:18 HTTPS connection
10:01:03 SSH failure
10:01:05 SSH failure
10:01:08 SSH success
10:02:11 Internal SMB connection
10:02:40 Internal RDP connection
```

Build:

```text
Time | Source | Destination | Event | Interpretation
```

Then ask:

1. Which event is the first anomaly?
2. What changed after authentication?
3. What evidence would you collect next?
4. Is this enough to declare an incident?

---

# 🧪 27. Practical Lab — Identify a Suspicious Host

You receive:

```text
Source IP: 10.10.10.55
```

Find:

```text
IP
 ↓
DHCP
 ↓
MAC
 ↓
Switch port
 ↓
VLAN
 ↓
Hostname
 ↓
User
```

Useful Cisco evidence:

```text
show ip arp
show mac address-table
show vlan brief
show interfaces status
show cdp neighbors
show logging
```

Then document the result.

---

# 🧪 28. Practical Lab — Network Scanning Investigation

Scenario:

```text
10.10.10.55
        |
        +----> 10.10.20.10
        +----> 10.10.20.11
        +----> 10.10.20.12
        +----> ...
        +----> 10.10.20.254
```

### Investigation checklist

```text
[ ] Is source approved scanner?
[ ] How many destinations?
[ ] How many ports?
[ ] How quickly?
[ ] Were connections successful?
[ ] Which services responded?
[ ] Who owns source host?
[ ] What process generated traffic?
[ ] Did authentication activity follow?
```

---

# 🧪 29. Practical Lab — Security Control Validation

Test the architecture safely in a lab.

### Scenario

```text
USER VLAN
   |
   | ❌
   ↓
ADMIN VLAN
```

Verify that unauthorized traffic is blocked.

Then test:

```text
ADMIN VLAN
   |
   | SSH ✅
   ↓
Network Device
```

Document:

```text
Expected
Actual
Evidence
Root Cause if Failed
Remediation
```

> ⚠️ Perform blocking/failover tests only in an isolated lab or approved maintenance window.

---

# 📡 30. Packet-Level Investigation with Wireshark

Wireshark lets you move from:

```text
"There was traffic"
```

to:

```text
"What exactly happened in the packet exchange?"
```

Useful investigation categories:

- DNS
- TCP
- TLS metadata
- ICMP/ICMPv6
- DHCP
- ARP/NDP
- HTTP where applicable

Example:

```text
DNS Query
   ↓
DNS Response
   ↓
TCP SYN
   ↓
SYN/ACK
   ↓
TLS handshake
   ↓
Application traffic
```

Packet analysis helps validate what higher-level logs report.

---

# 🧠 31. Network Evidence vs Endpoint Evidence

Network evidence tells you:

> **"What communicated with what?"**

Endpoint evidence may tell you:

> **"Which process/user generated it?"**

Example:

```text
Network:
PC → Suspicious IP

Endpoint:
unknown_process.exe → outbound connection
```

Together they are much stronger.

---

# 🚨 32. Incident Response Lifecycle

A simplified operational lifecycle:

```text
Preparation
    ↓
Detection / Identification
    ↓
Analysis
    ↓
Containment
    ↓
Eradication
    ↓
Recovery
    ↓
Lessons Learned
```

### Preparation

Have:

- Documentation
- Contacts
- Logging
- Backups
- Access
- Playbooks

### Detection

Recognize suspicious activity.

### Analysis

Determine what happened and impact.

### Containment

Limit damage.

### Eradication

Remove the cause.

### Recovery

Return systems safely to operation.

### Lessons Learned

Improve controls.

---

# 🚧 33. Containment Strategy

Containment should reduce risk without unnecessarily destroying evidence or causing a larger outage.

Possible controls:

- Endpoint isolation
- Switch-port restriction
- VLAN quarantine
- Firewall block
- Account disablement
- VPN access revocation

Decision model:

```text
Threat Confidence
      ×
Business Impact
      ×
Asset Criticality
      ↓
Containment Decision
```

Never blindly disconnect critical infrastructure without considering operational consequences and incident procedures.

---

# 📋 34. Evidence Preservation

Before changing everything, preserve useful evidence where possible.

Record:

```text
Timestamp
Source IP
Destination IP
Source MAC
Hostname
Username
VLAN
Switch Port
Alert ID
Relevant Logs
PCAP if available
Actions Taken
Who Approved Action
```

The goal is reproducibility:

> Another analyst should be able to understand what happened from your case notes.

---

# 🧭 35. Chain of Custody — Concept

For serious investigations, evidence handling may require controlled documentation.

Track:

```text
What evidence?
Who collected it?
When?
Where stored?
Who accessed it?
Was it modified?
```

Follow your organization's legal and incident-response procedures for formal investigations.

---

# 🏢 36. NH Technologies SOC Scenario

### 09:40

Firewall reports unusual outbound connections.

### 09:42

DNS telemetry shows a rare external domain.

### 09:44

Endpoint security flags suspicious activity.

### 09:45

NetFlow shows new east-west connections.

### 09:47

Authentication logs show failed attempts followed by a success.

### 09:50

SOC escalates to incident response.

---

## 🔎 Investigation

### Step 1 — Identify the source

```text
Source IP
   ↓
DHCP
   ↓
MAC
   ↓
Switch port
```

### Step 2 — Identify the owner

```text
MAC/IP
 ↓
Asset inventory
 ↓
Hostname
 ↓
User
```

### Step 3 — Build timeline

```text
DNS
 ↓
Outbound connection
 ↓
Endpoint alert
 ↓
Internal scan
 ↓
Authentication attempts
 ↓
Successful login
```

### Step 4 — Determine blast radius

Which systems were contacted?

### Step 5 — Contain

Apply approved isolation controls.

### Step 6 — Investigate further

Look for:

- Additional compromised systems
- Credential reuse
- Privileged access
- Persistence indicators
- Data movement

---

# 📊 37. Security Dashboard Design

A useful SOC dashboard should answer questions, not merely display colorful graphs. 🎨

Useful panels:

```text
🚨 Critical Alerts
🔐 Authentication Anomalies
🌐 Top External Destinations
🧩 Top Internal Talkers
📡 DNS Anomalies
🕵️ Scanning Sources
🔥 Firewall Denials
💻 High-Risk Endpoints
📈 Traffic Anomalies
```

### Bad dashboard

```text
100 random charts 📊📊📊📊
```

### Good dashboard

```text
What changed?
Why does it matter?
What should I investigate?
```

---

# 🧠 38. Security Playbooks

A playbook turns repeated investigation into a consistent workflow.

Example: **Suspicious Internal Scan**

```text
1. Validate alert
2. Identify source
3. Check approved scanners
4. Determine destination count
5. Determine ports
6. Identify user/device
7. Check endpoint telemetry
8. Check authentication events
9. Determine malicious likelihood
10. Escalate/contain if required
11. Document evidence
```

Playbooks reduce analyst guesswork.

---

# 🤖 39. Automation in Security Operations

Automation can help with repetitive enrichment.

Example:

```text
Alert: 10.10.10.55
        ↓
Automation
        ├── DHCP lookup
        ├── DNS lookup
        ├── Asset lookup
        ├── Threat-intel lookup
        ├── Switch-port lookup
        └── Recent authentication lookup
        ↓
Enriched Alert
```

The analyst receives context instead of manually searching six systems.

### Important principle

> Automate repetitive work, not unreviewed destructive decisions.

---

# 🐍 40. Python Security-Enrichment Concept

A future automation can conceptually perform:

```python
alert_ip = "10.10.10.55"

# 1. Identify asset
# 2. Query DHCP data
# 3. Find MAC address
# 4. Find switch port
# 5. Query recent DNS activity
# 6. Query flow data
# 7. Build investigation summary
```

This connects directly with **Module 41 — Python for Network Automation**.

---

# 🧱 41. Network Security Architecture Feedback Loop

Security operations should improve architecture.

```text
Attack / Incident
       ↓
Investigation
       ↓
Root Cause
       ↓
Control Gap
       ↓
Architecture Change
       ↓
New Detection
       ↓
Validation
```

Example:

```text
Incident:
User PC reached database

Root Cause:
Missing segmentation

Improvement:
Firewall policy between zones

Detection:
Alert on unauthorized DB access
```

This is how an enterprise becomes stronger over time.

---

# 🧪 42. Boss-Level Lab — Full SOC Investigation

You receive this alert:

```text
🚨 ALERT #NH-2026-049
Source: 10.10.10.55
```

Telemetry:

```text
09:12 DNS → rare domain
09:13 HTTPS → new external IP
09:17 22 internal destinations
09:18 143 internal destinations
09:19 SSH failures
09:20 SSH success
09:21 Server-03 contacted
09:23 Large outbound transfer
09:25 Endpoint alert
```

### Your mission

Build a complete incident record.

#### A. Identification

```text
Source IP:
MAC:
Hostname:
User:
VLAN:
Switch Port:
```

#### B. Timeline

Create a minute-by-minute timeline.

#### C. Network Analysis

Identify:

- External destinations
- Internal destinations
- Ports
- Protocols
- Connection volume

#### D. Authentication Analysis

Determine:

- Failed logins
- Successful logins
- Privileged accounts
- Source/destination relationships

#### E. Endpoint Correlation

Determine whether the network activity aligns with endpoint evidence.

#### F. Blast Radius

List every affected asset.

#### G. Containment

Propose the safest approved containment action.

#### H. Detection Improvement

Write one new detection rule.

#### I. Architecture Improvement

Recommend one segmentation or access-control improvement.

---

# 💀 43. Ultra Boss Challenge — Two Attackers, One Network

NH Technologies sees two suspicious sources.

```text
Host A → many internal hosts
Host B → rare external domains
```

At first they appear unrelated.

Then:

```text
Host A → Server-01
Host B → Server-01
```

And both appear to authenticate using related accounts.

### Your challenge

Determine whether:

- This is one campaign
- Two independent incidents
- Legitimate administration
- A compromised shared account

You must correlate:

```text
Network
DNS
DHCP
Authentication
Endpoint
Firewall
Flow
Timeline
```

The objective is not to guess.

> **The objective is to build an evidence-backed hypothesis.** 🔎

---

# 🎯 44. Interview Questions

### Q1. What is the difference between an event and an alert?

An event records something that happened; an alert indicates that something deserves investigation according to a detection or rule.

### Q2. What is alert fatigue?

Analyst overload caused by excessive or low-value alerts, which can reduce attention to genuinely important incidents.

### Q3. Why is NTP important in security operations?

Accurate timestamps enable reliable correlation and incident timelines across systems.

### Q4. What is threat hunting?

A proactive search for suspicious activity that may not have triggered existing detections.

### Q5. Why correlate DHCP and switch data?

To map an IP address to a MAC address, physical/logical port, and VLAN, helping identify the actual device.

### Q6. What is lateral movement?

An attacker's movement from an initially compromised system toward additional internal systems.

### Q7. Why is NetFlow useful in a SOC?

It reveals communication patterns, traffic volume and relationships between hosts without requiring full packet capture of every connection.

### Q8. What is a false positive?

An alert classified as suspicious by detection logic even though the activity is benign.

### Q9. What is a false negative?

Malicious activity that the detection system fails to identify.

### Q10. What is detection engineering?

Designing, testing, tuning and maintaining detections for meaningful suspicious behavior.

### Q11. What is containment?

Actions taken to limit ongoing impact while the incident is being investigated and resolved.

### Q12. Why should analysts avoid immediately changing everything?

Uncontrolled changes can destroy evidence, obscure the timeline or cause unnecessary business disruption.

---

# ⚡ 45. Quick Revision

```text
📦 Event
   ↓
🚨 Alert
   ↓
🔎 Triage
   ↓
🧩 Enrichment
   ↓
🧠 Correlation
   ↓
🕵️ Investigation
   ↓
🚨 Incident
   ↓
🚧 Containment
   ↓
🔧 Eradication / Recovery
   ↓
📋 Lessons Learned
   ↓
🏗️ Better Detection + Architecture
```

---

# 🧾 46. SOC Cheat Sheet

| Concept | Remember |
|---|---|
| Event | Something happened |
| Alert | Something deserves attention |
| Incident | Confirmed/validated security issue |
| Case | Investigation record |
| NOC | Availability/performance |
| SOC | Security monitoring/response |
| SIEM | Correlation + security analytics |
| NetFlow/IPFIX | Flow visibility |
| Syslog | Device/event evidence |
| NTP | Time synchronization |
| Baseline | What normal looks like |
| False positive | Benign activity flagged |
| False negative | Malicious activity missed |
| Alert fatigue | Too much low-value alerting |
| Threat hunting | Proactive investigation |
| Lateral movement | Internal attacker movement |
| C2 | Attacker-controlled communication |
| Containment | Limit ongoing impact |
| Evidence | Data supporting conclusions |
| Playbook | Repeatable response workflow |
| Detection engineering | Build/tune meaningful detections |

---

# 🧠 47. The SOC Analyst Mental Model

When an alert arrives, remember:

```text
WHAT happened?
      ↓
WHO / WHAT caused it?
      ↓
WHEN did it happen?
      ↓
WHERE did it happen?
      ↓
WHAT communicated with WHAT?
      ↓
IS it normal?
      ↓
WHAT happened before it?
      ↓
WHAT happened after it?
      ↓
HOW FAR did it spread?
      ↓
WHAT evidence supports the hypothesis?
      ↓
WHAT should be contained?
      ↓
HOW do we prevent recurrence?
```

Do not jump straight to:

> **"It's malware!"**

Instead say:

> **"Here is the evidence, here is the timeline, here is the hypothesis, here is what we validated, and here is the recommended response."** 🧠🔎

---

# 🏆 48. Portfolio Project — NH Technologies Security Operations Center

Build a portfolio-ready mini SOC architecture.

```text
                    🌍 INTERNET
                         |
                    🔥 Firewall
                         |
              ┌──────────┴──────────┐
              |                     |
            DMZ                  Internal
                                    |
                         ┌──────────┼──────────┐
                         |          |          |
                       Users     Servers      Admin
                         |          |          |
                         └────┬─────┴──────────┘
                              |
                         📊 Telemetry
                              |
                    ┌─────────┼─────────┐
                    ↓         ↓         ↓
                 Syslog     NetFlow    DNS
                    |         |         |
                    └─────────┼─────────┘
                              ↓
                           SIEM
                              ↓
                         🚨 Detection
                              ↓
                           SOC 🛡️
                              ↓
                       Incident Response
```

Document:

1. Data sources
2. Detection rules
3. Severity model
4. Investigation workflow
5. Containment process
6. Evidence requirements
7. Escalation process
8. Network architecture improvements

This is a strong bridge from **CCNA → SOC → Blue Team**.

---

# 🔥 Final Takeaway

You have now moved beyond:

> **"I know networking commands."**

You are building the ability to say:

> **"I understand how enterprise traffic should behave, I can recognize when it deviates, I can trace the device behind an IP address, correlate network and security telemetry, investigate the timeline, assess blast radius, and recommend containment."**

That is the beginning of real defensive security thinking. 🛡️🌐🕵️

---

# 🚀 Next Module

## **Module 50 — Network Security Protocols & Cryptography Fundamentals** 🔐🔑

Next we go deeper into the protocols underneath secure communication:

```text
🔐 Encryption
      ↓
🔑 Keys
      ↓
🧮 Hashing
      ↓
✍️ Digital Signatures
      ↓
📜 Certificates / PKI
      ↓
🔒 TLS
      ↓
🔐 SSH
      ↓
🛡️ IPsec
      ↓
📡 Secure Network Communication
```

> 🎯 **Module 49 complete — you are now learning not just how to secure a network, but how to operate, monitor, investigate and defend it.** 🛡️🔥
# 39 — Network Automation & Programmability 🤖

> **Goal:** Understand how modern networks move beyond manual CLI configuration toward controllers, APIs, data formats, automation, and intent-based operations.

---

# 🧭 Where We Are

We have now covered traditional networking fundamentals and QoS.

The next evolution is:

```text
Manual CLI
   ↓
Scripts
   ↓
APIs
   ↓
Controllers
   ↓
Automation
   ↓
Intent-Based Networking
```

This is especially important for modern network engineers and cybersecurity professionals because enterprise environments can contain **hundreds or thousands of devices**.

---

# 🎯 Learning Objectives

By the end of this module, you should be able to:

- Explain network automation
- Compare manual vs automated configuration
- Understand controllers and controller-based networking
- Explain APIs and REST APIs
- Understand JSON and YAML at a practical level
- Explain CRUD operations
- Understand synchronous API request/response behavior
- Distinguish northbound and southbound interfaces
- Understand NETCONF and RESTCONF concepts
- Explain configuration management
- Understand infrastructure as code at a high level
- Connect automation with cybersecurity
- Understand why Python is useful for network automation
- Identify common automation risks
- Prepare for the next Python/API labs

---

# 1. What Is Network Automation?

**Network automation** means using software, scripts, APIs, controllers, or orchestration systems to perform network tasks with reduced manual intervention.

Traditional approach:

```text
Engineer
   ↓
SSH
   ↓
Switch
   ↓
Type commands manually
```

Automated approach:

```text
Engineer
   ↓
Automation script/controller
   ↓
API / SSH / NETCONF / RESTCONF
   ↓
Many devices
```

### 🧠 Simple definition

> **Network automation = using software to perform repeatable network operations consistently and efficiently.**

---

# 2. Why Automation? 🚀

Imagine a company has:

```text
500 switches
100 routers
50 firewalls
```

A configuration change must be applied to 500 switches.

### Manual method

```text
SSH → Switch 1
SSH → Switch 2
SSH → Switch 3
...
SSH → Switch 500
```

This is:

- Slow 🐌
- Error-prone ❌
- Difficult to audit
- Difficult to repeat

### Automated method

```text
One approved workflow
       ↓
500 devices
       ↓
Consistent result
```

Automation provides:

- ⚡ Speed
- 🎯 Consistency
- 📋 Repeatability
- 🔍 Auditability
- 📈 Scalability

---

# 3. Real-World Analogy 🏭

Imagine a factory.

### Old factory

One worker manually assembles every product.

### Modern factory

Machines perform repeatable tasks automatically.

Networking is moving in the same direction.

```text
Manual networking
= technician repeatedly types commands

Automated networking
= software performs repeatable workflows
```

Humans still make the decisions.

Automation handles repetitive execution.

---

# 4. Manual vs Automated Networking

| Manual | Automated |
|---|---|
| Human-driven | Software-driven |
| Slow at scale | Fast at scale |
| More typing errors | More consistency |
| Harder to repeat exactly | Repeatable |
| Device-by-device | Many devices |
| Difficult to standardize | Easy to standardize |

⚠️ Automation does **not** eliminate the need for networking knowledge.

In fact:

> **The better you understand networking, the better you can automate it.**

---

# 5. Configuration Drift ⚠️

Suppose 100 switches should have the same configuration.

Over time:

```text
Switch 1 → Version A
Switch 2 → Version A
Switch 3 → Version B
Switch 4 → Version A
...
Switch 80 → Version C
```

This difference is called **configuration drift**.

Automation can help enforce a desired configuration.

---

# 6. Automation Workflow

A basic automation workflow looks like:

```text
Requirement
    ↓
Desired state
    ↓
Automation logic
    ↓
API / protocol / connection
    ↓
Network devices
    ↓
Verification
    ↓
Report
```

The verification stage is extremely important.

Never assume:

```text
Command sent = change succeeded
```

Instead:

```text
Change sent
   ↓
Verify actual state
```

---

# 7. Controllers 🎛️

A **controller** is a centralized software system that can manage or coordinate network devices.

Instead of engineers interacting independently with every device:

```text
             Controller
          🧠 Central Logic
          /      |      \
         /       |       \
       SW1      R1       SW2
```

The controller can provide:

- Centralized visibility
- Configuration workflows
- Policy management
- Telemetry
- Automation
- APIs

---

# 8. Traditional vs Controller-Based Networking

### Traditional

```text
Engineer
 ├── SSH → R1
 ├── SSH → R2
 ├── SSH → SW1
 └── SSH → SW2
```

### Controller-based

```text
             Engineer / App
                    │
                    ▼
               Controller
              /    |    \
             ▼     ▼     ▼
            R1    SW1    SW2
```

The controller provides an abstraction layer.

---

# 9. SDN — Software-Defined Networking

**SDN** is an architectural approach that separates or abstracts network control functions from packet forwarding functions.

A simplified model:

```text
┌─────────────────────────────┐
│ Applications / Automation   │
└──────────────┬──────────────┘
               │
        Northbound APIs
               │
┌──────────────▼──────────────┐
│       SDN Controller        │
└──────────────┬──────────────┘
               │
        Southbound interfaces
               │
┌──────────────▼──────────────┐
│ Routers / Switches / Fabric │
└─────────────────────────────┘
```

This is a conceptual model; real controller architectures can vary.

---

# 10. Northbound vs Southbound

### Northbound Interface ⬆️

Connects the controller to applications, orchestration, and higher-level systems.

```text
Application
    ↑
Northbound
    ↑
Controller
```

### Southbound Interface ⬇️

Connects the controller toward network devices/infrastructure.

```text
Controller
    ↓
Southbound
    ↓
Network devices
```

### 🧠 Memory trick

```text
NORTH = toward applications
SOUTH = toward devices
```

---

# 11. APIs 🔌

**API = Application Programming Interface.**

An API provides a defined way for software components to communicate.

Instead of a human typing:

```text
show ip interface brief
```

software may communicate with a controller/device through an API.

Conceptually:

```text
Python program
      ↓
     API
      ↓
Controller
      ↓
Network
```

---

# 12. REST API 🌐

A common web-style API architecture is **REST — Representational State Transfer**.

REST APIs commonly use HTTP/HTTPS.

Typical methods:

| Method | CRUD idea | Common meaning |
|---|---|---|
| GET | Read | Retrieve data |
| POST | Create/action | Create resource or trigger action |
| PUT | Update/replace | Replace/update resource |
| PATCH | Partial update | Modify part of resource |
| DELETE | Delete | Remove resource |

### 🧠 CRUD

```text
C = Create
R = Read
U = Update
D = Delete
```

---

# 13. API Request/Response Model

A simplified REST interaction:

```text
Client
  │
  │ GET /devices
  ▼
Server / Controller
  │
  │ HTTP response
  ▼
Client
```

Example conceptual response:

```json
{
  "device": "SW1",
  "status": "online"
}
```

The exact endpoint, authentication, headers, and response format depend on the API.

---

# 14. HTTP Status Codes

Automation engineers should recognize common response codes.

| Code | Meaning |
|---:|---|
| 200 | OK |
| 201 | Created |
| 202 | Accepted |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized / authentication required |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 500 | Server Error |
| 503 | Service Unavailable |

### Troubleshooting example

```text
API call → 401
```

Think:

> Authentication/credentials/token problem.

```text
API call → 404
```

Think:

> Endpoint/resource may not exist.

---

# 15. JSON 📦

**JSON = JavaScript Object Notation.**

It is a common data-interchange format used by APIs and automation systems.

Example:

```json
{
  "hostname": "SW1",
  "management_ip": "192.168.10.10",
  "status": "up",
  "vlans": [10, 20, 30]
}
```

### JSON building blocks

```text
Object      → { }
Array       → [ ]
Key/value   → "key": "value"
```

---

# 16. JSON Data Types

Common types:

```json
{
  "hostname": "SW1",
  "enabled": true,
  "ports": 24,
  "vlans": [10, 20, 30],
  "description": null
}
```

Here:

- String → `"SW1"`
- Boolean → `true`
- Number → `24`
- Array → `[10,20,30]`
- Null → `null`

---

# 17. YAML 📝

**YAML** is another human-friendly data serialization format often seen in automation/configuration workflows.

Example:

```yaml
hostname: SW1
management_ip: 192.168.10.10
status: up
vlans:
  - 10
  - 20
  - 30
```

Compared with JSON, YAML often uses indentation instead of braces and commas.

### ⚠️ YAML indentation matters

```yaml
network:
  device: SW1
```

Incorrect indentation can change the meaning or make the document invalid.

---

# 18. JSON vs YAML

| JSON | YAML |
|---|---|
| Very common in REST APIs | Common in configuration/automation |
| Uses `{}` and `[]` | Uses indentation and list markers |
| Machine-friendly | Human-friendly |
| Strict syntax | Indentation-sensitive |

You do not need to become a JSON/YAML expert before learning automation.

You need to become comfortable **reading and manipulating structured data**.

---

# 19. NETCONF

**NETCONF = Network Configuration Protocol.**

It is designed for managing network device configuration and operational data.

It commonly uses:

- SSH for transport
- XML for data encoding
- YANG as a data modeling language

Conceptually:

```text
Automation system
      ↓
   NETCONF
      ↓
     SSH
      ↓
Network device
```

---

# 20. RESTCONF

**RESTCONF** provides a REST-like HTTP interface for interacting with YANG-modeled data.

Conceptually:

```text
Application
    ↓
HTTPS / RESTCONF
    ↓
YANG-modeled device/controller
```

### NETCONF vs RESTCONF

| NETCONF | RESTCONF |
|---|---|
| Network configuration protocol | REST-style network management interface |
| Commonly SSH transport | HTTP/HTTPS |
| XML commonly used | JSON or XML can be used depending on implementation |
| RPC-oriented operations | HTTP methods |
| YANG models | YANG models |

---

# 21. YANG 🧩

**YANG** is a data modeling language used to model configuration and operational state for network management.

Think of YANG as a **schema/blueprint**.

```text
YANG model
    ↓
Defines structure
    ↓
Device data
    ↓
NETCONF / RESTCONF
```

### Analogy

A building blueprint tells you:

- What rooms exist
- Where they belong
- What structure is allowed

YANG similarly describes the structure of network data.

---

# 22. Why Structured Data Matters

CLI output is designed primarily for humans.

Example:

```text
Interface              IP-Address      Status
Gig0/0                 10.0.0.1        up
Gig0/1                 192.168.1.1     up
```

Automation prefers predictable structured data:

```json
{
  "interface": "Gig0/0",
  "ip": "10.0.0.1",
  "status": "up"
}
```

Software can parse structured data more reliably than arbitrary human-formatted text.

---

# 23. Idempotency ⭐

**Idempotency** is an important automation concept.

An idempotent operation can be run repeatedly while producing the same desired final state rather than repeatedly causing unintended changes.

Example desired state:

```text
Hostname = SW1
```

Running the automation once:

```text
SW1
```

Running it again:

```text
SW1
```

The desired state remains correct.

### Why it matters

Automation often runs repeatedly.

Good automation should be:

- Predictable
- Repeatable
- Safe
- Verifiable

---

# 24. Desired State vs Current State

Modern automation often compares:

```text
CURRENT STATE
      │
      ▼
   Compare
      ▲
      │
DESIRED STATE
```

Example:

```text
Desired:
VLAN 10 exists
VLAN 20 exists
SSH enabled
NTP configured
```

Automation checks the device.

If something is missing:

```text
Detect difference
      ↓
Apply change
      ↓
Verify
```

---

# 25. Infrastructure as Code (IaC) 💻

Infrastructure as Code means representing infrastructure configuration in code or declarative files so it can be managed consistently and automatically.

Conceptual example:

```text
Git repository
     ↓
Configuration files
     ↓
Automation pipeline
     ↓
Network infrastructure
```

Benefits include:

- Version control
- Review process
- Repeatability
- Audit history
- Easier rollback

This connects networking directly with DevOps practices.

---

# 26. Git + Network Automation 🔥

A modern workflow can look like:

```text
Engineer
   ↓
Edit automation/config
   ↓
Git commit
   ↓
Code review
   ↓
Automated validation
   ↓
Deployment
   ↓
Verification
```

This is one reason Git knowledge is valuable for network engineers.

---

# 27. Automation + Cybersecurity 🛡️

Automation is highly relevant to security operations.

Imagine 1,000 switches need an emergency configuration update.

Manual:

```text
1,000 devices × manual work
```

Automated:

```text
Approved workflow
       ↓
1,000 devices
       ↓
Verification
```

Security use cases include:

- Disabling compromised accounts
- Updating ACLs
- Applying secure configurations
- Collecting logs
- Checking compliance
- Rotating configuration values through approved systems
- Gathering indicators from devices
- Detecting configuration drift

⚠️ Automation must be tightly controlled. A bad script can make a bad change to hundreds of devices extremely quickly.

---

# 28. Automation Security Principles 🔐

Never treat automation as automatically safe.

Use:

- Least privilege
- Strong authentication
- Secrets management
- HTTPS/SSH
- Access controls
- Logging
- Change approval
- Version control
- Testing
- Rollback plans

### Dangerous scenario

```text
Buggy script
   ↓
500 routers
   ↓
Wrong configuration
   ↓
Large outage 💥
```

### Better workflow

```text
Code
 ↓
Lint/test
 ↓
Lab
 ↓
Small pilot
 ↓
Approval
 ↓
Controlled rollout
 ↓
Verification
```

---

# 29. Python 🐍 and Networking

Python is widely used for automation because it provides:

- Easy-to-read syntax
- Large ecosystem
- HTTP/API libraries
- SSH/networking libraries
- JSON/YAML handling
- Data processing
- Testing capabilities

Conceptually:

```text
Python
  ↓
Connect
  ↓
Collect data
  ↓
Analyze
  ↓
Change configuration
  ↓
Verify
```

We will build this skill in upcoming modules rather than trying to learn everything at once.

---

# 30. Example Automation Logic

Imagine checking whether SSH is enabled.

Pseudo-workflow:

```text
for each device:
    connect
    collect state
    if SSH is disabled:
        enable approved configuration
    verify SSH
    record result
```

Notice the structure:

```text
INPUT
 ↓
CHECK
 ↓
DECISION
 ↓
ACTION
 ↓
VERIFY
 ↓
REPORT
```

This same logic appears in SOC automation.

---

# 31. Automation Failure Modes 🛠️

### Authentication failure

```text
401 / SSH authentication failed
```

Check:

- Username
- Password/key
- Token
- Permissions

### Authorization failure

Authentication succeeds but the operation is denied.

Check:

- Role
- Privilege
- API permissions
- Device authorization

### Connectivity failure

Check:

- Routing
- DNS
- Firewall
- TCP port
- VPN
- Management interface

### Data parsing failure

Check:

- JSON syntax
- YAML indentation
- XML structure
- API schema

### Configuration failure

Check:

- Command/API payload
- Device capability
- Version compatibility
- Existing configuration

---

# 32. Automation Troubleshooting Decision Tree 🌳

```text
Automation failed
      │
      ▼
Can it reach the device/API?
   │             │
  NO            YES
   │             │
   ▼             ▼
Network       Authentication?
problem          │
              NO │ YES
                 │   │
                 ▼   ▼
             Fix auth  Authorization?
                           │
                        NO │ YES
                           │   │
                           ▼   ▼
                       Fix role  Validate
                                 payload/data
                                     │
                                     ▼
                                  Verify state
```

---

# 33. Industry Scenario 🏢

A bank has 2,000 branch routers.

Security team identifies an insecure management configuration.

### Old approach

Engineers manually log into every router.

### Modern approach

```text
Security requirement
       ↓
Approved automation
       ↓
Inventory
       ↓
Test on lab devices
       ↓
Pilot deployment
       ↓
Production rollout
       ↓
Compliance verification
       ↓
Report
```

This reduces repetitive effort and provides evidence of what changed.

---

# 34. 🎯 Scenario-Based Challenge

You manage 100 switches.

Requirement:

```text
Every switch must:
✓ Use hostname format SW-XXX
✓ Have SSH enabled
✓ Use the approved NTP server
✓ Have unused access ports disabled
✓ Have the management VLAN configured
```

### Questions

1. Would you configure these manually or automate them?
2. What data would you collect first?
3. How would you define the desired state?
4. How would you test the automation?
5. How would you prevent a bad script from changing all 100 switches?
6. How would you verify success?
7. Where would Git fit into the workflow?

### ⭐ Engineer mindset

Don't think:

> “How do I type these commands 100 times?”

Think:

> “How do I define the desired state once and safely apply it everywhere?”

---

# 35. 🧪 Mini Lab — Read a REST Response

Consider:

```json
{
  "hostname": "SW1",
  "management": {
    "ip": "192.168.10.10",
    "status": "up"
  },
  "vlans": [10, 20, 30]
}
```

### Questions

1. What is the hostname?
2. What is the management IP?
3. Is the device up?
4. How many VLAN values are listed?
5. What type of structure is `vlans`?

### Answers

```text
hostname → SW1
IP       → 192.168.10.10
status   → up
VLANs    → 3 values
vlans    → array/list
```

---

# 36. 🧪 Mini Lab — HTTP Thinking

Suppose an API returns:

```text
GET /devices → 200
```

This means the request succeeded.

Suppose:

```text
POST /devices → 201
```

The resource was successfully created.

Suppose:

```text
GET /devices/SW99 → 404
```

The requested resource was not found.

Suppose:

```text
GET /devices → 401
```

Authentication is required or failed.

---

# 37. 🔥 Interview Questions

### Q1. What is network automation?

Using software, scripts, APIs, or controllers to perform network operations consistently and at scale.

### Q2. Why automate networks?

To improve speed, consistency, scalability, repeatability, and operational visibility.

### Q3. What is an API?

A defined interface that allows software components to communicate.

### Q4. What is REST?

An architectural style commonly implemented using HTTP methods and resource-oriented APIs.

### Q5. What is JSON?

A structured data-interchange format commonly used by APIs.

### Q6. What is YAML?

A human-friendly data serialization format frequently used for configuration and automation.

### Q7. What is NETCONF?

A network management/configuration protocol commonly using SSH and XML, with YANG data models.

### Q8. What is RESTCONF?

A REST-style interface for interacting with YANG-modeled network data over HTTP/HTTPS.

### Q9. What is YANG?

A data modeling language that defines the structure of network configuration and operational data.

### Q10. What is the difference between northbound and southbound interfaces?

Northbound connects controllers to higher-level applications/orchestration; southbound connects controllers toward network infrastructure.

### Q11. What is idempotency?

The ability to repeatedly apply an operation while maintaining the intended final state without unintended cumulative effects.

### Q12. Why is automation dangerous if poorly designed?

A single error can be propagated quickly across many devices and create a large outage or security problem.

---

# 38. 🧠 Quick Revision

```text
NETWORK AUTOMATION
│
├── Why?
│   ├── Speed
│   ├── Consistency
│   ├── Scale
│   └── Repeatability
│
├── Controller
│   └── Centralized management/coordination
│
├── Interfaces
│   ├── Northbound → applications
│   └── Southbound → infrastructure
│
├── APIs
│   └── Software communication interface
│
├── REST
│   ├── GET
│   ├── POST
│   ├── PUT
│   ├── PATCH
│   └── DELETE
│
├── Data
│   ├── JSON
│   └── YAML
│
├── Network management
│   ├── NETCONF
│   ├── RESTCONF
│   └── YANG
│
├── Operations
│   ├── Desired state
│   ├── Current state
│   ├── Idempotency
│   └── Verification
│
└── Security
    ├── Least privilege
    ├── Secrets
    ├── Testing
    ├── Approval
    └── Rollback
```

---

# 39. ⚡ Cheat Sheet

| Term | Remember |
|---|---|
| Automation | Software-driven network operations |
| Controller | Centralized software control/management system |
| API | Interface for software communication |
| REST | Common HTTP-based API style |
| GET | Read/retrieve |
| POST | Create/action |
| PUT | Replace/update |
| PATCH | Partial update |
| DELETE | Remove |
| JSON | Structured data format |
| YAML | Human-friendly structured data |
| NETCONF | Network configuration protocol |
| RESTCONF | REST-style network management interface |
| YANG | Data modeling language |
| Northbound | Controller ↔ applications |
| Southbound | Controller ↔ network devices |
| Desired state | What the network should look like |
| Current state | What the network looks like now |
| Idempotency | Repeated runs preserve desired result |
| IaC | Infrastructure represented/managed as code |
| Configuration drift | Devices diverge from intended configuration |

---

# 40. 🧩 Final Mental Model

Modern networking is becoming:

```text
                    👨‍💻 Engineer
                         │
                         ▼
                  📦 Git / Code
                         │
                         ▼
                  🤖 Automation
                         │
                ┌────────┴────────┐
                ▼                 ▼
             REST API          NETCONF
                │                 │
                └────────┬────────┘
                         ▼
                    🎛️ Controller
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
             R1         SW1        SW2
              │          │          │
              └──────────┴──────────┘
                         │
                         ▼
                    🔍 Verify
                         │
                         ▼
                    📊 Report
```

> **Traditional networking teaches you how to configure one device. Automation teaches you how to operate an entire infrastructure.**

---

## 🚀 Next Module

**40 — APIs, JSON & REST Deep Dive 🔌**

We will go deeper into:

- HTTP request/response
- Headers
- Authentication
- REST resources
- JSON objects and arrays
- API endpoints
- CRUD operations
- Status codes
- Postman-style API testing concepts
- Network-device API examples
- Practical API troubleshooting
- Security considerations for APIs

# 🐍⚡ Module 41 — Python for Network Automation

> **From clicking commands manually → writing tools that do the work for you.**

![Python](https://img.shields.io/badge/Python-Automation-blue?style=for-the-badge&logo=python)
![Networking](https://img.shields.io/badge/Networking-CCNA-orange?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Beginner%20→%20Advanced-success?style=for-the-badge)

---

## 🗺️ Where We Are in the Journey

You have already learned how networks work and how engineers manage them:

```text
Networking Fundamentals
        ↓
Switching & VLANs
        ↓
Routing & OSPF
        ↓
DHCP → DNS → ACL → NAT
        ↓
Wireless → Security → Management
        ↓
Automation + APIs + JSON + REST
        ↓
🐍 PYTHON FOR NETWORK AUTOMATION  ← YOU ARE HERE
        ↓
Network Automation Projects
        ↓
Troubleshooting + Enterprise Labs
        ↓
SOC / Cybersecurity Automation
```

### 🎯 Mission

By the end of this module, you should be able to look at a repetitive networking task and think:

> **“Why am I doing this manually? I can make Python do it.”**

---

# 🎯 1. Learning Objectives

After completing this module, you should be able to:

- 🐍 Understand why Python is popular in networking
- 📦 Work with Python data structures
- 🔀 Use conditions and loops
- 🧩 Build reusable functions
- 🛡️ Handle errors with exceptions
- 📁 Read and write files
- 📊 Work with CSV data
- 🧾 Parse and create JSON
- 🌐 Call REST APIs with Python
- 🔐 Handle credentials safely
- 🌍 Validate and calculate IP addresses with `ipaddress`
- 🖥️ Understand SSH automation
- 🔌 Understand libraries such as Netmiko and Paramiko
- 🧰 Build network automation scripts
- 🔍 Parse network information
- 🧪 Test automation safely before production
- 🛠️ Troubleshoot Python automation failures
- 🏢 Think like an enterprise network automation engineer

---

# 🤔 2. Why Python for Networking?

Imagine an engineer has **200 switches**.

Each switch requires:

```text
Login
 ↓
Check hostname
 ↓
Check interfaces
 ↓
Check VLANs
 ↓
Collect configuration
 ↓
Save output
 ↓
Logout
```

Doing this manually:

```text
Switch 1   😐
Switch 2   😐
Switch 3   😐
...
Switch 200 😭
```

Python changes the model:

```text
        🐍 Python
            │
     ┌──────┼──────┐
     ↓      ↓      ↓
   SW1    SW2    SW3 ... SW200
     │      │      │
     └──────┼──────┘
            ↓
       📊 Collected Data
```

### 🚀 Automation = Scale + Consistency + Speed

Python does not replace networking knowledge.

It **multiplies** it.

> A person who knows Python but does not understand networking can automate mistakes very quickly.

So the order is:

**Understand networking → understand the task → automate it.**

---

# 🧠 3. Python as a Network Engineer's Swiss Army Knife

Python can help with:

| Task | Python Role |
|---|---|
| IP validation | `ipaddress` |
| REST APIs | `requests` |
| JSON | `json` |
| CSV inventory | `csv` |
| SSH | Netmiko / Paramiko |
| File processing | built-in file handling |
| Automation logic | `if`, `for`, functions |
| Logging | `logging` |
| Text parsing | strings / regex |
| Testing | assertions / test frameworks |
| Secrets | environment variables |

Think of Python as the **control center** 🧠.

---

# 🧰 4. Environment Setup

You need:

```text
💻 Computer
 ├── Python 3
 ├── Terminal / PowerShell
 ├── VS Code or another editor
 └── Optional: Cisco Packet Tracer / EVE-NG / GNS3
```

Verify Python:

```bash
python --version
```

or:

```bash
python3 --version
```

Check pip:

```bash
python -m pip --version
```

### 📦 Virtual Environment

For projects, isolate dependencies:

```bash
python -m venv .venv
```

Activate on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Linux/macOS:

```bash
source .venv/bin/activate
```

Install a package:

```bash
python -m pip install requests
```

> 💡 **Best practice:** Keep project dependencies isolated instead of installing everything globally.

---

# 🐣 5. Python Basics — Only What Networking Needs

We are not trying to become software engineers in one module.

We are learning the Python pieces a network engineer actually needs.

---

# 📦 6. Variables

A variable stores information.

```python
hostname = "SW1"
ip_address = "192.168.10.10"
interface_count = 24
is_reachable = True
```

Think:

```text
hostname ───────→ "SW1"
ip_address ─────→ "192.168.10.10"
interfaces ─────→ 24
reachable ──────→ True
```

---

# 🔤 7. Strings

Network automation constantly deals with text.

```python
hostname = "CORE-SW1"

print(hostname)
print(hostname.lower())
print(hostname.upper())
print(len(hostname))
```

Useful string operations:

```python
output = "GigabitEthernet0/1 is up"

print("up" in output)
print(output.startswith("GigabitEthernet"))
```

### 🔥 Practical Example

```python
interface = "GigabitEthernet0/1"

if interface.startswith("GigabitEthernet"):
    print("Cisco GigabitEthernet interface")
```

---

# 🔢 8. Numbers

```python
vlan_id = 10
port_count = 24
cpu_usage = 72.5
```

Operations:

```python
free_ports = 24 - 7
print(free_ports)
```

---

# ✅ 9. Boolean Values

Boolean = `True` or `False`.

Perfect for network decisions.

```python
interface_up = True
ssh_enabled = False
```

```python
if interface_up:
    print("Interface is operational")
```

---

# 📚 10. Lists

A list stores multiple values.

```python
switches = ["SW1", "SW2", "SW3"]
```

Loop through them:

```python
for switch in switches:
    print(switch)
```

Output:

```text
SW1
SW2
SW3
```

### 🌐 Networking Example

```python
vlans = [10, 20, 30, 40]

for vlan in vlans:
    print(f"Checking VLAN {vlan}")
```

---

# 🗂️ 11. Dictionaries — The Network Engineer's Best Friend

Dictionaries store **key → value** pairs.

```python
device = {
    "hostname": "SW1",
    "ip": "192.168.10.10",
    "vendor": "Cisco",
    "role": "access"
}
```

Access values:

```python
print(device["hostname"])
print(device["ip"])
```

This is extremely useful because network data often looks like structured records.

```text
Device
 ├── hostname
 ├── ip
 ├── vendor
 ├── role
 └── location
```

---

# 🧱 12. List of Dictionaries = Device Inventory

Now we are getting serious.

```python
devices = [
    {
        "hostname": "SW1",
        "ip": "192.168.10.10",
        "role": "access"
    },
    {
        "hostname": "SW2",
        "ip": "192.168.10.11",
        "role": "distribution"
    }
]
```

Loop:

```python
for device in devices:
    print(device["hostname"], device["ip"])
```

This structure appears everywhere in automation.

---

# 🔀 13. Conditions — Make Decisions

```python
status = "up"

if status == "up":
    print("Interface is healthy")
else:
    print("Investigate interface")
```

Multiple conditions:

```python
if cpu < 60:
    print("Healthy")
elif cpu < 85:
    print("Warning")
else:
    print("Critical")
```

### 🚨 Network Monitoring Logic

```text
CPU < 60%      🟢 Healthy
60–85%         🟡 Warning
> 85%          🔴 Critical
```

---

# 🔁 14. Loops — Automation Starts Here

## `for` loop

```python
interfaces = ["Gi0/1", "Gi0/2", "Gi0/3"]

for interface in interfaces:
    print(f"Checking {interface}")
```

## `while` loop

```python
attempt = 1

while attempt <= 3:
    print(f"Attempt {attempt}")
    attempt += 1
```

### ⚠️ Be careful with `while` loops

An incorrect condition can create an infinite loop.

---

# 🧩 15. Functions

Functions turn repeated logic into reusable tools.

```python
def check_device(hostname):
    print(f"Checking {hostname}")

check_device("SW1")
check_device("SW2")
```

With a return value:

```python
def is_healthy(cpu):
    return cpu < 80

print(is_healthy(45))
```

### 🧠 Automation mindset

Instead of writing:

```text
100 lines
100 lines
100 lines
```

write:

```text
function()
   ↓
reuse it 100 times
```

---

# 📦 16. Modules and Imports

Python has a huge standard library.

```python
import json
import ipaddress
```

Use a function:

```python
network = ipaddress.ip_network("192.168.10.0/24")
print(network.num_addresses)
```

You can also import specific functions:

```python
from ipaddress import ip_address
```

---

# 🛡️ 17. Exception Handling

Real networks fail.

Devices can be:

- offline ❌
- unreachable ❌
- misconfigured ❌
- overloaded ⚠️
- slow 🐢
- authentication-failed 🔐

Your automation must handle failure.

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
```

### Network-style example

```python
try:
    print(device["management_ip"])
except KeyError:
    print("Management IP is missing")
```

### 🚫 Don't do this blindly

```python
try:
    do_something()
except:
    pass
```

That can hide important failures.

Prefer specific exceptions where possible.

---

# 📁 18. File Handling

Automation often needs to read inventories and save results.

Read:

```python
with open("devices.txt", "r", encoding="utf-8") as file:
    data = file.read()

print(data)
```

Write:

```python
with open("results.txt", "w", encoding="utf-8") as file:
    file.write("SW1 is reachable\n")
```

### ⭐ Why `with`?

It automatically manages the file resource.

---

# 📊 19. CSV — Device Inventory Files

Example `devices.csv`:

```csv
hostname,ip,role
SW1,192.168.10.10,access
SW2,192.168.10.11,distribution
R1,192.168.10.1,router
```

Python:

```python
import csv

with open("devices.csv", newline="", encoding="utf-8") as file:
    reader = csv.DictReader(file)

    for device in reader:
        print(device["hostname"], device["ip"])
```

Now your script can process an entire inventory.

---

# 🧾 20. JSON — The Automation Language of the Web

You learned JSON in Module 40.

Now Python can manipulate it.

```python
import json

raw = '{"hostname": "SW1", "ip": "192.168.10.10"}'
device = json.loads(raw)

print(device["hostname"])
```

### Python → JSON

```python
device = {
    "hostname": "SW1",
    "ip": "192.168.10.10"
}

json_data = json.dumps(device, indent=2)
print(json_data)
```

### JSON file

```python
with open("device.json", "r", encoding="utf-8") as file:
    device = json.load(file)
```

---

# 🌐 21. Calling a REST API with `requests`

Install:

```bash
python -m pip install requests
```

Simple GET request:

```python
import requests

response = requests.get(
    "https://example.com",
    timeout=10
)

print(response.status_code)
print(response.text)
```

### 🧠 The flow

```text
Python
  │
  │ GET /resource
  ↓
API Server
  │
  │ HTTP 200 + JSON
  ↓
Python
  │
  ↓
Parse / Validate / Store / Act
```

---

# 🧪 22. API Response Validation

Never assume a request succeeded.

```python
import requests

response = requests.get(
    "https://example.com",
    timeout=10
)

if response.ok:
    print("Request succeeded")
else:
    print(f"Request failed: {response.status_code}")
```

You can explicitly handle status codes:

```python
if response.status_code == 200:
    print("Success")
elif response.status_code == 401:
    print("Authentication required")
elif response.status_code == 403:
    print("Access denied")
elif response.status_code == 404:
    print("Resource not found")
else:
    print("Unexpected response")
```

---

# 🔐 23. Never Hardcode Secrets

❌ Avoid:

```python
username = "admin"
password = "MyRealPassword123"
```

Especially never commit real credentials to GitHub.

Better:

```python
import os

username = os.getenv("NETWORK_USERNAME")
password = os.getenv("NETWORK_PASSWORD")
```

Example environment variables:

```text
NETWORK_USERNAME=automation_user
NETWORK_PASSWORD=<secret>
```

### 🛡️ Git rule

Before committing:

```text
Search for:
password
secret
api_key
token
private_key
```

Then confirm no real credentials are present.

---

# 🌍 24. IP Address Automation with `ipaddress`

Python's standard library includes an excellent IP-address module.

```python
import ipaddress

ip = ipaddress.ip_address("192.168.1.10")
print(ip)
```

Check whether an address belongs to a network:

```python
network = ipaddress.ip_network("192.168.1.0/24")
ip = ipaddress.ip_address("192.168.1.50")

print(ip in network)
```

Calculate hosts:

```python
network = ipaddress.ip_network("192.168.10.0/24")

for host in network.hosts():
    print(host)
```

### ⚡ This is powerful for automation

Instead of manually calculating every subnet:

```text
Python
 ↓
Subnet
 ↓
Hosts
 ↓
Validation
 ↓
Inventory
```

---

# 🧮 25. Subnet Automation Mini Tool

```python
import ipaddress

cidr = input("Enter network (example 192.168.10.0/24): ")

try:
    network = ipaddress.ip_network(cidr, strict=False)

    print(f"Network: {network.network_address}")
    print(f"Broadcast: {network.broadcast_address}")
    print(f"Prefix: /{network.prefixlen}")
    print(f"Total addresses: {network.num_addresses}")

except ValueError:
    print("Invalid network")
```

🎯 **Practice:** Modify it to display the first and last usable host.

---

# 🧹 26. Working with Network CLI Output

Cisco CLI output is usually plain text.

Example:

```text
Interface              Status       Protocol
Gi0/1                   up           up
Gi0/2                   down         down
Gi0/3                   up           up
```

A basic approach:

```python
output = """
Gi0/1 up up
Gi0/2 down down
Gi0/3 up up
"""

for line in output.strip().splitlines():
    parts = line.split()
    print(parts)
```

### ⚠️ Important

Real CLI parsing can be complicated because spacing and output formats vary.

For serious automation, prefer structured APIs when available. When CLI parsing is necessary, use reliable parsing libraries or carefully tested parsers.

---

# 🔎 27. Regular Expressions — Powerful, but Don't Overuse Them

Python provides `re`.

```python
import re

text = "Interface Gi0/1 has IP 192.168.10.1"

match = re.search(r"\b(?:\d{1,3}\.){3}\d{1,3}\b", text)

if match:
    print(match.group())
```

### 🧠 Rule

Use regex when you need pattern matching.

Do not use a giant regex when structured data or a proper parser is available.

---

# 🔌 28. SSH Automation — The Next Level

Traditional network administration often uses SSH:

```text
Python
  │
  │ SSH
  ↓
Router / Switch
  │
  ├── show version
  ├── show ip interface brief
  └── show vlan brief
```

Python libraries commonly used include:

- **Netmiko** — network-device CLI automation
- **Paramiko** — SSH implementation/library

Install Netmiko:

```bash
python -m pip install netmiko
```

> ⚠️ Use these against your own lab devices or systems you are explicitly authorized to administer.

---

# 🖥️ 29. Netmiko — Conceptual Workflow

A typical workflow looks like:

```python
from netmiko import ConnectHandler

switch = {
    "device_type": "cisco_ios",
    "host": "192.168.10.10",
    "username": "automation_user",
    "password": "<use-secure-secret>",
}

connection = ConnectHandler(**switch)

output = connection.send_command("show ip interface brief")
print(output)

connection.disconnect()
```

### 🔐 Production improvement

Credentials should come from a secure secret store or environment variables, not the source code.

---

# 🧯 30. Safe Automation Principles

Before allowing a script to change configuration:

```text
1️⃣ Understand the change
2️⃣ Validate input
3️⃣ Test in a lab
4️⃣ Back up configuration
5️⃣ Use least privilege
6️⃣ Make small changes
7️⃣ Verify result
8️⃣ Log what happened
9️⃣ Have rollback ready
```

### 🚨 Golden Rule

> **Read-only automation first. Write automation later.**

Start with:

```text
show commands
 ↓
collect data
 ↓
parse data
 ↓
report
```

Then move toward:

```text
configuration changes
```

---

# 🧰 31. Mini Project 1 — Device Inventory Printer

```python
devices = [
    {"hostname": "SW1", "ip": "192.168.10.10", "role": "access"},
    {"hostname": "SW2", "ip": "192.168.10.11", "role": "distribution"},
    {"hostname": "R1", "ip": "192.168.10.1", "role": "router"},
]

for device in devices:
    print(
        f"{device['hostname']:6} "
        f"{device['ip']:16} "
        f"{device['role']}"
    )
```

### 🎯 Challenge

Add:

- location
- vendor
- management protocol
- environment

---

# 🧪 32. Mini Project 2 — IP Validator

```python
import ipaddress

address = input("Enter an IP address: ")

try:
    ip = ipaddress.ip_address(address)
    print(f"Valid IP: {ip}")
    print(f"Version: IPv{ip.version}")
except ValueError:
    print("Invalid IP address")
```

### 🔥 Upgrade

Ask the user for a subnet and determine whether the IP belongs to it.

---

# 🧪 33. Mini Project 3 — JSON Device Inventory

Create `inventory.json`:

```json
{
  "devices": [
    {
      "hostname": "SW1",
      "ip": "192.168.10.10",
      "role": "access"
    },
    {
      "hostname": "R1",
      "ip": "192.168.10.1",
      "role": "router"
    }
  ]
}
```

Python:

```python
import json

with open("inventory.json", encoding="utf-8") as file:
    inventory = json.load(file)

for device in inventory["devices"]:
    print(f"{device['hostname']} → {device['ip']}")
```

---

# 🌐 34. Mini Project 4 — API Data Collector

```python
import requests

url = "https://example.com/api/devices"

try:
    response = requests.get(url, timeout=10)
    response.raise_for_status()

    data = response.json()
    print(data)

except requests.exceptions.Timeout:
    print("The API request timed out")
except requests.exceptions.HTTPError as error:
    print(f"HTTP error: {error}")
except requests.exceptions.RequestException as error:
    print(f"Request failed: {error}")
```

### 🧠 Notice the mindset

Not:

> “Send request and hope.”

But:

> “Send → validate → handle failure → parse → act.”

---

# 🔐 35. Mini Project 5 — Secure API Authentication Pattern

```python
import os
import requests

api_token = os.getenv("API_TOKEN")

if not api_token:
    raise RuntimeError("API_TOKEN is not configured")

headers = {
    "Authorization": f"Bearer {api_token}",
    "Accept": "application/json",
}

response = requests.get(
    "https://example.com/api/devices",
    headers=headers,
    timeout=10,
)

response.raise_for_status()
print(response.json())
```

### 🛡️ Security checklist

- Never print the token
- Never commit the token
- Never paste secrets into logs
- Use least privilege
- Rotate credentials
- Prefer a secret manager in production

---

# 🏗️ 36. Mini Project 6 — Generate Cisco Configuration

Suppose you need the same VLAN configuration on multiple lab switches.

```python
vlan_id = 20
vlan_name = "USERS"

config = [
    f"vlan {vlan_id}",
    f" name {vlan_name}",
]

print("\n".join(config))
```

Output:

```text
vlan 20
 name USERS
```

### 🚀 Scale it

```python
vlans = [
    (10, "MGMT"),
    (20, "USERS"),
    (30, "SERVERS"),
]

for vlan_id, vlan_name in vlans:
    print(f"vlan {vlan_id}")
    print(f" name {vlan_name}")
```

This is **configuration generation**.

The next step would be safely sending that configuration to authorized lab devices.

---

# 🧪 37. Lab 1 — Python Networking Basics

### Objective

Build a script that asks for:

- hostname
- management IP
- device role

Then prints a formatted summary.

### Expected

```text
================================
DEVICE INFORMATION
================================
Hostname : SW1
IP       : 192.168.10.10
Role     : Access Switch
================================
```

### ⭐ Bonus

Validate the IP using `ipaddress`.

---

# 🧪 38. Lab 2 — Multi-Device Inventory

Create an inventory of at least 5 lab devices.

Your script should:

- print all devices
- count routers
- count switches
- identify missing IP addresses
- display device roles

### Challenge

Add a health field:

```text
SW1 → 🟢 UP
SW2 → 🔴 DOWN
R1  → 🟢 UP
```

---

# 🧪 39. Lab 3 — Subnet Intelligence Tool

Input:

```text
10.10.20.0/27
```

Output:

```text
Network:     10.10.20.0
Broadcast:   10.10.20.31
Prefix:      /27
Addresses:   32
```

### Boss upgrade 👑

Allow the user to enter an IP such as:

```text
10.10.20.14
```

Then determine whether it belongs to the selected network.

---

# 🧪 40. Lab 4 — CSV → JSON Converter

Input:

```csv
hostname,ip,role
SW1,192.168.1.10,access
SW2,192.168.1.11,access
R1,192.168.1.1,router
```

Python should create:

```json
{
  "devices": [
    {
      "hostname": "SW1",
      "ip": "192.168.1.10",
      "role": "access"
    }
  ]
}
```

### Skills trained

`CSV → Python objects → JSON → file output`

---

# 🧪 41. Lab 5 — API Health Checker

Build a tool that checks a list of API endpoints.

For every endpoint display:

```text
URL                  STATUS
--------------------------------
API-1                🟢 200
API-2                🟡 401
API-3                🔴 TIMEOUT
```

### Requirements

- timeout handling
- HTTP status handling
- readable output
- no secrets in source code

---

# 🧪 42. Lab 6 — Authorized Lab Device Collector

Use your own Packet Tracer/EVE-NG/GNS3/physical lab environment.

Build a read-only collector that gathers:

```text
show version
show ip interface brief
show vlan brief
```

Save each result to a file such as:

```text
outputs/
 ├── SW1-show-version.txt
 ├── SW1-show-ip-interface-brief.txt
 └── SW1-show-vlan-brief.txt
```

### 🛡️ Safety rule

Start with **show commands only**.

Do not test configuration-changing automation on networks you do not own or administer.

---

# 🧪 43. Lab 7 — Multi-Device Read-Only Collector

Now combine:

```text
CSV inventory
      ↓
Python
      ↓
SSH connection
      ↓
show commands
      ↓
output files
      ↓
summary report
```

Pseudo-workflow:

```python
for device in inventory:
    connect(device)
    output = collect_information(device)
    save_output(device, output)
    disconnect(device)
```

### 🚀 Enterprise mindset

One script should work for 5, 50, or 500 devices with minimal changes.

---

# 🧪 44. Lab 8 — Network Health Report

Build a final report containing:

```text
NETWORK HEALTH REPORT
=====================
Total Devices : 10
Reachable     : 9
Unreachable   : 1

CPU Alerts    : 2
Interface Down: 4
Config Errors : 1

Overall Health: WARNING
```

### ⭐ Final upgrade

Export the report as JSON.

Then create a simple Markdown report from the same data.

---

# 🏢 45. Enterprise Scenario — NH Technologies

Imagine a company called **NH Technologies**.

It has:

```text
                    🌐 Internet
                        │
                  ┌─────┴─────┐
                  │   EDGE    │
                  └─────┬─────┘
                        │
                  🧠 CORE ROUTER
                  /      |      \
                 /       |       \
              SW1       SW2      SW3
             / | \     / | \    / | \
           PCs Servers APs ...  Printers
```

There are 150 network devices.

The network team needs a morning report.

Manual process:

```text
2–3 hours 😩
```

Automated process:

```text
Python
  ↓
Inventory
  ↓
Collect data
  ↓
Validate
  ↓
Parse
  ↓
Generate report
  ↓
📊 Morning dashboard
```

### What would you automate first?

1. Inventory validation
2. Read-only health checks
3. Configuration backups
4. Interface status collection
5. VLAN inventory
6. Device version inventory
7. API-based monitoring

Then gradually introduce controlled configuration automation.

---

# 🔄 46. The Complete Automation Pipeline

Memorize this architecture:

```text
              📋 INVENTORY
                   ↓
             🧠 PYTHON LOGIC
                   ↓
       ┌───────────┼───────────┐
       ↓           ↓           ↓
     SSH          API         Files
       ↓           ↓           ↓
   Network       Controller   Data
   Devices
       \           |           /
        \          |          /
         └───────┬─┘
                 ↓
             🔍 PARSE
                 ↓
             ✅ VALIDATE
                 ↓
             📊 REPORT
                 ↓
             📝 LOG
                 ↓
             🔐 AUDIT
```

---

# 🛡️ 47. Python + Cybersecurity

This is where your networking and cybersecurity paths connect.

Python can help with defensive tasks such as:

- log processing
- IOC extraction
- IP validation
- DNS/API lookups
- alert enrichment
- asset inventory
- configuration auditing
- firewall-rule analysis
- SIEM API integration
- report generation

Example:

```text
SIEM Alert
    ↓
Python
    ↓
Extract source IP
    ↓
Validate / normalize IP
    ↓
Query approved threat-intelligence API
    ↓
Enrich alert
    ↓
Return context to analyst
```

### 🔥 Networking knowledge makes the automation better

Because you understand:

```text
IP → subnet → gateway → routing → ACL → NAT → DNS → TCP/UDP
```

you can interpret security telemetry much more effectively.

---

# 🚨 48. Automation Failure Modes

Automation can fail in many ways.

### 1️⃣ Authentication failure

```text
401 / SSH authentication error
```

### 2️⃣ Authorization failure

```text
403 / insufficient privilege
```

### 3️⃣ Network failure

```text
Timeout / unreachable
```

### 4️⃣ Data failure

```text
Malformed JSON
Missing key
Unexpected CLI output
```

### 5️⃣ Logic failure

```text
Script itself made the wrong decision
```

### 6️⃣ Dependency failure

```text
Missing Python package
```

### 7️⃣ Rate limiting

```text
Too many API requests
```

### 8️⃣ Configuration failure

```text
Automation changed something incorrectly
```

This is why automation requires **engineering discipline**, not just scripting.

---

# 🧰 49. Troubleshooting Ladder

When your Python automation fails, don't randomly change code.

Use this ladder:

```text
1. Is Python working?
        ↓
2. Is the package installed?
        ↓
3. Is the input valid?
        ↓
4. Is DNS working?
        ↓
5. Is the device/API reachable?
        ↓
6. Is authentication valid?
        ↓
7. Is authorization sufficient?
        ↓
8. Is the request correct?
        ↓
9. Is the response expected?
        ↓
10. Is the parsing logic correct?
        ↓
11. Did the automation perform the intended action?
```

---

# 🐞 50. Common Python Automation Mistakes

## ❌ Mistake 1 — Hardcoded credentials

Use environment variables or a secret manager.

## ❌ Mistake 2 — No timeout

Network requests can hang.

```python
requests.get(url, timeout=10)
```

## ❌ Mistake 3 — No error handling

Always plan for failure.

## ❌ Mistake 4 — No logging

You need to know what happened.

## ❌ Mistake 5 — Blind configuration changes

Test first.

## ❌ Mistake 6 — Ignoring return values

A function may fail.

## ❌ Mistake 7 — Parsing fragile text without tests

CLI formats can change.

## ❌ Mistake 8 — Huge scripts

Break the project into functions/modules.

## ❌ Mistake 9 — No documentation

Future-you is another engineer.

## ❌ Mistake 10 — No Git history

Commit meaningful changes.

---

# 📝 51. Logging — Give Your Automation a Memory

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s"
)

logging.info("Starting inventory check")
logging.warning("SW2 is unreachable")
```

### Log levels

```text
DEBUG     🔍 Detailed debugging
INFO      ℹ️ Normal operation
WARNING   ⚠️ Something unexpected
ERROR     ❌ Operation failed
CRITICAL  🚨 Serious failure
```

### 🛡️ Security warning

Never log passwords, tokens, private keys, or sensitive session data.

---

# 🧱 52. Build Small Functions, Not Giant Scripts

Bad architecture:

```text
main.py
 └── 900 lines 😵
```

Better:

```text
network_automation/
 ├── main.py
 ├── inventory.py
 ├── connections.py
 ├── validators.py
 ├── parsers.py
 ├── reports.py
 ├── config.py
 └── tests/
```

### Example function responsibilities

```text
load_inventory()
connect_device()
collect_facts()
parse_output()
validate_data()
generate_report()
```

One function → one clear responsibility.

---

# 🧪 53. Testing Mindset

Before changing a network, test the logic.

Example:

```python
def is_valid_vlan(vlan):
    return 1 <= vlan <= 4094

assert is_valid_vlan(10)
assert not is_valid_vlan(5000)
```

### 🧠 Why testing matters

```text
Small bug
   ↓
Wrong config generated
   ↓
100 devices
   ↓
🚨 BIG OUTAGE
```

Testing reduces blast radius.

---

# 🎯 54. Idempotency

A powerful automation concept.

An idempotent operation can be run repeatedly while keeping the desired final state consistent.

Example goal:

```text
VLAN 20 must exist
```

Good automation:

```text
Already exists → do nothing
Missing → create it
```

Bad automation:

```text
Run script
→ blindly recreate/change everything
```

### 🔥 Desired state

```text
Current State ──────→ Automation ──────→ Desired State
```

---

# 🔐 55. Least Privilege

Automation accounts should have only the permissions they require.

For a read-only inventory collector:

```text
❌ Full administrative privileges

✅ Read-only / minimum required privileges
```

This limits damage if credentials or the automation host are compromised.

---

# 📦 56. Dependencies and `requirements.txt`

If your project uses external libraries:

```text
requests
netmiko
```

A `requirements.txt` can record them:

```text
requests
netmiko
```

Install:

```bash
python -m pip install -r requirements.txt
```

For production projects, pin versions after testing to improve reproducibility.

---

# 🌳 57. Git + Python Automation

Your GitHub workflow should look like:

```text
Write code
   ↓
Test locally
   ↓
Run lab tests
   ↓
Review diff
   ↓
Remove secrets
   ↓
Commit
   ↓
Push
```

Example:

```bash
git status
git diff
git add .
git commit -m "Add network health checker"
git push
```

### 🛡️ Before push

```text
☐ No passwords
☐ No API keys
☐ No private keys
☐ No personal tokens
☐ No production IPs if sensitive
☐ No unnecessary output logs
```

---

# 🧠 58. Mental Model — Think Like an Automation Engineer

When given a repetitive task, ask:

### 1. What is the input?

```text
Inventory / API / CLI / CSV / JSON
```

### 2. What is the desired state?

```text
What should be true?
```

### 3. What decision is required?

```text
if / else
```

### 4. What repeats?

```text
for device in devices
```

### 5. What can fail?

```text
try / except
```

### 6. What should be logged?

```text
success / warning / failure
```

### 7. How do I verify?

```text
before → change → after
```

### 8. How do I roll back?

Always ask this before production changes.

---

# 🏆 59. Scenario-Based Challenge — The 50-Switch Morning Problem

You join NH Technologies as a junior network engineer.

Every morning, an engineer manually logs into 50 switches and records:

- hostname
- management IP
- software version
- interface status
- VLAN count
- uptime

It takes 90 minutes.

### Your mission 🐍

Design an automation solution.

### Level 1 🟢
Create a CSV inventory.

### Level 2 🟡
Write Python to load the inventory.

### Level 3 🟠
Validate every IP.

### Level 4 🔵
Connect to authorized lab devices.

### Level 5 🟣
Collect read-only information.

### Level 6 🔴
Parse the output.

### Level 7 🏆
Generate a Markdown/JSON report.

### Level 8 👑
Add logging and failure handling.

### Level 9 🚀
Expose the report through an API/dashboard.

### Level 10 🧠
Run it automatically on a schedule with appropriate security controls.

---

# 🎮 60. Learner Progression System

Don't jump straight to advanced automation.

```text
🥉 LEVEL 1
Python syntax

🥈 LEVEL 2
Files + CSV + JSON

🥇 LEVEL 3
IP automation

💎 LEVEL 4
REST APIs

🔥 LEVEL 5
SSH automation

⚡ LEVEL 6
Multi-device automation

🧠 LEVEL 7
Testing + logging + error handling

🏢 LEVEL 8
Enterprise architecture

🛡️ LEVEL 9
Secure automation

👑 LEVEL 10
Automation platform thinking
```

---

# 🎤 61. Interview Questions

### Q1. Why is Python widely used in network automation?

Because it is readable, has strong networking/API libraries, supports rapid development, and can automate repetitive tasks at scale.

### Q2. What is a dictionary?

A key-value data structure useful for representing structured network information.

### Q3. Why use `try/except`?

To handle expected runtime failures without crashing the entire automation workflow.

### Q4. What is JSON?

A lightweight structured data format widely used by APIs and automation systems.

### Q5. What does `requests` do?

It provides a convenient Python interface for making HTTP requests.

### Q6. Why should requests have timeouts?

Without a timeout, a network operation may wait indefinitely.

### Q7. What is Netmiko?

A Python library designed to simplify CLI-based connections and automation across network devices.

### Q8. Netmiko vs Paramiko?

Paramiko provides SSH functionality; Netmiko builds network-device-oriented automation workflows on top of SSH-related capabilities and supports many network platforms.

### Q9. Why should credentials not be hardcoded?

They can leak through source code, Git history, logs, backups, or shared repositories.

### Q10. What is idempotency?

The property that repeatedly applying automation results in the same intended final state rather than causing unnecessary changes.

### Q11. Why start with read-only automation?

It reduces risk while you learn connection handling, parsing, error handling, and verification.

### Q12. What is the automation pipeline?

A common pipeline is inventory → connect → collect → parse → validate → report/log → verify.

### Q13. What is the purpose of `ipaddress`?

It provides standard-library tools for manipulating and validating IPv4/IPv6 addresses and networks.

### Q14. What is configuration drift?

The difference between the intended configuration and the actual configuration over time.

### Q15. Why is logging important?

It creates an operational record that helps debugging, auditing, monitoring, and incident investigation.

---

# ⚡ 62. Quick Revision

```text
🐍 Python
  ↓
Variables → Lists → Dictionaries
  ↓
Conditions → Loops → Functions
  ↓
Exceptions → Files → CSV → JSON
  ↓
IP Automation → APIs
  ↓
SSH → Netmiko / Paramiko
  ↓
Inventory → Collect → Parse → Validate
  ↓
Logs → Reports → Verification
  ↓
Secure Enterprise Automation
```

---

# 📋 63. Cheat Sheet

| Need | Python Tool |
|---|---|
| Text | `str` |
| Number | `int`, `float` |
| True/False | `bool` |
| Multiple values | `list` |
| Key/value data | `dict` |
| Decisions | `if` |
| Repetition | `for`, `while` |
| Reusable logic | `def` |
| Errors | `try/except` |
| JSON | `json` |
| CSV | `csv` |
| IP/subnet | `ipaddress` |
| HTTP/API | `requests` |
| SSH | Netmiko / Paramiko |
| Logs | `logging` |
| Environment variables | `os` |
| Regex | `re` |
| Dependencies | `pip` / `requirements.txt` |

---

# 🧠 64. Final Mental Model

Remember this picture:

```text
                  🧑‍💻 YOU
                    │
                    ↓
             🐍 PYTHON ENGINE
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
      REST         SSH         FILES
        ↓           ↓           ↓
       API       NETWORK       DATA
        │         DEVICES       │
        └───────────┼───────────┘
                    ↓
                 🧠 LOGIC
                    ↓
             🔍 VALIDATION
                    ↓
              📊 REPORTING
                    ↓
                📝 LOGGING
                    ↓
                 🔐 AUDIT
```

Python is not the goal.

**Automation is the goal.**

The real skill is learning to translate:

> **Networking problem → repeatable logic → safe automation → verified result.**

---

# 🚀 65. Final Challenge — Build Your First Network Automation Portfolio Project

Create a project named:

```text
network-health-automation
```

Suggested structure:

```text
network-health-automation/
│
├── README.md
├── requirements.txt
├── .gitignore
├── inventory/
│   └── devices.example.csv
│
├── src/
│   ├── main.py
│   ├── inventory.py
│   ├── validators.py
│   ├── connections.py
│   ├── collectors.py
│   ├── parsers.py
│   └── reports.py
│
├── outputs/
│   └── .gitkeep
│
└── tests/
    └── test_validators.py
```

### Portfolio requirements

Your project should demonstrate:

- 🐍 Python
- 🌐 Networking knowledge
- 🔌 API/SSH concepts
- 🧾 JSON/CSV
- 🛡️ Secure credential handling
- 🧯 Error handling
- 📝 Logging
- 🧪 Testing
- 📊 Reporting
- 🌳 Git/GitHub discipline

### 💼 Why this matters

A recruiter can see more than:

> “I learned Python.”

They can see:

> **“I used Python to solve a networking problem.”**

That is a much stronger portfolio signal.

---

# 🏁 Module Completion Checklist

- [ ] I understand Python variables
- [ ] I can use lists and dictionaries
- [ ] I can write conditions
- [ ] I can write loops
- [ ] I can create functions
- [ ] I can handle exceptions
- [ ] I can read/write files
- [ ] I can process CSV
- [ ] I can parse JSON
- [ ] I can call REST APIs
- [ ] I understand API errors
- [ ] I can validate IP addresses
- [ ] I understand SSH automation
- [ ] I understand Netmiko and Paramiko
- [ ] I can create a read-only automation workflow
- [ ] I understand logging
- [ ] I understand idempotency
- [ ] I can keep credentials out of source code
- [ ] I can design a multi-device automation project
- [ ] I can explain Python network automation in an interview

---

# 🧭 What You Should Be Able to Say After This Module

> **“I can use Python to process network inventories, validate IP addressing, work with JSON/CSV, consume REST APIs, connect to authorized network devices through SSH-oriented libraries, collect and parse operational data, handle failures, generate reports, and build safer repeatable automation workflows.”**

That is the bridge from **CCNA learner → automation-capable network engineer → cybersecurity automation engineer.** 🐍🌐🛡️

---

## 🔥 Learner Challenge

Don't just read this module.

**Build something. Break it. Fix it. Improve it. Commit it.**

```text
READ 📖
  ↓
TYPE ⌨️
  ↓
RUN ▶️
  ↓
BREAK 💥
  ↓
DEBUG 🐞
  ↓
UNDERSTAND 🧠
  ↓
AUTOMATE 🐍
  ↓
DOCUMENT 📝
  ↓
PUBLISH 🌐
```

> 🚀 **The moment you stop copying scripts and start designing them, you become an automation engineer.**

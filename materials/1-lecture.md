<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Introduction to Network Automation. From CLI Artisan to Network Reliability Engineering.
-->

# Session 1: Introduction to Network Automation

## Welcome to the Bootcamp

    --{{0}}--
    Hello everyone, and welcome to the Python Network Automation Bootcamp.
    I'm Alex Rivera, but most people just call me "The Automator".
    I've successfully automated myself out of a job three times, and that's exactly what I want for you.
    We are not here to write scripts. We are here to build systems.

    --{{1}}--
    We're going to move from being "CLI Artisans"—hand-crafting configs like they're fine pottery—to becoming **Network Reliability Engineers**.
    Our goal is simple: Sleep better at night because the network manages itself.

    {{1}}
> **"If you do it more than twice, automate it—but test it first."**

    --{{2}}--
    Here is our roadmap. We start today with the foundation.
    Then we build our Source of Truth, learn the language of APIs, and finally build a pipeline that does the work for us.

    {{2}}
### The Roadmap
1. **Foundation** (Today)
2. **Source of Truth** (NetBox)
3. **APIs & Data** (REST, GraphQL)
4. **Templating** (Jinja2)
5. **Testing** (Batfish, pyATS)
6. **Delivery** (Ansible, CI/CD)

## The Problem: The "Artisan" Network

    --{{0}}--
    Let's talk about the elephant in the room. SSH.
    We all love the CLI. It feels powerful. But it's also our biggest weakness.

    --{{1}}--
    Picture this: Friday, 4:47 PM. You're 13 minutes from freedom.
    Your OSPF neighbor just flapped. You SSH into the router, run `show ip ospf neighbor`, and your stomach drops.
    Last week's subnet migration? You fat-fingered the network mask. On 43 interfaces. Across 12 routers.

    --{{2}}--
    That's **516 commands** you need to fix. By hand. One interface at a time.
    Your weekend plans just became "configure terminal" 516 times.
    And you know what the worst part is? You'll probably make another typo while fixing it.

    {{2}}
> **The Reality**: One mistake × 12 routers = Career-limiting event

    --{{3}}--
    When you configure a router by hand, you are the single point of failure.
    Did you capitalize that description? Did you check the MTU on *both* sides?
    Did you document it? (I know you didn't).

    {{3}}
### Why Humans Scale Poorly
*   **Inconsistency**: "Snowflake" configurations. Every device is unique, but not in a good way.
*   **Documentation Drift**: The diagram says VLAN 10, the router says VLAN 20, the ticket says VLAN 30.
*   **Fear**: "Don't touch that switch, we don't know what it does." (It's been running since 2011)
*   **Speed**: Typing is slow. Copy-pasting is dangerous. Scripting in Notepad is terrifying.
*   **Error Rate**: Humans make mistakes. We make MORE mistakes when we're tired, rushed, or it's Friday at 4:47 PM.

    --{{4}}--
    The result? Configuration Drift. The entropy of the network increases until it collapses.

## The Math: Why Automation Wins

    --{{0}}--
    Let's do the math. Because at some point, your manager will ask: "Is this automation thing worth it?"
    The answer is yes, and here's why.

    --{{1}}--
    **Manual Configuration:**
    - Time per device: ~5 minutes (if you're fast and don't make mistakes)
    - 100 devices = 5 min × 100 = **500 minutes** = **8.3 hours**
    - Plus: The stress of doing it perfectly, 100 times in a row

    --{{2}}--
    **Automated Configuration:**
    - Time to write playbook: ~2 hours (one time investment)
    - Time to run on 100 devices: ~5 minutes (parallel execution)
    - Total time: **2 hours, 5 minutes**
    - Plus: You can run it at 2 AM from your couch in your pajamas

    --{{3}}--
    **The Savings:** 6+ hours on the first run. Every subsequent run? Nearly instant.
    And here's the kicker: the playbook is reusable. Next month, when you have 200 devices? Same 2 hours of work.

    {{3}}
> **ROI Reality Check**: Your first automation project pays for itself the moment you run it the second time.

## The Solution: Infrastructure as Code (IaC)

    --{{0}}--
    The software world solved this years ago. They treat servers like cattle, not pets.
    We need to do the same for our excessive number of pets (Routers).

    --{{1}}--
    **Infrastructure as Code** means your network state is defined in text files (Code/YAML), not in the NVRAM of a device.
    The device is just a projection of that code.

    --{{2}}--
    Let me show you what this looks like in practice.

    {{2}}
### Before: The Old Way (Imperative)
```bash
# You type this 100 times, hoping you don't typo
configure terminal
interface GigabitEthernet0/1
  description UPLINK_TO_CORE_SW01
  ip address 10.1.1.1 255.255.255.252
  no shutdown
  exit
interface GigabitEthernet0/2
  description UPLINK_TO_CORE_SW02
  ip address 10.1.1.5 255.255.255.252
  no shutdown
  exit
end
write memory
```

    {{3}}
### After: The New Way (Declarative)
```yaml
# You write this ONCE, run it on 100 devices
- name: Configure uplinks
  cisco.ios.l3_interfaces:
    config:
      - name: GigabitEthernet0/1
        ipv4:
          - address: 10.1.1.1/30
      - name: GigabitEthernet0/2
        ipv4:
          - address: 10.1.1.5/30
    state: merged
```

    --{{4}}--
    See the difference? The first one tells the device *how* to do it, step by step.
    The second one tells the device *what* you want, and lets it figure out the how.
    If the interface is already configured correctly? It does nothing. That's idempotency.

    {{4}}
### The Shift
| Traditional | Network Automation |
| :--- | :--- |
| **State**: On the Device | **State**: In Git / NetBox |
| **Change**: SSH + Typing | **Change**: `git push` |
| **Validation**: Ping & Pray | **Validation**: Automated Tests |
| **Documentation**: Excel (maybe) | **Documentation**: The Code *is* the Doc |
| **Speed**: 5 min/device | **Speed**: 5 min/100 devices |

## Core Principles

    --{{0}}--
    Before we touch a single line of Python, you need to understand these three words.
    Burn them into your brain.

    --{{1}}--
    **1. Source of Truth (SoT)**.
    There can be only one. If NetBox says the IP is `.1` and the device says `.2`, the device is wrong.
    We drive the state *from* the SoT *to* the device.

    --{{2}}--
    **2. Declarative vs. Imperative**.
    *Imperative* is "Go to interface Gi0/1, type `no shut`".
    *Declarative* is "Interface Gi0/1 should be UP". I don't care *how* you do it, just make it happen.
    Ansible is great at this.

    --{{3}}--
    **3. Idempotency**.
    This is the big one.
    An operation is **idempotent** if running it multiple times produces the same result.
    If I run a script to add a VLAN, and the VLAN is already there, nothing should happen. It shouldn't add a second one, and it definitely shouldn't error out.

    --{{4}}--
    Think of it like a light switch. If the light is off and you flip the switch, it turns on.
    If the light is already on and you flip the switch again... it stays on. It doesn't get "more on" or explode.
    That's idempotency.

    {{4}}
### Idempotency in Action
```ascii
Run #1: VLAN 10 doesn't exist
  ┌─────────────┐
  │ Add VLAN 10 │ ──> ✓ VLAN 10 created
  └─────────────┘

Run #2: VLAN 10 already exists
  ┌─────────────┐
  │ Add VLAN 10 │ ──> ✓ No change (already correct)
  └─────────────┘

Run #3: VLAN 10 still exists
  ┌─────────────┐
  │ Add VLAN 10 │ ──> ✓ No change (still correct)
  └─────────────┘

Result: Safe to run 100 times. Safe to run in production.
```

    --{{5}}--
    **Why this matters:** You can run your automation playbook every day, every hour, even every minute.
    It will only make changes when the actual state doesn't match the desired state.
    This is how we sleep at night.

    {{5}}
> **Idempotency**: Run it twice, run it a thousand times. Same result. No fear.

    {{6}}
### The Anti-Pattern (What NOT to do)
```python
# BAD: Not idempotent
for vlan in [10, 20, 30]:
    device.send_command(f"vlan {vlan}")
    device.send_command(f"name DATA_VLAN_{vlan}")
# Run this twice = VLANs created twice = CHAOS

# GOOD: Idempotent
for vlan in [10, 20, 30]:
    if vlan not in device.get_vlans():
        device.send_command(f"vlan {vlan}")
        device.send_command(f"name DATA_VLAN_{vlan}")
# Run this 100 times = Same result = PEACE
```

## The Toolkit (Your BOM)

    --{{0}}--
    Here is the weapon rack for this bootcamp. We aren't using these because they are trendy.
    We are using them because they work.

    --{{1}}--
    First, the Brain. **NetBox**. It knows what your network *should* look like.

    --{{2}}--
    The Time Machine. **Git**. It tracks every change, who made it, and lets us roll back when we screw up.

    --{{3}}--
    The Hands. **Ansible**. It does the heavy lifting of talking to devices.

    --{{4}}--
    The Safety Net. **Batfish** and **pyATS**.
    Batfish tests your config *before* you push it. pyATS checks the network *after* you push it.

    {{1}}
### The Stack
*   **Source of Truth**: NetBox
*   **Version Control**: Git
*   **Configuration Mgmt**: Ansible
*   **Templating**: Jinja2
*   **Pre-Testing**: Batfish
*   **Post-Testing**: pyATS

## Wrap-up & Lab Setup

    --{{0}}--
    That's the theory. We don't do much theory here, so enjoy it while it lasts.
    Next session, we get our hands dirty with NetBox.

    --{{1}}--
    **Assignment for next time**:
    1.  Ensure you have VS Code installed.
    2.  Install the "Python" and "Remote - SSH" extensions.
    3.  Get some sleep. You'll need it.

    {{1}}
> **Next Session**: Building the Source of Truth with NetBox.

    --{{2}}--
    See you in the lab. Rivera out.

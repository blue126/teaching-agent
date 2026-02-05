<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Validation on the wire. pyATS & Genie.
-->

# Session 9: Post-Change Testing with pyATS

## Introduction: Trust, but Verify (with Code)

    --{{0}}--
    Batfish told us the Config is correct.
    But does the fiber have light? Is the neighbor actually up?
    Batfish is the Architect. pyATS is the Inspector.

    --{{1}}--
    **The old verification process:**
    - SSH to each device
    - Run `show ip bgp summary`
    - Copy output to notepad
    - Manually check neighbor state (Established?)
    - Repeat for OSPF, interfaces, VLANs, routing table
    - Repeat for 100 devices
    - **Time: 4+ hours. Accuracy: Depends on coffee levels.**

    --{{2}}--
    **The pyATS way:**
    ```python
    for device in testbed.devices:
        bgp = device.learn('bgp')
        assert all(n['state'] == 'Established' for n in bgp.neighbors)
    ```
    - **Time: 2 minutes. Accuracy: 100%.**

    {{2}}
> **pyATS**: Verification at scale. Because manually checking 100 routers is not a job, it's punishment.

    --{{3}}--
    **pyATS** (Python Automated Test System).
    Originally Cisco's internal tool, used for millions of tests monthly.
    Open-sourced in 2017. Now the industry standard for network testing.

    --{{2}}--
    **The pyATS Ecosystem:**
    - **pyATS Core**: The framework (testbed, connections, logging)
    - **Genie**: The library (parsers, models, triggers, verifications)
    - **XPRESSO**: The web dashboard (optional)

    --{{3}}--
    **Genie Parser Library:**
    - **1000+ parsers** for show commands
    - **Multi-vendor**: Cisco, Arista, Juniper, etc.
    - **Structured output**: CLI → Python dict/JSON
    - **Learn feature**: Snapshot entire device state
    - **Diff engine**: Compare before/after snapshots

    --{{4}}--
    **Supported Platforms:**
    - Cisco: IOS, IOS-XE, IOS-XR, NXOS, ASA
    - Juniper: JunOS
    - Arista: EOS (via community)
    - Linux servers
    - Extensible via plugins

## Lab 9.1: The Testbed

    --{{0}}--
    pyATS needs a `testbed.yml`. It's like the Ansible Inventory, but on steroids.

```yaml
devices:
  nyc-spine-01:
    os: nxos
    type: switch
    connections:
      cli:
        protocol: ssh
        ip: 10.1.10.11
    credentials:
      default:
        username: admin
        password: cisco
```

    --{{1}}--
    We can generate this from NetBox too! But for now, let's write it.

    {{1}}
> **Task**: Create your `testbed.yml` and verify connectivity with `pyats shell --testbed testbed.yml`.

## Lab 9.2: Genocide of Regex (Genie)

    --{{0}}--
    Writing Regex to parse `show ip interface brief` is painful.
    **Genie** does it for you.
    It has thousands of parsers built-in.

    --{{1}}--
    In Python:
```python
from genie.testbed import load
testbed = load('testbed.yml')
dev = testbed.devices['nyc-spine-01']
dev.connect()

output = dev.parse('show ip interface brief')
print(output['interface']['Eth1/1']['status'])
```

    --{{2}}--
    No regex. Just a dictionary. `status` is either `up` or `down`.
    Clean.

    --{{3}}--
    **Genie Learn & Diff** (State Comparison):
    ```python
    # Snapshot entire BGP state
    bgp_before = dev.learn('bgp')
    
    # Make configuration changes...
    
    # Snapshot again
    bgp_after = dev.learn('bgp')
    
    # Compare
    from genie.utils.diff import Diff
    diff = Diff(bgp_before.info, bgp_after.info)
    diff.findDiff()
    print(diff)  # Shows exactly what changed
    ```

    --{{4}}--
    **Supported Learn Features (20+):** `bgp`, `ospf`, `interface`, `vlan`, `arp`, `routing`, `acl`, `platform`, `vrf`, `hsrp`, `msdp`, `device`, `lldp`

    --{{5}}--
    **AEtest Framework Structure:**
    - `CommonSetup`: Connect to devices, initial state gathering
    - `Testcase`: Individual tests with `@aetest.test` decorators
    - `CommonCleanup`: Disconnect, save logs, generate reports
    - Each can have multiple `@aetest.subsection` functions

    {{1}}
> **Task**: Parse `show ip ospf neighbors` and print a warning if the state is not `FULL`.

## Lab 9.3: Learning and Diffing

    --{{0}}--
    This is the coolest feature.
    1.  Snapshot the network state **before** the change.
    2.  Make the change (Ansible).
    3.  Snapshot **after**.
    4.  Diff.

    --{{1}}--
    Command Line Magic:
    `genie learn ospf --testbed testbed.yml --output before`
    *(... run ansible assignment ...)*
    `genie learn ospf --testbed testbed.yml --output after`
    `genie diff before after`

    --{{2}}--
    It will generate a report showing exactly what changed.
    "Neighbor 10.1.1.2 went from FULL to DOWN".
    "LSA Count increased by 5".

    {{1}}
> **Task**: Perform a "Learn, Change, Learn, Diff" workflow on the OSPF state suitable for a maintenance window report.

## Wrap-up

    --{{0}}--
    We have the full stack.
    NetBox (Source), Ansible (Execution), Batfish (Pre-Test), pyATS (Post-Test).
    But running these manually is slow.

    --{{1}}--
    We need to glue them together into a pipeline that runs automatically every time we save a file.
    Next up: **Git & CI/CD**.

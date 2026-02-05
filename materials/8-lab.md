<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Validation without Packets. Batfish.
-->

# Session 8: Pre-Change Testing with Batfish

## Introduction: The Crystal Ball

    --{{0}}--
    Simulating a network has always been hard. GNS3 is heavy. CML is expensive.
    What if I told you we can model the control plane without running any virtual routers?

    --{{1}}--
    Picture this: You're about to push a new ACL to 50 routers.
    The ACL is supposed to allow traffic from subnet A to subnet B.
    You deploy it Friday at 5 PM. By 5:02 PM, your phone is ringing.
    The ACL is blocking ALL traffic. Typo in line 3. Production down.

    --{{2}}--
    **What if you could test that ACL BEFORE pushing it?**
    Not in a lab. Not on one router. Test it against your ENTIRE production topology.
    Ask questions like: "Will this ACL allow Host A to reach Host B?" "Will OSPF still converge?"
    Get answers in seconds. Before you touch production.

    {{2}}
> **Batfish**: Test in simulation, deploy with confidence.

    --{{3}}--
    **Batfish**.
    Developed at Microsoft Research, UCLA, and USC, now maintained by AWS.
    It reads your text config files (Cisco, Juniper, Arista, Palo Alto), parses them, and calculates the math of the network.
    No packets. No VMs. Pure logic.

    --{{2}}--
    **What Batfish Can Analyze:**
    - **Reachability**: Can Host A reach Host B?
    - **ACL/Firewall Rules**: What traffic does this ACL permit/deny?
    - **Routing**: What path will traffic take?
    - **BGP Sessions**: Will BGP peers establish?
    - **OSPF/ISIS**: Are neighbor relationships correct?
    - **Multipath**: Where does ECMP occur?
    - **Failure Impact**: What breaks if this link fails?
    - **Differential Analysis**: What changes between snapshots?

    --{{3}}--
    **Supported Vendor Formats:**
    - Cisco: IOS, IOS-XE, IOS-XR, NXOS, ASA
    - Arista: EOS
    - Juniper: JunOS
    - Palo Alto: PAN-OS
    - F5: BIG-IP
    - AWS: VPC configurations
    - And more being added by the community

## Lab 8.1: Setup

    --{{0}}--
    Batfish runs as a Docker container (the heavy lifting) and a Python library (the interface).

    --{{1}}--
    Run the service:
    `docker run -v $(pwd)/data:/data -p 9996:9996 batfish/batfish`

    --{{2}}--
    Install the client:
    `pip install pybatfish`

    {{1}}
> **Task**: Get the Batfish service running and connect to it using a simple Python script.

## Lab 8.2: Snapshots (The Model)

    --{{0}}--
    We work in **Snapshots**. A snapshot is just a folder of config files.
    `snapshots/network_v1/configs/*.cfg`

    --{{1}}--
    Let's load our current network.
```python
from pybatfish.client.commands import *
from pybatfish.question import load_questions

bf_session.host = 'localhost'
load_questions()

bf_init_snapshot('./snapshots/current', name='current_state', overwrite=True)
```

    --{{2}}--
    Batfish will parse them. If you have syntax errors, it will tell you.

    --{{3}}--
    **Common Batfish Questions:**
    - `bf.q.traceroute()`: Path from src to dst
    - `bf.q.reachability()`: Can traffic reach destination?
    - `bf.q.searchFilters()`: What ACL lines match traffic?
    - `bf.q.filterLineReachability()`: Are ACL rules shadowed?
    - `bf.q.bgpSessionStatus()`: BGP neighbor states
    - `bf.q.ospfSessionCompatibility()`: OSPF neighbor compatibility
    - `bf.q.undefinedReferences()`: Missing ACLs, route-maps
    - `bf.q.unusedStructures()`: Orphaned config objects

    --{{4}}--
    **Differential Analysis** (Before/After):
    ```python
    bf_init_snapshot('./before', name='before')
    bf_init_snapshot('./after', name='after')
    diff = bf.q.differentialReachability(
        reference_snapshot='before'
    ).answer()
    ```
    That alone is worth the price of admission.

## Lab 8.3: Asking Questions

    --{{0}}--
    Now we query the model.
    "Can the Web Server reach the DB Server?"

    --{{1}}--
    Reachability Analysis:
```python
result = bf.q.reachability(
    pathConstraints=PathConstraints(startLocation='@enter(nyc-leaf-01[Gi1/0/1])'),
    headers=HeaderConstraints(dstIps='10.1.20.50', srcIps='10.1.10.50')
).answer().frame()
```

    --{{2}}--
    It returns a Pandas dataframe.
    If it says "DENIED", it will tell you *which ACL* blocked it and at *which line*.

    {{1}}
> **Task**: Verify that SSH (Port 22) is allowed from the Management subnet to the Loopbacks.

## Lab 8.4: Differential Analysis (The Magic)

    --{{0}}--
    Here is the killer feature.
    Snapshot A (Current) vs Snapshot B (Proposed).

    --{{1}}--
    1.  Generate new configs using Ansible (do not deploy). Save to `snapshots/proposed`.
    2.  Load both into Batfish.
    3.  Compare.

```python
bf_init_snapshot('./snapshots/proposed', name='proposed_state')

# Compare
diff = bf.q.differentialReachability().answer().frame()
```

    --{{2}}--
    If the diff shows that flows are "LOST", you know you broke something.
    **Do not deploy.**

    {{1}}
> **Task**: Intentionally break an ACL in your proposed config. Run the differential analysis and see Batfish catch the regression.

## Wrap-up

    --{{0}}--
    Batfish proves our logic is sound.
    But it assumes the device behaves as configured.
    Hardware can fail. Cables can be unplugged. SFP modules die.

    --{{1}}--
    Batfish can't see a cut cable.
    For that, we need to test the *live* network.
    Enter **pyATS**.

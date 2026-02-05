<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Orchestration at Scale. Ansible.
-->

# Session 7: Ansible for Network Engineers

## Introduction: The Force Multiplier

    --{{0}}--
    Writing a Python script to config one router is easy.
    Writing a script to config 1,000 routers, handle SSH timeouts, manage threading, and report errors... that's hard.

    --{{1}}--
    Let's do some math. You need to configure BGP on 200 routers.
    **Manual approach:**
    - SSH to each router: ~30 seconds
    - Paste config: ~20 seconds  
    - Verify: ~10 seconds
    - Total per router: 1 minute
    - **200 routers = 200 minutes = 3.3 hours** (if you don't make mistakes)

    --{{2}}--
    **Ansible approach:**
    - Write playbook once: 15 minutes
    - Run on 200 routers in parallel (20 forks): 5 minutes
    - **Total: 20 minutes**
    - **Time saved: 3 hours. Error rate: Near zero.**

    {{2}}
> **The Ansible Promise**: Write once, deploy everywhere. In parallel. Idempotently.

    --{{3}}--
    Don't reinvent the wheel. Use **Ansible**.
    Ansible is our force multiplier. It takes the logic we built (Jinja2) and pushes it to the edge.
    Key features:
    - **Agentless**: No software to install on network devices
    - **Idempotent**: Run it 100 times, get the same result
    - **Declarative**: Define the desired state, not the steps
    - **Extensible**: 5000+ modules, growing ecosystem

    --{{2}}--
    **Network Automation with Ansible:**
    - **Connection Plugins**: network_cli, netconf, httpapi
    - **Platform Collections**: cisco.ios, cisco.nxos, arista.eos, juniper.junos
    - **Resource Modules**: l3_interfaces, vlans, bgp_global, ospf
    - **25+ supported platforms** out of the box

    --{{3}}--
    **Supported Network Platforms (Partial List):**
    - Cisco: IOS, IOS-XE, IOS-XR, NXOS, ASA
    - Arista: EOS
    - Juniper: JunOS
    - Dell: OS6, OS9, OS10
    - Extreme: EXOS, SLX-OS
    - Palo Alto: PAN-OS
    - F5: BIG-IP
    - And many more via community collections

## Lab 7.1: The Inventory (Static)

    --{{0}}--
    Ansible needs to know *who* to talk to. This is the **Inventory**.
    Let's start simple.

    --{{1}}--
    Create `hosts.ini`:
```ini
[spine]
nyc-spine-01 ansible_host=10.1.10.11
nyc-spine-02 ansible_host=10.1.10.12

[leaf]
nyc-leaf-01  ansible_host=10.1.10.13

[all:vars]
ansible_network_os=cisco.nxos.nxos
ansible_user=admin
ansible_password=cisco
```

    --{{2}}--
    Test it:
    `ansible all -m ping` (Note: for network modules, use `ansible.netcommon.net_ping` or similar, but let's assume standard connection setup).

    {{1}}
> **Task**: Create the inventory file and get a successful "pong" (connectivity check) from all 4 devices.

## Lab 7.2: The Dynamic Inventory (NetBox)

    --{{0}}--
    Static files are dead. `hosts.ini` is already out of date.
    We have **NetBox**. Let's use it.

    --{{1}}--
    Install the collection:
    `ansible-galaxy collection install netbox.netbox`

    --{{2}}--
    Create `netbox_inv.yml`:
```yaml
plugin: netbox.netbox.nb_inventory
api_endpoint: http://netbox
token: 0123456789abcdef...
validate_certs: False
config_context: False
group_by:
  - site
  - device_role
```

    --{{3}}--
    Run it: `ansible-inventory -v -i netbox_inv.yml --graph`
    You will see your devices grouped by Site and Role automatically!

    {{2}}
> **Task**: Set up NetBox dynamic inventory and verify device discovery.

## Advanced: Resource Modules & Connection Plugins

    --{{0}}--
    **Ansible Resource Modules** (Network Automation 2.0):
    Declarative, model-driven modules that replace old command-based approach.

    --{{1}}--
    **Resource Module States:**
    - `merged`: Add/update config (default)
    - `replaced`: Replace config for specified resources only
    - `overridden`: Replace entire resource config
    - `deleted`: Remove specified config
    - `gathered`: Fetch device state as structured data
    - `rendered`: Generate config without applying (dry-run)
    - `parsed`: Parse running config to structured data

    --{{2}}--
    **Examples**: `l3_interfaces`, `vlans`, `bgp_global`, `ospf_interfaces`, `acls`, `static_routes`, `lag_interfaces`

    --{{3}}--
    **Network Connection Plugins:**
    - `network_cli`: SSH-based CLI (most common, uses paramiko)
    - `netconf`: NETCONF over SSH (Yang models, XML)
    - `httpapi`: REST APIs (NXOS, ACI, Arista eAPI)
    - Set via `ansible_connection` variable in inventory

    {{1}}
> **Task**: Configure Dynamic Inventory. Verify that `nyc-spine-01` appears under `@spine_switch` group.

## Lab 7.3: The Backup Playbook

    --{{0}}--
    First rule of automation: Back up before you screw up.
    Let's write a playbook to save the config.

```yaml
---
- name: Backup Configs
  hosts: all
  gather_facts: no
  tasks:
    - name: Backup Running Config
      cisco.nxos.nxos_config:
        backup: yes
      register: backup_output

    - name: Debug backup path
      debug:
        msg: "Backup saved to {{ backup_output.backup_path }}"
```

    {{1}}
> **Task**: Run this playbook. Find the backup files on your local disk.

## Lab 7.4: Deploying Configs (The Real Deal)

    --{{0}}--
    Now we combine everything.
    Playbook + NetBox Data + Jinja2 Template = State.

```yaml
---
- name: Deploy Interface Configs
  hosts: spine_switch
  gather_facts: no
  tasks:
    - name: Render Template
      ansible.builtin.set_fact:
        intf_config: "{{ lookup('template', 'templates/interfaces.j2') }}"

    - name: Push Config
      cisco.nxos.nxos_config:
        src: "{{ intf_config }}"
```

    --{{1}}--
    Ansible natively handles the Jinja2 rendering. We don't need our Python script from Session 5 anymore; Ansible has `template` modules built-in.

## Lab 7.5: Check Mode (Dry Run)

    --{{0}}--
    Before you push the button, check what will happen.
    Run with `--check --diff`.

    --{{1}}--
    Ansible will connect, compare the running config with your template, and show you the **diff**.
    "I will remove `vlan 10` and add `vlan 20`".
    This is powerful.

    {{1}}
> **Task**: Make a change in your template. Run with `--check --diff`. Verify the output shows the expected changes without actually applying them.

## Wrap-up

    --{{0}}--
    We can now change the entire network with one command.
    That is terrifying.
    If you have a bug in your template, you just broke the entire data center in 2 seconds.

    --{{1}}--
    We need safety. We need pre-change testing.
    Enter **Batfish**.

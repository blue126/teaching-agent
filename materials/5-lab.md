<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Templating. Separating Data from Logic.
-->

# Session 5: Jinja2 Templating

## Introduction: The Formula

    --{{0}}--
    Stop copy-pasting text files.
    If you are finding and replacing "VLAN10" with "VLAN20" in Notepad++, you are a danger to society.

    --{{1}}--
    Let me paint a picture: You need to deploy 50 access switches.
    Each has the same base config: hostname, SNMP, NTP, syslog, AAA, management VLAN.
    But each has a unique hostname and management IP.

    --{{2}}--
    **The old way**: Open notepad. Copy a "golden" config. Find/replace the hostname. Find/replace the IP. Copy to switch. Repeat 50 times.
    By switch #37, you forget to change the hostname. Now you have 14 switches named "nyc-acc-01". Oops.

    --{{3}}--
    **The new way**: One template. One data source (NetBox). One script. 50 perfect configs in 10 seconds.
    Each hostname correct. Each IP correct. Each time.

    {{3}}
> **The Truth**: Humans are bad at repetitive tasks. Computers are great at them.

    --{{4}}--
    We treat configuration generation as a math problem.
    `Data + Template = Configuration`.

    --{{5}}--
    **The Data**: Comes from NetBox (or YAML files).
    **The Template**: Defines the structure (Jinja2).
    **The Config**: The output text we push to the router.

## Jinja2 Basics: Variables

    --{{0}}--
    Jinja2 is the de facto templating engine for Python.
    Created by Armin Ronacher (same author as Flask).
    Fast, expressive, and extensible. Used by:
    - **Ansible**: For all templating
    - **Flask**: Web framework templates
    - **Salt**: Configuration management
    - **Airflow**: Workflow DAGs

    --{{1}}--
    **The Delimiter Syntax:**
    - **{{ }}**: Expressions (print variables)
    - **{% %}**: Statements (logic: for, if, set)
    - **{# #}**: Comments (not in output)
    - **{%- -%}**: Whitespace control (strip)

    --{{2}}--
    **Example - Basic Variable:**
    Template: `hostname {{ device_name }}`
    Data: `{'device_name': 'nyc-spine-01'}`
    Output: `hostname nyc-spine-01`

    --{{3}}--
    **Filters** transform variables:
    - `{{ name | upper }}`: UPPERCASE
    - `{{ items | join(', ') }}`: Join list
    - `{{ value | default('N/A') }}`: Fallback
    - `{{ ip | ipaddr }}`: Network filter (Ansible)
    - `{{ text | truncate(20) }}`: Shorten text
    - **50+ built-in filters available**

## Lab 5.1: Loops (The Interface Generator)

    --{{0}}--
    Network engineers love loops. We have 48 ports to config, and they are mostly identical.
    Let's loop.

    --{{1}}--
    The Python data:
```python
interfaces = [
    {"name": "Gi1/0/1", "desc": "Server A", "vlan": 10},
    {"name": "Gi1/0/2", "desc": "Server B", "vlan": 20}
]
```

    --{{2}}--
    The Template (`interfaces.j2`):
```jinja2
{% for intf in interfaces %}
interface {{ intf.name }}
  description {{ intf.desc }}
  switchport access vlan {{ intf.vlan }}
  no shutdown
!
{% endfor %}
```

    {{1}}
> **Task**: Create a python script that defines a list of 5 VLANs and renders a template to create them (`vlan 10`, `name Web`, etc.).

## Lab 5.2: Conditionals (The Logic)

    --{{0}}--
    Not all interfaces are created equal.
    Some are Layer 2, some are Layer 3.
    We use `{% if %}`.

    --{{1}}--
    Template:
```jinja2
interface {{ intf.name }}
{% if intf.ip_address %}
  no switchport
  ip address {{ intf.ip_address }}
{% else %}
  switchport mode access
  switchport access vlan {{ intf.vlan }}
{% endif %}
```

    --{{2}}--
    Now your template is smart. It adapts to the data.

    --{{3}}--
    **Advanced Jinja2 Features:**
    - **Template Inheritance**: `{% extends "base.j2" %}`
    - **Blocks**: `{% block content %}...{% endblock %}`
    - **Macros**: Reusable functions
    - **Include**: `{% include "partial.j2" %}`
    - **Import**: `{% import "macros.j2" as m %}`
    - **Set**: `{% set var = value %}`
    - **Tests**: `{% if var is defined %}`

## Lab 5.3: Template Inheritance (DRY Configs)

    --{{0}}--
    You have 3 device types: Spine, Leaf, Access.
    They all share 80% of the same base config (SNMP, AAA, NTP, syslog).
    You don't want to copy-paste that 80% into three templates.

    --{{1}}--
    **Template Inheritance** to the rescue.
    Think of it like object-oriented programming for configs.

    --{{2}}--
    **Base template** (`base_config.j2`):
```jinja2
hostname {{ hostname }}
!
{% block snmp %}
snmp-server community {{ snmp_community }} RO
snmp-server location {{ site }}
{% endblock %}
!
{% block routing %}
{# Child templates override this #}
{% endblock %}
!
end
```

    --{{3}}--
    **Child template** (`spine_config.j2`):
```jinja2
{% extends "base_config.j2" %}

{% block routing %}
! Spine-specific routing
router bgp {{ asn }}
  neighbor {{ peer_ip }} remote-as {{ peer_asn }}
{% endblock %}
```

    --{{4}}--
    **The magic**: When you render `spine_config.j2`, it inherits everything from base_config,
    then fills in the `routing` block with spine-specific content.
    One base template. Three child templates. No duplication.

    {{4}}
> **Task**: Create a base template with common settings, then extend it for different device roles.

## Lab 5.4: Macros (Functions for Templates)

    --{{0}}--
    Macros are reusable template functions. Think of them as "config snippets" you can call with different parameters.

    --{{1}}--
    **Define a macro** (`macros.j2`):
```jinja2
{% macro interface_config(name, description, vlan) %}
interface {{ name }}
  description {{ description }}
  switchport mode access
  switchport access vlan {{ vlan }}
  spanning-tree portfast
  no shutdown
{% endmacro %}

{% macro vlan_config(vlan_id, vlan_name) %}
vlan {{ vlan_id }}
  name {{ vlan_name }}
{% endmacro %}
```

    --{{2}}--
    **Use the macro** in your main template:
```jinja2
{% import 'macros.j2' as m %}

hostname {{ hostname }}
!
{# Create VLANs #}
{{ m.vlan_config(10, 'DATA') }}
{{ m.vlan_config(20, 'VOICE') }}
{{ m.vlan_config(30, 'GUEST') }}
!
{# Configure interfaces #}
{{ m.interface_config('Gi1/0/1', 'Server-A', 10) }}
{{ m.interface_config('Gi1/0/2', 'Server-B', 10) }}
{{ m.interface_config('Gi1/0/3', 'IP-Phone', 20) }}
```

    --{{3}}--
    **Why this is powerful**:
    - Change the interface config format once (in the macro)
    - All 48 ports update automatically
    - No copy-paste errors
    - Easier to read and maintain

    {{3}}
> **Task**: Create macros for common config blocks (ACL, route-map, prefix-list) and reuse them.

## Lab 5.5: Real-World Integration (NetBox to Config)
print(config)
```

    {{1}}
> **Task**: Create `generate_config.py`. It should fetch `nyc-spine-01` from NetBox and render a full base config (Hostname, SNMP, Logging) using a template.

## Common Pitfalls & Jinja2 Best Practices

    --{{0}}--
    Jinja2 is powerful but can create unmaintainable messes if you're not careful.

    {{0}}
### ❌ Common Mistakes

**1. Putting too much logic in templates**
```jinja2
{# BAD: Business logic in template #}
{% set mgmt_vlan = 10 if site == 'nyc' else 20 if site == 'sfo' else 30 %}
{% if device_role == 'spine' and asn > 65000 and peer_count >= 2 %}
  {# 50 lines of complex BGP config #}
{% endif %}

{# GOOD: Logic in Python, data in template #}
hostname {{ hostname }}
vlan {{ management_vlan }}  {# Calculated in Python #}
{{ bgp_config }}  {# Pre-rendered string from Python #}
```

**2. Not handling missing variables**
```jinja2
{# BAD: Crashes if snmp_community is undefined #}
snmp-server community {{ snmp_community }} RO

{# GOOD: Provide defaults #}
snmp-server community {{ snmp_community | default('public') }} RO

{# BETTER: Check if defined #}
{% if snmp_community is defined %}
snmp-server community {{ snmp_community }} RO
{% endif %}
```

**3. Ignoring whitespace control**
```jinja2
{# BAD: Generates blank lines everywhere #}
{% for vlan in vlans %}
vlan {{ vlan.id }}
  name {{ vlan.name }}
{% endfor %}

{# GOOD: Strip whitespace #}
{% for vlan in vlans -%}
vlan {{ vlan.id }}
  name {{ vlan.name }}
{% endfor -%}
```

**4. Not using includes for repeated blocks**
```jinja2
{# BAD: Same ACL definition in 10 templates #}
ip access-list extended MGMT-IN
  permit tcp any host {{ mgmt_ip }} eq 22
  deny ip any any

{# GOOD: One file, many includes #}
{# In acls/mgmt-in.j2 #}
ip access-list extended MGMT-IN
  permit tcp any host {{ mgmt_ip }} eq 22
  deny ip any any

{# In main template #}
{% include 'acls/mgmt-in.j2' %}
```

**5. Forgetting to escape special characters**
```jinja2
{# BAD: Jinja2 interprets {{ }} in descriptions #}
description Link to {{ remote_site }}  {# Tries to render variable! #}

{# GOOD: Escape or use raw #}
description Link to {% raw %}{{ remote_site }}{% endraw %}
{# Or just use regular text #}
description Link to {{ remote_site_name }}
```

    {{1}}
### ✅ Best Practices

**1. Separate data, templates, and logic**
```
project/
  ├── data/
  │   ├── nyc-spine-01.yaml    # Device-specific data
  │   └── global.yaml          # Shared values
  ├── templates/
  │   ├── base.j2             # Base template
  │   ├── spine.j2            # Role-specific
  │   └── macros.j2           # Reusable blocks
  └── generate.py             # Orchestration logic
```

**2. Use meaningful variable names**
```jinja2
{# BAD: Cryptic #}
{{ x }}
{{ d['i'][0]['n'] }}

{# GOOD: Self-documenting #}
{{ management_ip }}
{{ device.interfaces[0].name }}
```

**3. Add comments for future maintainers**
```jinja2
{# Configure iBGP peers for spine layer #}
{# ASN range: 65000-65099 for private use #}
router bgp {{ bgp_asn }}
  {# Loopback 0 used for iBGP source #}
  neighbor {{ peer_ip }} remote-as {{ bgp_asn }}
  neighbor {{ peer_ip }} update-source Loopback0
```

**4. Test templates with edge cases**
```python
# Test data variations
test_cases = [
    {'hostname': 'test'},  # Minimal
    {'hostname': 'test', 'vlans': []},  # Empty list
    {'hostname': 'test', 'interfaces': None},  # Null value
    {'hostname': 'a' * 100},  # Long strings
]

for test_data in test_cases:
    try:
        output = template.render(test_data)
        print(f"OK: {test_data}")
    except Exception as e:
        print(f"FAIL: {test_data} -> {e}")
```

**5. Version control your templates**
```bash
# Treat templates like code
git add templates/
git commit -m "Add BGP config template for spine routers"
git tag -a v1.0 -m "Production release"

# Review changes before deploying
git diff templates/spine.j2
```

**6. Use custom filters for network-specific operations**
```python
# In your Python code, define custom filters
def ipaddr_filter(value, query):
    """Network address filters (similar to Ansible)"""
    import ipaddress
    net = ipaddress.ip_network(value, strict=False)
    if query == 'network':
        return str(net.network_address)
    elif query == 'netmask':
        return str(net.netmask)
    return str(net)

env = Environment(loader=FileSystemLoader('templates'))
env.filters['ipaddr'] = ipaddr_filter

# In template:
{{ '10.1.1.0/24' | ipaddr('network') }}  # Output: 10.1.1.0
{{ '10.1.1.0/24' | ipaddr('netmask') }}  # Output: 255.255.255.0
```

## Wrap-up

    --{{0}}--
    You have built a config generator.
    But wait... garbage in, garbage out.
    What if the data in NetBox is wrong? What if the config is invalid syntax?

    --{{1}}--
    Next, we validate. **JSON Schema** helps us unsure our data is clean before we even try to enforce it.

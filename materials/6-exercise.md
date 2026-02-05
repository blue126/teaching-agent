<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Validation. Preventing Garbage In, Garbage Out.
-->

# Session 6: Data Validation with JSON Schema

## Introduction: Defensive Engineering

    --{{0}}--
    Scripts are fragile. If you feed them bad data, they explode.
    Or worse, they silently generate a config that takes down the network.

    --{{1}}--
    True story: An engineer writes a script to provision VLANs from a CSV file.
    Works great in the lab. Deploys to production. Row 47 of the CSV has a typo: VLAN "10O" (letter O, not zero).
    Script doesn't validate. Jinja2 renders: `vlan 10O`. Pushed to 200 switches.
    Every switch rejects the config. Network change frozen for 3 hours while we manually fix it.

    --{{2}}--
    **Cost of that typo:**
    - 3 hours of downtime
    - 5 engineers scrambling
    - Angry CTO asking "Don't we have tests?"
    - We do now.

    {{2}}
> **Lesson**: Validate BEFORE you render. Validate BEFORE you push. Always.

    --{{3}}--
    We need a bouncer at the door.
    That bouncer is **JSON Schema**.
    We define *exactly* what valid data looks like. If it doesn't match, we stop immediately.

## JSON Schema: The Rules

    --{{0}}--
    JSON Schema is a **vocabulary for annotating and validating JSON documents**.
    It's a standard (IETF draft, widely adopted) that provides:
    - Data validation
    - Documentation
    - Interaction control
    - Code generation hints

    --{{1}}--
    **The Type System:**
    - `string`: Text data
    - `number`: Any numeric value (int or float)
    - `integer`: Whole numbers only
    - `boolean`: true/false
    - `array`: Ordered list
    - `object`: Key-value pairs
    - `null`: Absence of value

    --{{2}}--
    **Common Constraints:**
    - **String**: `minLength`, `maxLength`, `pattern` (regex), `format` (email, uri, ipv4, date-time)
    - **Number**: `minimum`, `maximum`, `exclusiveMinimum`, `multipleOf`
    - **Array**: `minItems`, `maxItems`, `uniqueItems`, `items`
    - **Object**: `properties`, `required`, `additionalProperties`, `minProperties`

    --{{3}}--
    Example Rule: "VLAN ID must be an integer between 1 and 4094".
    If I give you "10", that's a string. **REJECT**.
    If I give you 5000. **REJECT**.

```json
{
  "type": "integer",
  "minimum": 1,
  "maximum": 4094,
  "description": "IEEE 802.1Q VLAN ID"
}
```

## Exercise 6.1: Defining the Contract

    --{{0}}--
    Let's protect our variables.
    Open `schema.json`.

    --{{1}}--
    We want a root object with a required `hostname` and `interfaces` list.

```json
{
  "type": "object",
  "required": ["hostname", "interfaces"],
  "properties": {
    "hostname": { "type": "string" },
    "interfaces": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "name": { "type": "string" },
          "enabled": { "type": "boolean" }
        }
      }
    }
  }
}
```

    {{1}}
> **Task**: Create this schema file. Try to validate a JSON file that is missing the "hostname" key using an online validator or Python.

## Exercise 6.2: Regex is your Friend

    --{{0}}--
    Strings are dangerous.
    "192.168.1.1" is an IP. "999.999.999.999" is not.
    We use `pattern` with regular expressions.

    --{{1}}--
    **JSON Schema Format Keywords** (built-in):
    - `date-time`: RFC 3339 timestamp
    - `date`: Full date (YYYY-MM-DD)
    - `time`: Time of day
    - `email`: RFC 5321 email address
    - `hostname`: RFC 1123 hostname
    - `ipv4`: IPv4 address (dotted quad)
    - `ipv6`: IPv6 address
    - `uri`: RFC 3986 URI
    - `uuid`: RFC 4122 UUID

    --{{2}}--
    IPv4 CIDR Pattern (Simplified):
    `^([0-9]{1,3}\.){3}[0-9]{1,3}(\/([0-9]|[1-2][0-9]|3[0-2]))?$`

    --{{3}}--
    Add this to your schema for the `ip_address` field.
    Now, if someone types `10.1.1.256`, the schema validator catches it before we even generate the config.

    --{{4}}--
    **Advanced JSON Schema:**
    - **$ref**: Reference other schemas
    - **allOf**: Must match all schemas
    - **anyOf**: Must match at least one
    - **oneOf**: Must match exactly one
    - **not**: Must not match
    - **$defs**: Define reusable sub-schemas

## Exercise 6.3: The Python Bouncer

    --{{0}}--
    Let's integrate this into our workflow.
    `pip install jsonschema`

    --{{1}}--
    The Code:

```python
import yaml
import json
from jsonschema import validate, ValidationError

# Load Data
with open('host_vars/nyc-spine-01.yml') as f:
    data = yaml.safe_load(f)

# Load Schema
with open('schema.json') as f:
    schema = json.load(f)

try:
    validate(instance=data, schema=schema)
    print("Data is Valid. Proceeding to Jinja2...")
except ValidationError as e:
    print(f"FATAL ERROR: Data invalid! {e.message}")
    exit(1)
```

    {{1}}
> **Task**: Create a `validate_data.py` script. Feed it both valid and invalid YAML files. Watch it save you from deploying garbage.

## Wrap-up

    --{{0}}--
    We have Data (NetBox). We have Templates (Jinja2). We have Validation (JSON Schema).
    We can generate perfect configs.
    But how do we push them to 500 devices at once? ssh-loop? No.

    --{{1}}--
    Enter **Ansible**.

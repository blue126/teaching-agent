# Session 6 – Data Validation with JSON Schema (Lecture + Exercise)

## Summary
"Trust, but verify." In automation, we trust nothing. If a user typos a VLAN ID as "Ten" instead of `10`, our Jinja2 template might crash—or worse, generate a broken config. In this session, we learn how to use **JSON Schema** to enforce strict structure on our input data. We validate our data *before* it touches a template or a device.

## Content
*   **The Case for Validation**: Preventing "Garbage In, Garbage Out".
*   **JSON Schema Fundamentals**:
    *   Types: `string`, `integer`, `array`, `object`.
    *   Constraints: `minimum`, `maximum`, `pattern` (Regex for IP addresses), `enum`.
    *   Required fields.
*   **Python Integration**: Using the `jsonschema` library to validate generic dictionaries (from YAML/JSON).

## Activities
*   **Lecture**: Walkthrough of a JSON Schema definition for a Network Interface.
*   **Exercise 6.1**: Define a schema that requires a `hostname` (string) and a list of `interfaces`.
*   **Exercise 6.2**: Add Regex validation to ensure `ip_address` follows the CIDR format.
*   **Exercise 6.3**: Write a script that loads a YAML file, validates it, and only proceeds to Jinja2 rendering if valid.

## References & Sources
*   [JSON Schema Website](https://json-schema.org/)
*   Python `jsonschema` library
*   NetBox internal validation (it uses this too!)

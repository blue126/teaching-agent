# Session 5 – Jinja2 Templating (Lab)

## Summary
Hardcoding configurations is a sin. In this session, we learn the art of **Templating**. specific data is separated from the configuration structure. We use **Jinja2**, the industry standard for Python templating, to generate dynamic network configurations. We will cover variables, loops, conditionals, and filters, eventually generating full device configurations programmatically.

## Content
*   **Templating Philosophy**: Data + Template = Configuration.
*   **Jinja2 Syntax**:
    *   **Variables**: `{{ hostname }}`
    *   **Control Structures**: `{% for interface in interfaces %}`, `{% if vlan.id == 10 %}`
    *   **Filters**: `{{ variable | upper }}`
*   **Modularization**: Using `{% include %}` to break down complex configs.
*   **Rendering**: Using Python to merge a dictionary of data with a `.j2` template file.

## Activities
*   **Lab 5.1**: "Hello World" - Render a simple Message of the Day (MOTD) banner.
*   **Lab 5.2**: Build an Interface configuration loop using a list of dictionaries.
*   **Lab 5.3**: Create a "Base Config" template (NTP, SNMP, AAA, Users) for our Nexus switches.
*   **Lab 5.4**: The Integration - Fetch data from NetBox (Session 3/4) and feed it into Jinja2.

## References & Sources
*   [Jinja2 Documentation](https://jinja.palletsprojects.com/)
*   Real Python: Primer on Jinja2
*   Ansible: Templating with Jinja2

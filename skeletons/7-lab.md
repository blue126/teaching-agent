# Session 7 – Ansible for Network Engineers (Lab)

## Summary
Scripts are great for one device. But for 100 devices, we need an orchestrator. Ansible is the industry standard for network configuration management. It is agentless, idempotent, and declarative. In this extensive lab, we will build a production-grade Ansible playbook structure, moving from a static inventory to a dynamic one powered by NetBox.

## Content
*   **Ansible Architecture**: Control Node, Managed Nodes, Inventory, Plugins.
*   **The Playbook**: YAML-based instructions.
*   **Network Modules**: `cisco.ios.ios_command`, `cisco.nxos.nxos_config`, etc.
*   **Inventory Management**:
    *   **Static**: `hosts.ini`
    *   **Dynamic**: `nb_inventory.yml` (Plugin)
*   **Variables**: `group_vars`, `host_vars`, and hierarchy.
*   **Roles**: Organizing code for reusability.

## Activities
*   **Lab 7.1**: Setup Ansible and ping all devices (`ansible all -m ping`).
*   **Lab 7.2**: Connect Ansible to NetBox to generate Dynamic Inventory.
*   **Lab 7.3**: Write a Playbook to backup running configurations from all devices.
*   **Lab 7.4**: Write a Playbook to deploy the VLANs and Interfaces we generated in Session 5.
*   **Lab 7.5**: Check Mode (`--check`) - Previewing changes without applying them.

## References & Sources
*   [Ansible Network Documentation](https://docs.ansible.com/ansible/latest/network/index.html)
*   Packet Coders: NetBox Dynamic Inventory Guide
*   Red Hat Ansible for Network Automation (Free course material)

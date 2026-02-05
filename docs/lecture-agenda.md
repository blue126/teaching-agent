# Lecture Agenda

## Overview
This agenda matches the **Python Network Automation Bootcamp** structure. It is designed as an intensive journey from zero to a full CI/CD pipeline.
*   **Total Duration**: ~40-50 Hours
*   **Approach**: Progressive accumulation. Each session builds the environment for the next (e.g., NetBox data usage in Ansible).
*   **Style**: Hosted by **Alex Rivera**, focusing on "Production-Ready" workflows.

## Modules / Sessions

### Module 1: Foundation & Source of Truth
*   **Session 1: Introduction to Network Automation**
    *   **Duration**: 2 hours
    *   **Type**: Lecture
    *   **Goal**: Understand the "Why" and "How" of automation architecture.
    *   **Summary**: Shift from CLI key-pounding to engineering. Use cases, principles, and the tool landscape.
    *   **Materials**: `materials/1-lecture.md`

*   **Session 2: NetBox as Source of Truth (SoT)**
    *   **Duration**: 4 hours
    *   **Type**: Lab
    *   **Goal**: Model a network environment in NetBox.
    *   **Summary**: Deploying NetBox. Modeling Sites, Racks, Devices, and IPAM. Understanding why "Excel is not a database".
    *   **Materials**: `materials/2-lab.md`

### Module 2: programmatic Access & Data
*   **Session 3: Mastering REST APIs with Python**
    *   **Duration**: 4 hours
    *   **Type**: Lab
    *   **Goal**: Interact with NetBox and Devices programmatically.
    *   **Summary**: HTTP verbs, headers, auth. Using Postman and `requests` library. Pulling data from NetBox vs. scraping screens.
    *   **Materials**: `materials/3-lab.md`

*   **Session 4: GraphQL - The Efficient Alternative**
    *   **Duration**: 3 hours
    *   **Type**: Lab
    *   **Goal**: Fetch exactly the data you need.
    *   **Summary**: REST vs. GraphQL. Query structure. Integrating GraphQL into Python scripts for efficient data queries.
    *   **Materials**: `materials/4-lab.md`

### Module 3: Templating & Configuration Generation
*   **Session 5: Jinja2 Templating**
    *   **Duration**: 4 hours
    *   **Type**: Lab
    *   **Goal**: Separate data from logic to generate configs.
    *   **Summary**: Variables, Loops, Conditionals. Creating modular templates for Cisco/Arista/Juniper configs.
    *   **Materials**: `materials/5-lab.md`

*   **Session 6: Data Validation with JSON Schema**
    *   **Duration**: 2 hours
    *   **Type**: Lecture + Exercise
    *   **Goal**: Validating inputs before they break the network.
    *   **Summary**: Defining data models. Validating YAML/JSON input files against a strict schema.
    *   **Materials**: `materials/6-exercise.md`

### Module 4: Configuration Management
*   **Session 7: Ansible for Network Engineers**
    *   **Duration**: 6 hours
    *   **Type**: Lab
    *   **Goal**: Automate mass-configuration management.
    *   **Summary**: Inventory management (Static vs. NetBox Dynamic). Playbooks, Tasks, Modules. Idempotency explained.
    *   **Materials**: `materials/7-lab.md`

### Module 5: Network Testing & Assurance
*   **Session 8: Pre-Change Testing with Batfish**
    *   **Duration**: 4 hours
    *   **Type**: Lab
    *   **Goal**: Validate changes *before* pushing to devices.
    *   **Summary**: Modeling the network state. ACL testing, routing analysis, and structural validation without packets.
    *   **Materials**: `materials/8-lab.md`

*   **Session 9: Post-Change Testing with pyATS**
    *   **Duration**: 4 hours
    *   **Type**: Lab
    *   **Goal**: Verify the actual state of the network.
    *   **Summary**: Testbeds, Genie parsers (CLI to JSON). Writing test cases to verify OSPF neighbors, BGP sessions, and interfaces.
    *   **Materials**: `materials/9-lab.md`

### Module 6: DevOps & Pipelines
*   **Session 10: Git & Version Control**
    *   **Duration**: 3 hours
    *   **Type**: Lab
    *   **Goal**: Manage infrastructure as code.
    *   **Summary**:git add/commit/push. Branching strategies. Collaboration via Pull Requests. "Config as Code".
    *   **Materials**: `materials/10-lab.md`

*   **Session 11: CI/CD Pipelines (GitHub Actions)**
    *   **Duration**: 5 hours
    *   **Type**: Capstone Project
    *   **Goal**: Build the "Golden Loop".
    *   **Summary**: Automating the entire workflow: Checkout -> Lint -> Batfish Test -> Ansible Deploy -> pyATS Verify.
    *   **Materials**: `materials/11-capstone.md`

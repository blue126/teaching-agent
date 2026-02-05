# Session 1 – Introduction to Network Automation (Lecture)

## Summary
Welcome to the bootcamp! In this first session, we set the stage. We aren't just learning scripts; we are changing how we operate networks. We will move away from the "Artisan CLI" approach to an industrial engineering approach. We'll cover the "Why", the core principles (Idempotency, Atomicity), and map out the landscape of tools we will conquer (NetBox, Ansible, Git, etc.).

## Content
*   **The Problem**: Why humans are bad at SSH (Fat fingers, inconsistency, lack of documentation).
*   **The Solution**: Infrastructure as Code (IaC).
*   **Core Principles**:
    *   Source of Truth (SoT) vs. Network Reality.
    *   Imperative vs. Declarative models.
    *   Idempotency: Running it twice shouldn't break it.
*   **The Toolkit Overview** (The "BOM" for this course):
    *   **NetBox**: Our brain.
    *   **Git**: Our time machine.
    *   **Jinja2/YAML**: Our language.
    *   **Ansible/Python**: Our hands.
    *   **Batfish/pyATS**: Our safety net.

## Activities
*   **Discussion**: "Horror Stories" – Share a time a manual change caused an outage.
*   **Setup**: Verify everyone has access to the lab environment (VS Code, Python installed, Git configured).

## References & Sources
*   *Network Programmability and Automation* (O'Reilly)
*   Packet Coders Blog
*   The "Twelve-Factor App" methodology (applied to NetOps)

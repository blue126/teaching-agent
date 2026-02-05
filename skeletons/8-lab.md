# Session 8 – Pre-Change Testing with Batfish (Lab)

## Summary
Pushing config and praying is not a strategy. We need to know if our changes will work *before* we touch a production device. **Batfish** allows us to analyze configurations offline. It builds a digital twin of the network, calculates the routing tables, and allows us to ask questions like "Can Site A talk to Site B?" without sending a single packet.

## Content
*   **Pre-Change Verification**: The concept of validating intent before deployment.
*   **Batfish Architecture**: The Docker container, the Python client (`pybatfish`).
*   **Configuration Parsing**: How Batfish understands Cisco/Arista/Juniper syntax.
*   **Analysis Questions**:
    *   **ACL Analysis**: "Does this ACL block SSH?"
    *   **Reachability**: "Can Subnet A reach Subnet B?"
    *   **Routing Table**: "Which path will traffic take?"

## Activities
*   **Lab 8.1**: Installing Batfish (Docker) and connecting via Jupyter/Python.
*   **Lab 8.2**: Snapshotting - Uploading our current network configs to Batfish.
*   **Lab 8.3**: Running an "ACL Reachability" analysis.
*   **Lab 8.4**: Differential Analysis - Comparing the "Current" snapshot vs. a "Proposed" config change to see impact.

## References & Sources
*   [Batfish.org](https://batfish.org/)
*   [Pybatfish Documentation](https://batfish.readthedocs.io/en/latest/)
*   Packet Coders: Batfish Intro

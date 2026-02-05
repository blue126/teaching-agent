# Session 9 – Post-Change Testing with pyATS (Lab)

## Summary
Validation in code (Batfish) is great, but eventually, the rubber meets the road. **pyATS** (Python Automated Test Systems) is the industry standard for testing live networks. We use it to verify that our changes actually worked. "Did the OSPF neighbor come up?" "Is the BGP session Established?" "Are we dropping packets?". We will use the **Genie** library to parse CLI output into structured data and run automated test cases.

## Content
*   **The pyATS Ecosystem**: pyATS (The harness) + Genie (The library).
*   **The Testbed**: Defining our devices (YAML).
*   **Genie Parsers**: Transforming `show ip interface brief` into JSON.
*   **AEtest**: The testing framework (Setup, Test, Cleanup).
*   **pyATS Jobs**: Orchestrating multiple test scripts.

## Activities
*   **Lab 9.1**: specific the Testbed file (`testbed.yml`).
*   **Lab 9.2**: Interactive Genie - Using the CLI to parse commands (`genie parse "show version"`).
*   **Lab 9.3**: Writing a Python script to verify OSPF neighbors.
*   **Lab 9.4**: Use `genie learn` to snapshot the network state before and after a change, and generate a diff.

## References & Sources
*   [Cisco pyATS Documentation](https://pubhub.devnetcloud.com/media/pyats/docs/index.html)
*   [Genie Parsers List](https://pubhub.devnetcloud.com/media/genie-feature-browser/docs/#/parsers)
*   NetDevOps: Testing with pyATS

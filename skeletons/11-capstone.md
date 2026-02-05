# Session 11 – CI/CD Pipelines (Capstone Project)

## Summary
The "Golden Loop". In this final capstone session, we automate the automation. We build a Continuous Integration / Continuous Delivery (CI/CD) pipeline using GitHub Actions. The goal: You push a change to a Git branch, and the system automatically Lints it, Tests it (Batfish), Deploys it (Ansible), and Verifies it (pyATS).

## Content
*   **CI/CD Concepts**: What is a "Pipeline"? Jobs, Steps, Runners.
*   **GitHub Actions**: Defining workflows in `.github/workflows/`.
*   **Secrets Management**: Safely passing credentials (SSH keys, NetBox Tokens) to the runner.
*   **The Pipeline Stages**:
    1.  **Code Quality**: YAML Lint, Python syntax check.
    2.  **Pre-Flight**: Batfish differential analysis.
    3.  **Deployment**: Ansible Playbook execution (Staging vs Prod).
    4.  **Post-Flight**: pyATS verification.

## Activities
*   **Capstone Step 1**: Define secrets in GitHub Repo (HOST, USERNAME, PASSWORD, NETBOX_TOKEN).
*   **Capstone Step 2**: Create the Workflow YAML.
*   **Capstone Step 3**: Integrate Batfish. If Batfish fails, the pipeline must stop.
*   **Capstone Step 4**: Integrate Ansible. Deploy the change.
*   **Capstone Step 5**: Trigger the pipeline by pushing a Pull Request.

## References & Sources
*   [GitHub Actions Spec](https://docs.github.com/en/actions)
*   Packet Coders: Building a Network CI Pipeline

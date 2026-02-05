<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  The Golden Loop. CI/CD. The End.
-->

# Session 11: CI/CD Pipelines (Capstone)

## Introduction: The "One-Click" Dream

    --{{0}}--
    We have spent 10 sessions building isolated tools.
    Now we bind them together into one automated pipeline.

    --{{1}}--
    **Manual deployment (what you're doing now):**
    1. Make config changes locally
    2. Run yamllint manually
    3. Run JSON Schema validation manually
    4. Run Batfish tests manually
    5. If all pass, run Ansible playbook manually
    6. If deployed, run pyATS tests manually
    7. **Time: 20 minutes. Error-prone: Very.**

    --{{2}}--
    **CI/CD Pipeline (what we're building today):**
    1. `git push` to main branch
    2. **Robot automatically:**
       - Lints YAML (10 seconds)
       - Validates JSON Schema (5 seconds)
       - Runs Batfish simulation (30 seconds)
       - Deploys with Ansible (2 minutes)
       - Verifies with pyATS (1 minute)
       - Sends notification with results
    3. **Time: 4 minutes. Human involvement: Zero.**

    {{2}}
> **CI/CD**: The ultimate force multiplier. You write code once. Robot runs it forever.

    --{{3}}--
    **CI/CD** means we treat the network like software.
    **CI (Integration)**: Does my code work? (Lint, Batfish).
    **CD (Delivery)**: Push it to production (Ansible, pyATS).

    --{{4}}--
    We don't run these tools on our laptop anymore.
    We run them in the cloud (GitHub Actions).

## The Pipeline Architecture

    --{{0}}--
    We will define a file `.github/workflows/deploy.yml`.
    It triggers on every `push` to `main`.

    {{1}}
### The Stages
1.  **Checkout Code**: Get the repo.
2.  **Lint**: `yamllint .` (Catch typos).
3.  **Validate**: `python validate_data.py` (JSON Schema).
4.  **Simulate**: Run Batfish Docker + Python script.
5.  **Deploy**: Run Ansible Playbook.
6.  **Verify**: Run pyATS.

## Capstone Part 1: The Workflow File

    --{{0}}--
    Here is the beast.

```yaml
name: Network Deploy
on:
  push:
    branches: [ "main" ]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v3
        with:
          python-version: "3.9"
      - name: Install Dependencies
        run: pip install yamllint jsonschema
      - name: Lint
        run: yamllint host_vars/

  simulate:
    needs: validate
    runs-on: ubuntu-latest
    services:
      batfish:
        image: batfish/batfish
        ports:
          - 9996:9996
    steps:
      - uses: actions/checkout@v3
      - name: Run Batfish Analysis
        run: python check_reachability.py

    --{{1}}--
    **GitHub Actions Workflow Syntax:**
    - `on`: Trigger events (push, pull_request, schedule, workflow_dispatch, release)
    - `jobs`: Collection of steps executing on same runner
    - `needs`: Job dependencies for sequential execution
    - `runs-on`: Runner type (ubuntu-latest, windows-latest, macos-latest, self-hosted)
    - `steps`: Individual actions or shell commands
    - `uses`: Pre-built actions from GitHub Marketplace
    - `with`: Input parameters for actions
    - `env`: Environment variables (job or step level)
    - `secrets`: Encrypted secrets (`${{ secrets.NAME }}`)

    --{{2}}--
    **Advanced Features:**
    - **Matrix Builds**: Test multiple versions/platforms simultaneously
    ```yaml
    strategy:
      matrix:
        python-version: [3.8, 3.9, '3.10', 3.11]
    runs-on: ${{ matrix.python-version }}
    ```
    - **Conditional Execution**: `if: github.ref == 'refs/heads/main'`
    - **Artifacts**: Share files between jobs (`actions/upload-artifact@v3`)
    - **Caching**: Speed up builds (`actions/cache@v3`)
    - **Services**: Docker containers for databases/testing

  deploy:
    needs: simulate
    runs-on: self-hosted # We need VPN access to the lab!
    steps:
      - uses: actions/checkout@v3
      - name: Run Ansible
        env:
          NETBOX_TOKEN: ${{ secrets.NETBOX_TOKEN }}
        run: ansible-playbook deploy.yml -i netbox_inv.yml
```

    {{1}}
> **Task**: Create this workflow. Note that `deploy` needs a `self-hosted` runner (your laptop) because GitHub cloud cannot reach your private GNS3/Lab devices.

## Capstone Part 2: Secrets

    --{{0}}--
    Never commit passwords.
    Go to GitHub Repo > Settings > Secrets.
    Add `NETBOX_TOKEN`, `DEVICE_PASSWORD`, etc.

    --{{1}}--
    Inject them as environment variables in the workflow.
    `env: PASSWORD: ${{ secrets.DEVICE_PASSWORD }}`

## Capstone Part 3: The Big Red Button

    --{{0}}--
    1.  Create a branch.
    2.  Change a YAML file (e.g., add a VLAN).
    3.  Push.
    4.  Create PR. `validate` and `simulate` run automatically.
    5.  Merge. `deploy` runs.

    --{{1}}--
    Watch the "Actions" tab in GitHub.
    Green checks are dopamine. Red Xs mean you have work to do.

## Graduation

    --{{0}}--
    If dependencies install, tests pass, and Ansible completes...
    Congratulations.
    You are no longer a CLI Artisan.
    You are a Network Reliability Engineer.

    --{{1}}--
    You have built a system that is:
    *   **Truthful** (NetBox)
    *   **Declarative** (Ansible)
    *   **Tested** (Batfish/pyATS)
    *   **Versioned** (Git)
    *   **Automated** (CI/CD)

    --{{2}}--
    Now go automate yourself out of a job.
    Rivera out.

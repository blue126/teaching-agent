<!--
author:   Alex "The Automator" Rivera
email:    alex.rivera@packetcoders.io
version:  1.0.0
language: en
narrator: English Male
comment:  Version Control. The Time Machine.
-->

# Session 10: Git & Version Control

## Introduction: No More `config_final_FINAL_v2.txt`

    --{{0}}--
    If I see a folder on your desktop named `scripts_backup_v3`, you failed the course.
    We use **Git**.

    --{{1}}--
    **The horror story:**
    You're working on a critical automation script. You make changes. It breaks. Panic.
    You try to undo your changes... but you can't remember exactly what you changed.
    You have 5 files named `script.py`, `script_old.py`, `script_working.py`, `script_FINAL.py`, `script_FINAL_USE_THIS.py`.
    Which one actually works? Nobody knows. It's 2 AM. You're re-writing from memory.

    --{{2}}--
    **With Git:**
    - Every change tracked automatically
    - Undo any change with one command: `git revert`
    - See exactly what changed: `git diff`
    - Go back to any point in time: `git checkout <commit>`
    - **No more panic. No more guessing.**

    {{2}}
> **Git**: Time travel for code. Because 'script_FINAL_v2_ACTUALLY_WORKING.py' is not a version control strategy.

    --{{3}}--
    Git captures the state of the entire project at points in time.
    It tells us **Who** changed it, **When** they changed it, and **Why**.
    And most importantly, it lets us go back.

## Lab 10.1: The Basics

    --{{0}}--
    Let's put our project under control.
    `git init`

    --{{1}}--
    We have `untracked files`.
    `git add .` (Stage them, get them ready).
    `git commit -m "Initial commit of automation repo"` (Save the state).

    --{{2}}--
    Now push it to the cloud.
    `git remote add origin https://github.com/riversne/network-automation.git`
    `git push -u origin main`

    {{1}}
> **Task**: Initialize a local git repo for your scripts. Create a `.gitignore` to exclude `__pycache__` and `.env` (don't commit secrets!). Push to GitHub.

## Lab 10.2: Branching (The Safe Space)

    --{{0}}--
    Never commit directly to `main`.
    `main` is Production. If you break `main`, you break the network.
    We work in **Branches**.

    --{{1}}--
    `git checkout -b feature/add-bgp`
    You are now in a parallel universe. You can mess up anything here, and `main` is safe.
    Make your edits. Commit.

    {{1}}
> **Task**: Create a branch `feature/update-inventory`. Add a new host to your Ansible inventory. Commit it.

## Lab 10.3: The Pull Request (PR)

    --{{0}}--
    You finished your feature. Now what?
    You **Push** the branch and open a **Pull Request**.

    --{{1}}--
    A PR is a request to merge your universe into the main universe.
    This is where the team reviews your code.
    "Hey, you forgot to update the documentation."
    "Hey, this loop is inefficient."

    --{{2}}--
    Only after approval do we click **Merge**.

    {{1}}
> **Task**: Open a PR on GitHub for your branch. Review the "File Changes" tab. Merge it.

## Lab 10.4: Conflicts (War Games)

    --{{0}}--
    Two engineers edit the same line of the same file.
    Git panics: "I don't know which one is right!"

    --{{1}}--
    You will see:
```text
<<<<<<< HEAD
hostname nyc-spine-01
=======
hostname NYC-SPINE-01
>>>>>>> feature/uppercase
```

    --{{2}}--
    It is your job to delete the markers and pick the winner.
    Then commit the fix.

    {{1}}
> **Task**: Create a conflict intentionally. Resolve it. Feel the stress. Conquer it.

## Advanced: Workflows & Strategies

    --{{0}}--
    **Feature Branch Workflow:**
    - Each feature gets its own branch
    - Short-lived (hours to days)
    - Merge to `main` when complete

    --{{1}}--
    **Gitflow Workflow:**
    - Two permanent branches: `main` (production) and `develop` (integration)
    - Supporting branches: `feature/*`, `release/*`, `hotfix/*`
    - Good for scheduled releases

    --{{2}}--
    **Trunk-Based Development:**
    - One main branch, direct commits or very short-lived branches
    - Requires strong CI/CD and testing
    - Used by Google, Facebook

    --{{3}}--
    **Merge vs Rebase:**
    - `git merge`: Creates merge commit, preserves full history
    - `git rebase`: Rewrites history, creates linear timeline
    - **Rule**: Never rebase public/shared branches

## Wrap-up

    --{{0}}--
    Git is the foundation of CI/CD.
    When you push to Git, you trigger the pipeline.
    The Pipeline runs the tests (Sessions 6, 8) and the deployment (Session 7).

    --{{1}}--
    Next Session: The Capstone. We build the Robot.

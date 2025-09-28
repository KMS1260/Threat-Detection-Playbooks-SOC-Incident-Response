# Using a Playbook

> **Scenario summary:** We will run a high‑CPU incident playbook to contain and analyse a rogue process. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Identify the offending process and verify persistence
- Contain by terminating or isolating the process
- Hash, triage, and quarantine the binary

## Tools & Techniques
- Task Manager / Process Explorer
- CLI: `tasklist`, `wmic`, `taskkill`
- PowerShell: `Get-Process`, `Stop-Process`

---

## Step 1 — Prepare & Validate
Confirm sustained CPU usage; locate the process via GUI or CLI and capture its path and parent.

---

## Step 2 — Execute & Observe
Terminate the process (`taskkill` or `Stop-Process`), confirm it does not respawn, and consider isolation.

---

## Step 3 — Verify & Capture Evidence
Hash the binary, triage via trusted services, quarantine artefacts, and remove per policy.

---

## Sample Outputs
```
taskkill /PID <pid> /F
Get-Process <name> | Stop-Process -Force
```

## Key Takeaways
- Contain first; analysis can follow
- Always record hash and file path before removal
- Quarantine artefacts for later review

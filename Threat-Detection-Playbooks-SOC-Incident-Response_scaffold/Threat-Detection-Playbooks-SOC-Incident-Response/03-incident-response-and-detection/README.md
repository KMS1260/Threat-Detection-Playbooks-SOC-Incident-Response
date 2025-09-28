# Incident Response & Detection

> **Scenario summary:** We will detect authentication abuse with a SIEM and confirm alert fidelity. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Generate realistic auth activity for baseline
- Detect failures followed by a success from the same source
- Pivot by agent and rule ID to confirm scope

## Tools & Techniques
- SIEM (e.g., Wazuh/Splunk/Sentinel)
- Test generator (e.g., `hydra` against RDP)
- KQL/SPL queries

---

## Step 1 — Prepare & Validate
Scope dashboards to the target agent/host. Establish timing for the test window.

---

## Step 2 — Execute & Observe
Run a controlled password‑guessing attempt; verify failed logons and look for a subsequent success.

---

## Step 3 — Verify & Capture Evidence
Review rule IDs, enrich with source IP and account details, and export the alert for evidence.

---

## Sample Outputs
```
hydra -t 1 -V -f -l administrator -P passlist.txt rdp://10.1.16.1
```

## Key Takeaways
- Correlate failures and success to minimise false positives
- Focus on agent/endpoint pivots for clarity
- Always export artefacts for the case record

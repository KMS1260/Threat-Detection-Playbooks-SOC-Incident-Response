# Configuring System Monitoring

> **Scenario summary:** We will configure centralised Windows Event Collection and verify subscriptions. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Enable Windows Event Collector on the collector host
- Allow the collector to read logs from the source host
- Confirm events flow and are queryable

## Tools & Techniques
- Windows Event Collector (WEC)
- WinRM, Firewall rules
- Group Policy / Local Security Policy
- PowerShell: `wecutil`, `winrm`, `Get-WinEvent`

---

## Step 1 — Prepare & Validate
On the collector, initialise WEC with `wecutil qc`, enable WinRM, and open the *Remote Event Log Management* firewall group. On the source, add the collector's computer account to **Event Log Readers**.

---

## Step 2 — Execute & Observe
Create or import a subscription on the collector. Generate a known event on the source (e.g., a test logon) and wait for collection to occur.

---

## Step 3 — Verify & Capture Evidence
Query the collector for recent events from the source using Event Viewer or `Get-WinEvent` and take a screenshot of matching Event IDs.

---

## Sample Outputs
```
wecutil qc
winrm quickconfig
Get-WinEvent -LogName Security -MaxEvents 5
```

## Key Takeaways
- Validate prerequisites before troubleshooting subscriptions
- Firewall display groups are the fastest way to open the right rules
- Use a known Event ID to test end‑to‑end flow

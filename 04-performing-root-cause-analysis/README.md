# Performing Root Cause Analysis

> **Scenario summary:** We will perform root cause analysis on DC10 audit‑policy changes dated Mar 31, 2023. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Anchor a time window around the SOC flash
- Locate audit‑policy change events and immediate precursors
- Attribute actor, source, and mechanism

## Tools & Techniques
- Windows Security Events (e.g., 4719, 4688, 4624/4648/4672)
- SIEM queries (KQL/SPL)
- PowerShell `Get-WinEvent`

---

## Step 1 — Prepare & Validate
Set a 17:30–18:30 window for 2023‑03‑31 and pull `4719`, `1102`, `4739` from DC10.

---

## Step 2 — Execute & Observe
Pivot to `4624/4648/4672` for the actor and `4688` for the execution method (e.g., `auditpol.exe`).

---

## Step 3 — Verify & Capture Evidence
Produce an RCA note: trigger, actor, mechanism, scope, impact; attach exports and screenshots.

---

## Sample Outputs
```
Event 4719 at 17:59:xx by DOMAIN\user from 10.x.x.x
4688: auditpol.exe /set ...
```

## Key Takeaways
- Time‑boxing first reduces noise and speeds attribution
- Correlate identity + process to reach a reliable root cause
- Capture evidence as you go to avoid rework

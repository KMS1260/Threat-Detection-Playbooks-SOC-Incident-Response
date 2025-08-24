# Threat Detection, Playbooks & SOC IR

A curated set of hands‑on projects focused on **threat detection**, **SOC playbooks**, and **incident response**. Each project guides the reader through realistic tasks—building monitoring, triaging alerts, investigating malware, and executing IR playbooks—using commonly available tools.

> This repository is organised for GitHub: each project has its own folder (with screenshots and a detailed lab guide). This README provides the high‑level map and outcomes.

---

## Repository Structure

```
Threat-Detection-Playbooks-SOC-IR/
├─ 01-system-monitoring/                 # Centralised Windows event collection
├─ 02-malware-analysis/                  # Sandbox & multi‑engine reputation
├─ 03-ir-detection-siem/                 # SIEM detections for auth abuse
├─ 04-root-cause-analysis/               # RCA from SOC alert → Windows logs
├─ 05-playbooks/                         # High‑CPU incident response playbook
└─ 06-threat-feeds/                      # Working with threat intel & OTX
```

---

## Quick Start

1. Pick a project below and open its lab guide in the corresponding folder.
2. Follow the steps in order; prerequisites and commands are listed inline.
3. Save evidence (screenshots, command outputs, notes) inside the project folder for auditability.

> Tip: prefer **read‑only** access to evidence images, note exact **timestamps**, and record **tool versions** for repeatability.

---

## Projects

### 1) Centralised System Monitoring (Windows Event Collection)
Build a basic **centralised logging** setup. Configure a Windows Server as an **Event Collector** and another as a **source**; enable WinRM, firewall rules, and role memberships so the collector can subscribe to the source’s logs. Validate with `wecutil qc`, `winrm quickconfig`, and test subscriptions.

**You will practise**
- Enabling and validating **Windows Event Collector**.
- Enabling **Windows Defender Firewall** rules for remote event management.
- Adding the collector computer account to **Event Log Readers** on the source.
- Using **Group Policy** and **PowerShell** to adjust WinRM listener behaviour.

**Key commands**
```powershell
wecutil qc
winrm quickconfig
Set-NetFirewallRule -DisplayGroup "Remote Event Log Management" -Enabled True -Profile Domain
Set-NetFirewallRule -DisplayGroup "Remote Event Monitor" -Enabled True -Profile Domain
# GPO registry: WinRM IPv4Filter → "*"
```

---

### 2) Detecting & Responding to Malware
Use an online **sandbox** and a multi‑engine **reputation** service to triage suspicious files/URLs. Review public analysis reports, observe ATT&CK mappings, and understand the implications of submitting samples to public portals.

**You will practise**
- Navigating a cloud sandbox before submitting samples.
- Searching public **analysis results** for known families.
- Submitting **hashes/URLs/files** to a multi‑engine scanner and interpreting results.
- Applying caution: public portals may expose **sample data** publicly.

**Notes**
- Prefer **hash** or **URL** submissions when handling corporate files.
- Avoid uploading proprietary or sensitive samples to public services.

---

### 3) IR Detection with a SIEM
Use a SIEM to detect **logon abuse** and other indicators. Generate realistic authentication events (including a controlled password‑guessing attempt), then pivot through **Security events** to identify both failures and a subsequent success. Filter by **agent**, search by **rule ID**, and confirm detections.

**You will practise**
- Scoping dashboards to a specific endpoint/agent.
- Interpreting detection **rule IDs** and timelines.
- Triggering test activity (e.g., `hydra` against RDP) to observe alerting.
- Distinguishing noisy baselines from actionable signals.

**Example generator**
```bash
hydra -t 1 -V -f -l administrator -P passlist.txt rdp://10.1.16.1
```

---

### 4) Performing Root Cause Analysis (RCA)
Start from a **SOC alert** (e.g., audit policy changes) and work backwards/forwards to establish **who**, **what**, **when**, and **from where**. Correlate SIEM hits with **Windows Event Viewer** (e.g., Event ID 4719), confirm **Logon Type**, and use `auditpol` to verify current policy state.

**You will practise**
- Time‑scoping investigations in the SIEM (absolute ranges).
- Locating relevant **event records** and correlating **record IDs**.
- Validating audit policy with:
```cmd
auditpol /get /category:*
```
- Explaining risk: removal of **Success/Failure** auditing can mask subsequent activity.

---

### 5) Using a Playbook (High‑CPU Incident)
Run a **SOC playbook** to contain a rogue process causing sustained **CPU saturation**. Identify the process, terminate it, hash and triage the binary, quarantine artefacts, and remove the file. Steps include GUI, CLI, and third‑party tools (Task Manager, `wmic`, `taskkill`, PowerShell, Sysinternals).

**You will practise**
- Process discovery (Task Manager / `wmic` / Process Explorer).
- Containment via:
```cmd
taskkill /PID <pid> /F
```
- Alternatives: PowerShell `Stop-Process`, Sysinternals `pskill`.
- Basic triage: hash, analyse via trusted services, package evidence, quarantine.

---

### 6) Working with Threat Feeds
Survey **IoC** / **threat intelligence** sources and practical portals (e.g., **AlienVault OTX** Pulses) to understand indicators, related items, and analysis views. Learn registration implications and how to search for domains, IPs, and hashes. Review exploit‑centric resources to inform detection engineering.

**You will practise**
- Exploring public IoC collections and understanding **indicator types**.
- Pivoting from an indicator to **related pulses** and artefacts.
- Identifying reputable TI sources and access considerations.

---

## Conventions & Notes

- All projects are written as **tutorials** with step‑by‑step tasks; screenshots live beside each project’s guide.
- Commands are provided exactly; adapt **paths/addresses** for your lab.
- Public services may expose submissions. Do not upload sensitive corporate data.

---

## Landing Pages & Related Work

- Main portfolio landing page: **KMS1** → https://github.com/KMS1260/KMS1  
- Related repositories:  
  - **Pentesting** → https://github.com/KMS1260/Pentesting  
  - **Digital Forensics – Data Protection Lifecycle** → https://github.com/KMS1260/Digital-Forensics-Data-Protection-Lifecycle

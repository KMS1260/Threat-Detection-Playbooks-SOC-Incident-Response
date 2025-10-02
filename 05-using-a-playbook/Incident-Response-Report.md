<strong>Incident Response Report — High CPU on PC10</strong>
Date: August 26, 2025 
 
<strong>Executive Summary</strong>  
 
On a Windows Server 2019 client (PC10), the SIEM flagged sustained CPU usage near 100%. SOAR automation was unavailable due to a recent reconfiguration, so the High-CPU IR Playbook was executed manually. The rogue process was identified as HeavyLoad.exe. The process was terminated, its executable hashed (SHA-256), the hash assessed via multiple online scanners (indeterminate), ownership inspected, the file and its hash archived, the archive safely transferred to a quarantine host (Kali), and the artifacts securely removed from the victim system. This report documents the steps, methods, and results for SOC review.  

**Environment**  

- Victim host: PC10 (Windows Server 2019; used as a client)  
- Quarantine host: KALI (Kali Linux)  
- Context: SIEM detected abnormal CPU; SOAR offline; manual playbook used.  
 
**Detection & Triage**
  
- Trigger: SIEM alert for sustained ~100% CPU on PC10.  
- IR Mode: Manual response using the organization’s High-CPU IR Playbook.  
 
**Timeline of Response (Playbook Mapping)**

<details>
<summary>1 Investigate high CPU & determine rogue process</summary>
   
Method used: Task Manager (primary), cross-checked with WMIC / Process Explorer.  

Result: Top CPU consumer identified as HeavyLoad.exe (“HeavyLoad”).  

   - Task Manager: Sort by CPU ® identify “HeavyLoad”.  
   - WMIC (admin CMD): wmic path Win32_PerfFormattedData_PerfProc_Process get Name,PercentProcessorTime ® highest % = HeavyLoad.  
   - Process Explorer: Sort CPU ® HeavyLoad on top. 
 </details>
 
<details>
<summary>2 Terminate the offending process</summary>
 
Method used: taskkill (admin CMD) (primary); GUI/PowerShell/Sysinternals also validated. 
 
Result: Process terminated and verified not running.  
 
  •- tasklist /FI "IMAGENAME eq HeavyLoad.exe" ® note PID.  
  - taskkill /PID /F ® confirms termination.  
  - Re-run tasklist to verify no instances remain.
 
</details>

<details>
<summary>3 Hash the suspicious file</summary>  

Method used: PowerShell Get-FileHash (validated with certutil / sigcheck).  

Result: SHA-256 calculated and written to a text file.  

  - Locate binary (e.g., C:\Users\HeavyLoad.exe).  
  - Get-FileHash -Path "C:\Users\HeavyLoad.exe" -Algorithm SHA256 > C:\Users\HeavyLoad-Hash.txt  
  - Sample hash for demonstration: 705204f80158bfbf9216a19bc2cb25a392ff028ce028745ac16f543b6290ec81.
 
</details>

<details>
<summary>4 Online malware analysis of the hash</summary>  

Method used: Hybrid Analysis, MetaDefender, VirusTotal (search by hash).  

Result: Indeterminate/unknown across services; proceed with containment and eradication. 
</details>

<details>
<summary>5 Determine file owner</summary>  

Method used: File Explorer (Advanced Security), dir /q, and Get-Acl.  

Result: Owner observed as SYSTEM via GUI Advanced Security; CLI views may differ depending on path/context. 
</details>

<details>
<summary>6 Archive suspicious file + hash file</summary>  

Method used: tar (admin CMD) to create a ZIP; GUI zip and Compress-Archive also validated.  

Result: Archive created at C:\HeavyLoad.zip containing HeavyLoad.exe and HeavyLoad-Hash.txt.  

  - tar -c -a -f "C:\HeavyLoad.zip" "C:\Users\HeavyLoad.exe" "C:\Users\HeavyLoad-Hash.txt"
    
</details>

<details>
<summary>7 Transfer archive to quarantine system</summary> 

Method used: Netcat (Kali) + PowerShell (PC10) raw TCP; alternatives WinSCP/SCP or SMB. 
 
Result: Archive received on Kali; integrity verified with unzip -t.  
 
  - Kali: nc -l -p 1234 > HeavyLoad.zip; then unzip -t HeavyLoad.zip ® No errors detected.  
  - PC10 (PowerShell): open TCP client to Kali 10.1.16.66:1234, stream file bytes; confirm success.
  
</details>
 
<details>
<summary>8 Remove suspicious artifacts from the victim</summary> 

 Method used: Secure wipe using Sysinternals SDelete (preferred).  

 Result: HeavyLoad.exe, HeavyLoad-Hash.txt, and any C:\HeavyLoad.zip copies securely removed.  

  - sdelete -p 3 C:\Users\HeavyLoad.exe  
  - sdelete -p 3 C:\Users\HeavyLoad-Hash.txt  
  - sdelete -p 3 C:\HeavyLoad.zip (or del if sdelete not required)
  
 </details>
 
<details>
<summary>9 Reporting</summary> 
 
 Method used: This AAR documents actions, observations, and recommendations per playbook guidance.  

 Result: Report prepared for SOC/CISO review.  
</details>

**Indicators & Artifacts** 

- Suspicious process: HeavyLoad.exe (high sustained CPU).  
- Executable path(s): Observed under C:\Users\ (also possible vendor path in lab).  
- SHA-256 (demonstration): 705204f80158bfbf9216a19bc2cb25a392ff028ce028745ac16f543b6290ec81.  
• Archive: C:\HeavyLoad.zip ® quarantined to Kali /root/quarantine/HeavyLoad.zip (zip integrity verified).  

**Tools & Commands Used (Highlights)**  

- Discovery: Task Manager; wmic ... PercentProcessorTime; Sysinternals Process Explorer.  
- Containment: taskkill /PID /F (also Stop-Process, pskill). • Hashing: Get-FileHash, certutil -hashfile, sigcheck -h.  
- Ownership: File Explorer ® Advanced; dir /q; (Get-Acl).Owner.  
- Archiving: tar -c -a -f "C:\HeavyLoad.zip" ... (also GUI zip / Compress-Archive).  
- Transfer: Kali nc -l -p 1234 > ... + PowerShell TCP send; or WinSCP (SCP); or SMB share with net use.  
- Eradication: Sysinternals sdelete -p 3; verification via directory listings.  
- Online analysis: Hybrid Analysis, MetaDefender, VirusTotal (hash search).  

**Results & Assessment** 

- Immediate impact: CPU usage normalized after termination; system responsiveness restored.  
- Threat classification: Unknown based on public hash intelligence; no consensus detection.  
- Evidence preservation: Executable + hash captured and archived; transferred to offline quarantine; integrity verified.  
- System hygiene: Suspicious files securely wiped from PC10.  

**Notable Observations / Discrepancies**  

• Owner mismatch: GUI showed SYSTEM; some CLI outputs may show Guest depending on file instance/path. For chain-of-custody, prefer a single artifact path and record its exact ACL at time of acquisition.  

**Recommendations** 

- Restore SOAR reliability and test automation for High-CPU anomalies to reduce time-to-containment.  
- Harden endpoint controls: block/alert on unapproved stress tools; enforce allow-listing where feasible.  
- Improve evidence workflow: standardize hashing tool and path logging; capture ACLs (icacls export) with hashes.  
- Adopt sdelete/equivalent as default for eradication to avoid recoverable deletes.  
- Playbook hygiene: mark a single “golden path” and document lab path differences (C:\Users\ vs. vendor install path).  

**Closure**

All playbook steps (1–9) were completed: identification, termination, hashing, external intelligence checks (indeterminate), ownership check, archiving, quarantine transfer, secure removal, and reporting to SOC. Submit this report and associated artifacts to the CISO/SOC for review and lessons-learned tracking.  

**Appendices**  

<details>
<summary>A. Key Commands (verbatim examples)</summary>  

  - wmic path Win32_PerfFormattedData_PerfProc_Process get Name,PercentProcessorTime  
  - tasklist /FI "IMAGENAME eq HeavyLoad.exe"  
  - taskkill /PID /F  
  - Get-FileHash -Path "C:\Users\HeavyLoad.exe" -Algorithm SHA256 > C:\Users\HeavyLoad-Hash.txt  
  - tar -c -a -f "C:\HeavyLoad.zip" "C:\Users\HeavyLoad.exe" "C:\Users\HeavyLoad-Hash.txt"  
  - Kali: nc -l -p 1234 > HeavyLoad.zip; unzip -t HeavyLoad.zip  
  - Secure removal: sdelete -p 3 C:\Users\HeavyLoad.exe; sdelete -p 3 C:\Users\HeavyLoad-Hash.txt; sdelete -p 3 C:\HeavyLoad.zip  
</details>
 

<details>
<summary>B. Online Intel Lookups (hash search)</summary>

Hybrid Analysis, MetaDefender, and VirusTotal were queried for the demonstration hash. No definitive malicious verdict was found; containment and eradication proceeded as a precaution.
</details>

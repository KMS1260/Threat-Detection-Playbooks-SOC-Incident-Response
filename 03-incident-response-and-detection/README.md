# Incident Response & Detection

In this case, we will learn about using an automated security platform, wazuh, to detect IoCs related to suspicious activity. This demonstrates the detection phase of an Incident Response Plan. 

As a security professional, we want to take full advantage of automation to detect and potentially respond to security violations. In this case, we will use wazuh to review security alerts (i.e., detections) related to questionable logon activity. Finally, we will delete audit logs files and then use wazuh to evaluate the detection of this abusive activity. 

the security workstation this work is going to be done in Kali Linux, is in server subnet. we will access the wazuh web interface from Kali and DC10 while performing attack simulations from Kali and DC10 against DC10. 

## Understand the environment

we will be working from a virtual machine named KALI hosting Kali Linux. This system is security workstation and is in server subnet. we will use a virtual machine named WAZUH running Ubuntu Server and supporting the wazuh security platform. we will be accessing the wazuh web interface from Kali. we will also be using a virtual machine named DC10 hosting Windows Server 2019, where we will perform attack simulations on and against.

## Objectives
- Generate realistic auth activity for baseline
- Detect failures followed by a success from the same source
- Pivot by agent and rule ID to confirm scope
- apply security principles to secure enterprise infrastructure.
- Explaining security alerting and monitoring concepts and tools.
- modify enterprise capabilities to enhance security.
- use data sources to support an investigation. 

## Table of Contents
- [1) Detecting logon events with wazuh](#1-Detecting-logon-events-with-wazuh)
- [2) Detecting anti-forensics with wazuh](#2-Detecting-anti-forensics-with-wazuh)


---

## 1) Detecting logon events with wazuh
Scope dashboards to the target agent/host. Establish timing for the test window.

---

## 2) Detecting anti-forensics with wazuh
Run a controlled password‑guessing attempt; verify failed logons and look for a subsequent success.

---



## Key Takeaways
- Correlate failures and success to minimise false positives
- Focus on agent/endpoint pivots for clarity
- Always export artefacts for the case record

# Using a Playbook

In this blog we will work through a playbook in response to a security incident. 

As a security professional, we may need to step in and perform corrective actions and implement response strategies when automated security systems are offline. In this blog we will work through a playbook focused on resolving potentially malicious code running on an internal system.

## Understanding the environment

we will be working from a virtual machine named PC10, hosting Windows Server 2019, being used as a client. And we will use a virtual machine named KALI, hosting Kali Linux.

## Objectives
- The importance of using appropriate cryptographic solutions. 
- Analyze indicators of malicious activity. 
- Explaining various activities associated with vulnerability management. 
- Explaining security alerting and monitoring concepts and tools. 
- Explaining appropriate incident response activities. 
- using data sources to support an investigation. 
- Summering elements of effective security governance. 

## Tools & Techniques
- Task Manager / Process Explorer
- CLI: `tasklist`, `wmic`, `taskkill`
- PowerShell: `Get-Process`, `Stop-Process`

## Table of Contents

[Setup](#setup)
1. [Playbook Step 1](#playbook-step-1) - <strong>Investigate High CPU usage</strong>
2. [Playbook Step 2](#playbook-step-2) - <strong>Terminate the offending process</strong>
3. [Playbook Step 3](#playbook-step-3) - <strong>Hash the suspicious file</strong>
4. [Playbook Step 4](#playbook-step-4) - <strong>online malware scan</strong>
5. [Playbook Step 5](#playbook-step-5) - <strong>Determine the owner of the suspicious file</strong>
6. [Playbook Step 6](#playbook-step-6) - <strong>Archive the Suspicious File</strong>
7. [Playbook Step 7](#playbook-step-7) - <strong>Copy the archive to a quarantine system</strong>
8. [Playbook Step 8](#playbook-step-8) - <strong>Remove the suspicious file from the victim</strong>
9. [Playbook Step 9](#playbook-step-9) - <strong>Craft a Report about the Response</strong>

---

<strong>SIEM, SOAR, and playbooks</strong>
 
The organization's SEIM solution has detected a significant increase in CPU consumption on the PC10 workstation. The level of CPU activity has been at or near 100%, which is abnormal for the PC10 system. Under normal circumstances, the SOAR solution would respond to and resolve the abnormal event automatically. However, the SOAR system is currently offline due to a recent reconfiguration and update failure. Therefore, as a security professional, we will be responding manually to this situation. 

Fortunately, a pre-crafted playbook will us through the manual response activities. An incident response (IR) consulting group wrote the organization's library of playbooks. The IR consulting group was given broad parameters for crafting the playbooks. This has resulted in playbooks with flexibility and support for a wide range of knowledge, skill, and experience levels for those needing to use them to respond to incidents. We will work through the playbook explicitly designed to deal with high CPU consumption by rogue processes. 

A playbook is a checklist of actions to perform to detect and respond to a specific type of incident. 

<details>
<summary><strong>There are several primary steps or phases in this playbook:</strong></summary>

Investigate the high CPU usage and determine the rogue process's name. 

Terminate the offending process. 

Hash the file associated with the rogue process. 

Perform an online malware analysis using the hash value of the suspicious file. 

Determine the owner of the suspicious file. 

Archive the suspicious file into a zip container along with a file of its hash value. 

Copy the zip archive of the suspicious file to a quarantine system. 

Remove the suspicious file from the affected system(s). 

Fill out an incident report and submit it to the SOC for review.
</details>

For each of these steps, there are several options to select from. The playbook steps offer CLI (command line interface) solutions, GUI (graphical user interface) choices, or even third-party utility methods. While most of the operations use native tools, some reference use of tools from third parties.  

The blog is designed so you can choose your own options for each step of the overall playbook procedure. You are welcome to go over the entire blog and make other choices, or you can work through the various choices of each playbook step before moving forward. However, there may be a need to reset the system or implement a work around to use an alternate playbook step choice. These will be defined for you at the end of each playbook step before the Check your work section. 

A playbook is a common example of responsive controls. These are controls that serve to direct corrective actions that need to be enacted after an incident has been confirmed. In a Security Operations Center (SOC), responsive controls might include several very well-defined actions to be taken by a security professional after identifying a specific issue.

## Setup

In this introductory exercise, we will log into PC10 and initiate the rogue process. 

This is a necessary step to simulate the persistent execution of a rogue process. 

Connect to the PC10 virtual machine. Sign in as Jaime 

![](./images/0.jpg)

Select **Type here to search** from the taskbar, type powershell, then select **Windows PowerShell** from the results. 

![](./images/1.png)

Enter: 
```powershell
C:\LABFILES\Playbook-Lab.ps1
```
![](./images/2.jpg)

There may be a brief presentation of an empty Windows PowerShell console while the process starts. 

If prompted about allowing an execution exception for the script, type Y, then press Enter. 

Close this Windows PowerShell console. 

**Note** If you fail to close the Windows PowerShell console, the rogue process will be a sub-process of a PowerShell process. 

The rogue process, which is the focus of this lab, should now be running. 

The rogue process will immediately begin to consume most of the CPU. This will cause the system to be sluggish. We should be able to complete the initial playbook steps. 

---

## Playbook Step 1

<strong>Investigate High CPU usage</strong>

The first step of the High-CPU IR Playbook is:

Investigate the high CPU usage and determine the rogue process's name. 

In this High-CPU IR Playbook step, we will determine which rogue process is consuming most of the CPU's resources. 

Make a selection of the method to use to accomplish this initial task. The method options are: 

GUI - using the Windows Task Manager 

CLI - using the CLI Command Prompt wmic utility 

Third-party - using the Sysinternals GUI tool Process Manager 

You can review the offered methods using the pull-down list below before making a final selection to work through. 

Security Orchestration, Automation, and Response (SOAR) is a security solution whose purpose is to scan security and threat intelligence data collected from multiple sources within the enterprise and then analyze it using various techniques. A SOAR can also assist with provisioning tasks, such as creating and deleting user accounts, making shares available, or launching VMs from templates. The SOAR will use technologies such as cloud and SDN/SDV APIs, orchestration tools, and cyber threat intelligence (CTI) feeds to integrate the different systems it manages. It will also leverage technologies such as automated malware signature creation and user and entity behavior analytics (UEBA) to detect and identify threats. The automated actions performed by a SOAR are to be documented in runbooks. However, when the SOAR fails to operate properly, security personnel can use a playbook to perform manually the actions that the SOAR would have automated. 

<details>
<summary>Choose a method</summary>

<details>
 <summary>Use GUI Task Manager</summary>

Select **Type here to search** from the taskbar, type task, then select **Task Manager** from the results. 

The Task Manager window should be displayed in details view with a menu bar and several tabs.

![](./images/3.png)

If the Task Manager is not in the details view, select **More Details** to switch to the details view. 

Select the **CPU** column to sort the processes by their CPU consumption. 

If the CPU percentages at the top of the column are 0%, then select the **CPU** column header again to reverse the sort order. 

Determine the name of the process that is consuming most of the CPU. We will note down the name 

High CPU process name: HeavyLoad 

Leave the Task Manager open. We will use it in a later playbook step. 

We have completed this playbook step using the GUI Task Manager. 

 </details>

<details>
 <summary>Use CLI Command Prompt WMIC utility</summary>
 
Select **Type here to search** from the taskbar, enter cmd, right-click **Command Prompt** from the results, then select **Run as administrator**. 

Select **Yes** on the User Account Control window. 

Enter: 
```cmd
wmic path Win32_PerfFormattedData_PerfProc_Process get Name,PercentProcessorTime. 
```
![](./images/4.jpg)

The results will be presented in an unsorted list. Scroll back up to locate the process name with the highest PercentProcessorTime.

![](./images/5.png)

The processor time is based on this system's use of 2 (virtual) CPUs. So, the total CPU process time available is 200%. 

As usual we would note down the name of the process: HeavyLoad  

Leave the Command Prompt window open. 

We have completed this playbook step using the CLI Command Prompt wmic utility. 
 
 </details>

<details>
 <summary>Use third-party GUI Process Manager</summary>

Select **Type here to search** from the taskbar, type file, then select **File Explorer** from the results. 

![](./images/6.png)

The File Explorer window should be displayed. 

![](./images/7.png)

In the left pane, select **SYSINTERNALS**. 

![](./images/8.png)

The contents of the C:\SYSINTERNALS folder will be displayed in the right pane. 

In the search field at the top right of the File Explorer window, which currently displays Search SYSINTERNALS*, enter procexp. 

![](./images/9.jpg)

There should be three results. 

Double-click **procexp64** from the search results.

![](./images/10.jpg)

The Process Explorer utility from Sysinternals should be displayed. 

![](./images/11.jpg)

Select the **CPU** column heading to sort by the use percentage. 

![](./images/12.jpg)

Scroll to the top of the listed processes to determine the process name consuming a significant amount of CPU resources. 

![](./images/13.jpg)

If the top of the CPU column is empty or shows use of "<0.01", then select the CPU column header again to reverse the sort order. 

High CPU process name: HeavyLoad 

Leave Process Explorer open. You may use it in a later playbook step. 

Leave File Explorer open. 

We have completed this playbook step using the third-party GUI utility Process Explorer from Microsoft's Sysinternals. 

Sysinternals, at sysinternals.com, is a Microsoft website that offers technical resources and utilities to manage, diagnose, troubleshoot, and monitor a Microsoft Windows environment. You can experiment with the Sysinternals tools go directly to sysinternals.com to learn more and download the suite of nearly 75 tools onto your own system. 
 
 </details>

</details>


---


## Playbook Step 2

<strong>Terminate the offending process</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use GUI Task Manager</summary>
 
![](./images/14.jpg)
![](./images/15.jpg)

</details>

<details>
 <summary>Use CLI Command Prompt tool taskkill</summary>

![](./images/16.png)
![](./images/17.jpg)
![](./images/18.jpg)
![](./images/19.png)
 
</details>

<details>
 <summary>Use CLI PowerShell cmdlet Stop-Process</summary>

![](./images/20.png)
![](./images/21.png)
![](./images/22.jpg)
![](./images/23.png)
![](./images/24.jpg)
 
</details>

<details>
 <summary>Use third-party CLI pskill utility</summary>

![](./images/25.png)
![](./images/26.png)
![](./images/27.jpg)
![](./images/28.png)
![](./images/29.png)
 
</details>

<details>
 <summary>Use third-party GUI Process Explorer</summary>

![](./images/30.jpg)
![](./images/31.png)
![](./images/32.jpg)
 
</details>
 
 </details>

---

## Playbook Step 3

<strong>Hash the suspicious file</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use CLI Command Prompt tool certutil</summary>

![](./images/33.jpg)
![](./images/34.png)
![](./images/35.png)
![](./images/36.jpg)
![](./images/37.png)
 
</details>

<details>
 <summary>Use CLI PowerShell cmdlet Get-FileHash</summary>

![](./images/38.jpg)
![](./images/39.png)
![](./images/40.png)
![](./images/41.png)
![](./images/42.jpg)
 
</details>

<details>
 <summary>Use third-party CLI tool sigcheck</summary>

![](./images/43.png)
![](./images/44.png)
![](./images/45.png)
![](./images/46.png)
 
</details>

</details>

---

## Playbook Step 4

<strong>online malware scan</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use Hybrid Analysis</summary>


![](./images/47.jpg)
![](./images/48.png)
![](./images/49.jpg)
![](./images/50.png)
![](./images/51.jpg)
 
</details>

<details>
 <summary>Use MetaDefender</summary>

![](./images/52.png)
![](./images/53.jpg)
![](./images/54.jpg)
 
</details>

<details>
 <summary>Use VirusTotal</summary>

![](./images/55.png)
![](./images/56.png)
![](./images/57.png)
![](./images/58.png)
 
</details>
 
</details>

---

## Playbook Step 5

<strong>Determine the owner of the suspicious file</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use GUI File Explorer</summary>

![](./images/59.jpg)
![](./images/60.png)
![](./images/61.png)
![](./images/62.jpg)
 
</details>

<details>
 <summary>Use CLI Command Prompt dir command</summary>

![](./images/63.jpg)
 
</details>

<details>
 <summary>Use CLI PowerShell cmdlet Get-Acl</summary>

![](./images/64.png)
 
</details>
 
</details>



---

## Playbook Step 6

<strong>Archive the Suspicious File</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use GUI File Explorer</summary>

![](./images/65.jpg)
![](./images/66.png)
![](./images/67.jpg)
![](./images/68.jpg)
![](./images/69.jpg)
![](./images/70.png)
![](./images/71.png)
 
</details>

<details>
 <summary>Use CLI Command Prompt tool tar</summary>

![](./images/72.jpg)
![](./images/73.jpg)
 
</details>

<details>
 <summary>Use CLI PowerShell cmdlet Compress-Archive</summary>

![](./images/74.jpg)
 
</details>
 
</details>

---

## Playbook Step 7

<strong>Copy the archive to a quarantine system</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use Netcat and PowerShell</summary>

![](./images/75.png)
![](./images/76.png)
![](./images/77.png)
![](./images/78.jpg)
![](./images/79.jpg)
![](./images/80.png)
![](./images/81.jpg)
![](./images/82.jpg)
![](./images/83.png)
 
</details>

<details>
 <summary>Use GUI WinSCP</summary>

![](./images/84.png)
![](./images/85.jpg)
![](./images/86.png)
![](./images/87.png)
![](./images/88.png)
![](./images/89.jpg)
![](./images/90.png)
![](./images/91.jpg)
![](./images/92.png)
![](./images/93.png)
![](./images/94.jpg)
![](./images/95.png)
![](./images/96.jpg)
![](./images/97.jpg)
 
</details>

<details>
 <summary>Use SAMBA</summary>

![](./images/98.png)
![](./images/99.png)
![](./images/100.png)
![](./images/101.png)
![](./images/102.jpg)
![](./images/103.jpg)
![](./images/104.png)
![](./images/105.jpg)
![](./images/106.jpg)
![](./images/107.jpg)
![](./images/108.jpg)
![](./images/109.png)
![](./images/110.png)
![](./images/111.png)
![](./images/112.png)
![](./images/113.png)
![](./images/114.png)
![](./images/115.png)
![](./images/116.png)
 
</details>
 
</details>

---

## Playbook Step 8

<strong>Remove the suspicious file from the victim</strong>

<details>
 <summary>Choose a method</summary>

<details>
 <summary>Use CLI Command Prompt del command</summary>

![](./images/117.png)
![](./images/118.jpg)
![](./images/119.png)
![](./images/120.jpg)
 
</details>

<details>
 <summary>Use third-party CLI SDelete utility</summary>

![](./images/121.png)
![](./images/122.jpg)
![](./images/123.png)
![](./images/122.jpg)
 
</details>

<details>
 <summary>Use GUI w/ Recycle Bin</summary>

![](./images/124.jpg)
![](./images/125.jpg)
![](./images/126.jpg)
![](./images/127.png)
![](./images/128.jpg)
![](./images/129.jpg)
![](./images/130.jpg)
 
</details>
 
</details>

---

## Playbook Step 9

<strong>Craft a Report about the Response</strong>



---


## Key Takeaways
- Contain first; analysis can follow
- Always record hash and file path before removal
- Quarantine artefacts for later review

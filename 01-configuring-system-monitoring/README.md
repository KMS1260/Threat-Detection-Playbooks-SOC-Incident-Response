# Configuring System Monitoring
This case focuses on centralized log management. As a network grows, managing the logs of the systems and applications can be complicated. Collecting logs into a single location simplifies log management. In this case, we will configure centralized logging from one Windows server system to another.

## Understand the environment
we will work from two virtual machines in this case, including a VM named DC10 hosting Windows Server 2019 and MS10 hosting Windows Server 2016.

## Objectives
- apply common security techniques to computing resources. 
- Explain security alerting and monitoring concepts and tools. 
- use data sources to support an investigation.

## Tools & Techniques
- Windows Event Collector (WEC)
- WinRM, Firewall rules
- Group Policy / Local Security Policy
- PowerShell: `wecutil`, `winrm`, `Get-WinEvent`

## Configure centralized logging 

Centralized logging is an essential service of modern security management. Having logs duplicated to a single location facilitates analysis and backup operations. In this exercise, we will configure centralized logging on Windows systems. 

Select the DC10 VM sign in as Administrator 

The DC10 virtual machine will be used as the collector and the MS10 virtual machine will be used as the logging source. 

Minimize or close Server Manager if it appears.  

Select Type here to search from the taskbar, type powershell, right-click Windows PowerShell from the results, then select Run as administrator. 

![](./images/0.png)

Select Yes on the User Account Control window. 

![](./images/1.png)

Select the empty area of the Administrator: Windows PowerShell console, then select the   below to paste the PowerShell script into the VM: 

```powershell
Import-Module GroupPolicy 
 
# Get the cc-domain-default GPO object 
$gpo = Get-GPO -Name "cc-domain-default" 
 
# Get the Group Policy Preference registry key for WinRM 
$winrmRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service" 
 
# Set the value of the "IPv4Filter" registry value to "*" 
Set-GPRegistryValue -Name $gpo.DisplayName -Key $winrmRegKey -ValueName "IPv4Filter" -Type String -Value "*" 
 
# Refresh the Group Policy settings on the local computer 
gpupdate /force 
```

Press Enter on your keyboard for the last line to be executed. we should see the message "Updating policy…" 

![](./images/4.jpg)

This PowerShell script is used to change the default setting of the WinRM listener from empty to *. The default setting acts as a deny-all, while the asterisks act like an accept-all.

When it’s complete it should display: 

- “Computer Policy update has completed successfully.” 
- “User Policy update has completed successfully.” 

![](./images/2.png)

In the Administrator: Windows PowerShell console, enter: 

```powershell
wecutil qc. 
```
When prompted by This service startup mode will be changed to Delay-Start. Would you like to proceed (Y- yes or N- no)? enter: 

```powershell
Y
```

![](./images/3.jpg)

We should see the result message of Windows Event Collector service was configured successfully. 

We have now enabled the Windows Event Collector service on DC10 (the collector). 

Close the Administrator: Command Prompt window. 

Connect to the MS10 virtual machine. 

![](./images/6.jpg)

Let's say the user jaime is a member of the Domain Admins group. So, this user account is an administrator on the MS10 system.

Restart MS10 by selecting the Start menu, select Power, select Restart, then select Continue to label the restart as Other (Unplanned).

![](./images/7.png)

This restart will ensure the VM is properly logged into the domain and the GPO settings defined by the previous script are enforced. 

Once restarted, connect to MS10 

![](./images/8.jpg)

Minimize or close Server Manager if it appears.

Select Type here to search from the taskbar, type PowerShell, right-click Windows PowerShell from the results, and then select Run as administrator. 

![](./images/9.png)

Select Yes on the User Account Control window. 

![](./images/10.jpg)

In the PowerShell console, enter: 
```powershell
Set-NetFirewallRule -DisplayGroup "Remote Event Log Management" -Enabled True -Profile Domain. 
```
![](./images/11.jpg)

In the PowerShell console, enter: 
```powershell
Set-NetFirewallRule -DisplayGroup "Remote Event Monitor" -Enabled True -Profile Domain. 
```
![](./images/12.jpg)

we have now enabled Remote Event Log Management and Remote Event Monitor through the Windows Defender Firewall using the PowerShell cmdlet Set-NetFirewallRule.

In the PowerShell console, enter: 
```powershell
winrm quickconfig.
```
![](./images/13.png)

we should see the message: WinRM service is already running on this machine. WinRM is already set up for remote management on this computer.

**Note**: If you see the message: WinRM is not set up to receive requests on this machine. The following changes must be made: Start the WinRM service. Set the WinRM service type to delayed auto start.,  

Close the PowerShell console. 

Right-click the Start menu, select Computer Management. 

![](./images/14.png)

In the Computer Management window, select Local Users and Groups, which is located under System Tools. 

![](./images/15.jpg)

In the middle pane, double-click Groups. 

![](./images/16.png)

Double-click the Event Log Readers groups. 

![](./images/17.jpg)

On the Event Log Readers Properties window, select Add. 

![](./images/18.png)

On the Select Users, Computers, Service Accounts, or Groups window, select Object Types…. 

![](./images/19.png)

On the Object Types window, select to enable the Computers checkbox, and then select OK. 

![](./images/20.png)

On the Select Users, Computers, Service Accounts, or Groups window, in the Enter the object names to select field, enter DC10, and then select OK. 

![](./images/21.png)

The Event log Readers Properties window should now show that DC10 is a member. 

![](./images/22.png)

Select OK to close the Event log Readers Properties window. 

Close the Computer Management window. 

Restart MS10 by selecting the Start menu, select Power, select Restart, then select Continue to label the restart as Other (Unplanned). 

Once the MS10 virtual machine restarts, and sign in as Jaime  

![](./images/6.png)

Once we see the MS10 desktop appear, we will leave the MS10 system as is and return to DC10. 

Switch back to the DC10 VM. If needed sign in as Administrator  

![](./images/23.jpg)

Now, we must configure an Event Viewer subscription to pull recorded event records from the source (i.e., MS10). 

Select Type here to search from the taskbar, enter Event and then select Event Viewer. 

![](./images/25.jpg)

Maximize the Event Viewer window. 

On the Event Viewer window, select Subscriptions in the left pane. 

![](./images/26.png)

In the right pane, select Create Subscription…. 

![](./images/27.png)

On the Subscription Properties window, enter Logs from MS10 in the Subscription name field. 

![](./images/28.png)

Leave the Destination Log field set to the default of Forwarded Events. 

Select the Collector Initiated radio button and then select Select Computers.

![](./images/29.png)

A collector-initiated setup for centralized logging means that the collector system pulls log information from the source system. It is also possible to set up centralized logging to be source computer initiated. In that configuration, the source computer pushes log updates to the collector system.

On the Computers window, select Add Domain Computers. 

![](./images/30.png)

On the Select Computer window, enter MS10 in the Enter the object name to select field, and then select OK.

![](./images/31.png)

we are returned to the computers window, which should now display the name MS10....., select Test

![](./images/32.png)

we should see a message stating Connectivity test succeeded, select OK to close this message window. 

![](./images/33.jpg)

Select OK to close the computers window.

On the Subscription Properties window, select Select Events…. 

![](./images/34.jpg)

On the Query Filter window, select Last 24 hours from the Logged: pull-down list. 

![](./images/35.png)

Select all five of the checkboxes in the Event level: area, which are Critical, Warning, Verbose, Error, and Information. 

![](./images/36.png)

Select the By log radio button, select the down arrow of the pull-down list, select the Windows Logs checkbox from the pull-down list, and then select somewhere outside the pull-down list to close it. The result of this selection should be a list of the Windows logs in the field showing Application, Security, Setup, System, Forwarded Events. 

![](./images/37.jpg)

Leave all other settings at their defaults, and then select OK to close the Query Filter window and return to the Subscription Properties window. 

![](./images/38.jpg)

Select OK to close the Subscription Properties window. 

![](./images/39.png)

we should now see the Logs from MS10 subscription in the list of subscriptions. 

![](./images/40.png)

Right-click the Logs from MS10 subscription, and then select Runtime Status. 

![](./images/41.jpg)

The Subscription Runtime Status window should show that it is Active. Select Close. 

![](./images/42.png)

In the left pane of Event Viewer, select the arrow beside Windows Logs to expand its contents. 

![](./images/43.jpg)

Select Forwarded Events. 

![](./images/45.png)

If the center pane remains empty after 10 seconds, select Refresh from the right pane.

If the center pane still remains empty, wait a minute or two, and then select Refresh from the right pane. 

Once the subscription is listed as Active, it can take a few minutes for events to be pulled from the source computer to the collecting computer. If no events are showing in the Forwarded Events log after five to ten minutes, you can wait longer or move on to the next exercise and return later. 

Once events are displayed in the Forwarded Events log, they will be updated regularly through regular pollings (i.e., queries) of the source system by the collector system. 

Select any event. View the General tab for information about the selected event. 

The Windows Event Viewer Subscription implements the concept of centralized or collected logging. An Event Viewer Subscription can be configured to pull logged event records from any member of the domain. Also, any member of a domain can be configured as the collecting system. 

![](./images/46.jpg)

## 📘 Quick Quiz — Windows Centralized Logging

<details>
  <summary><strong>1) What types of centralized logging management are available on Windows? (Select two)</strong></summary>

<details><summary>Client initiated</summary>❌ Incorrect</details>

<details><summary>Collector initiated</summary>✅ Correct</details>

<details><summary>Domain controller initiated</summary>❌ Incorrect</details>

<details><summary>Source computer initiated</summary>✅ Correct</details>

<details><summary>Sysconfig initiated</summary>❌ Incorrect</details>
</details>

---

<details>
  <summary><strong>2) Where in the Event Viewer can you view the events from a remote system?</strong></summary>

<details><summary>System</summary>❌ Incorrect</details>

<details><summary>Setup</summary>❌ Incorrect</details>

<details><summary>Application</summary>❌ Incorrect</details>

<details><summary>Security</summary>❌ Incorrect</details>

<details><summary>Forwarded Events</summary>✅ Correct</details>
</details>

---

<details>
  <summary><strong>3) On an event pulled from the remote system, what was listed as the <em>Computer:</em> value?</strong></summary>

<details><summary>DC10</summary>❌ Incorrect</details>

<details><summary>DC10.ad.structureality.com</summary>❌ Incorrect</details>

<details><summary>MS10</summary>❌ Incorrect</details>

<details><summary>MS10.ad.structureality.com</summary>✅ Correct</details>
</details>

---

<details>
  <summary><strong>4) What systems can be configured as a collecting system through the Windows Event Viewer Subscription mechanism?</strong></summary>

<details><summary>Linux clients</summary>❌ Incorrect</details>

<details><summary>only servers</summary>❌ Incorrect</details>

<details><summary>any domain member</summary>✅ Correct</details>

<details><summary>only domain controllers</summary>❌ Incorrect</details>
</details>

---

<details>
  <summary><strong>5) The ability to use centralized logging in Windows is enabled and configured by default.</strong></summary>

<details><summary>True</summary>❌ Incorrect</details>

<details><summary>False</summary>✅ Correct</details>
</details>




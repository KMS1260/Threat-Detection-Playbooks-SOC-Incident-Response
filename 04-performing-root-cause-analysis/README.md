# Performing Root Cause Analysis

performing a root cause analysis of a security breach is often required. This means I am responsible for performing initial management and investigation of any security alerts that take place in the network. In this case, we will investigate the cause of a recent security alert and perform root cause analysis to determine the cause of the compromise, including the identity of the perpetrator. 

## Understand the environment 

We will be working from a virtual machine named KALI hosting Kali Linux. We will perform investigations against several systems, including DC10 hosting Windows Server 2019, MS10 hosting Windows Server 2016, PC10 hosting Windows Server 2019 (being used as a client), and ROUTER-BORDER hosting OPNSense firewall. You will also be using the threat detection platform on WAZUH, which is hosting Ubuntu Server and the wazuh security platform.

## Objectives

- Common threat vectors and attack surfaces. 
- Analyse indicators of malicious activity. 
- Security alerting and monitoring concepts and tools. 
- Appropriate incident response activities. 
- Use data sources to support an investigation. 

## Tools & Techniques

- **SIEM queries (e.g., Wazuh):** time-box the window, pivot by host/rule, expand/collapse alert details.
- **Windows Event IDs to anchor facts:** **4719** (audit policy changed) and **1102** (audit log cleared). :contentReference[oaicite:0]{index=0}
- **Logon context:** use **4624** plus **Logon Type** mapping (2=Interactive, 3=Network, 10=RemoteInteractive/RDP). :contentReference[oaicite:1]{index=1}
- **Verify current auditing:** `auditpol /get /category:*` (before/after vs. 4719 timeline).
- **Network pivots (ROUTER-BORDER):** OPNsense **Firewall → Log Files → Live View** with smart filters. :contentReference[oaicite:2]{index=2}
- **Packet proof (MS10):** Wireshark display filters (e.g., `http.request.method == "POST"`). :contentReference[oaicite:3]{index=3}

## Table of Contents

1. [Investigating a security alert](#investigating-a-security-alert)
2. [Investigating the breach on DC10](#investigating-the-breach-on-dc10)
3. [Expanding the investigation to MS10](#expanding-the-investigation-to-ms10)
4. [Continuing the investigation from PC10](#continuing-the-investigation-from-pc10)
5. [Continuing the investigation on ROUTER-BORDER](#continuing-the-investigation-on-router-border)
6. [Concluding the investigation on MS10](#concluding-the-investigation-on-ms10)

---

## Investigating a security alert

It’s just before **6 PM (18:00) on Mar 31, 2023**. We receive a security flash from the SOC indicating that several **auditing policies on DC10** have been changed. This scenario is based on a simulated exploitation recorded on **3/31/2023**. We will proceed **as if today is 3/31/2023** and investigate as an on-duty SOC team. 

Connect to the **KALI virtual machine** and sign in as **root**. 

Open **Firefox** by selecting its icon from the taskbar. 

In the Firefox address bar, enter: 
```numb
10.1.16.242 
```
**Note** If an "Warning: Potential Security Risk Ahead" page is displayed when attempting to access 10.1.16.242, select **Advanced**, scroll down, and then select **Accept the Risk and Continue**. The reason for this is the wazuh security platform automatically rotates its certificates on a regular basis. Therefore, each time this happens, it will not be recognized by the browser as a known entity. 

**Note** If the wazuh log in page is not displayed, if you see an error, if you see the message Wazuh dashboard server is not ready yet, then wait a few moments, then refresh the page. 

The wazuh platform is deployed in this environment on an Ubuntu server VM named wazuh. 

Once the log in fields is presented, Log in as **admin**

![](./images/0.jpg)

A presentation of service activation progress may be displayed. This can take up to a minute to complete.

![](./images/1.png)

The wazuh home screen should be displayed. 

![](./images/2.png)

If you leave the wazuh interface idle for too long (typically 10 mins or more), the session will timeout. However, the currently displayed screen will not change, but the session will have ended. When you attempt to select another feature or function from the wazuh interface, you will be prompted to log in again.

Select **Security events** from the Security Information Management section of the wazuh home page.

![](./images/3.png)

The wazuh Security events presentation is an amalgamation of the data pulled from all systems where a wazuh agent is installed. In this lab, there is an agent on DC10 and PC10. 

Select **Show dates** from the data select field (it is beside the Refresh button).

![](./images/4.png)

Since we are working on a security breach that occurred on 3/31/2023, we need to set wazuh to display the information from that time period.

The date selection field should now display ~ a day ago -> now. 

Select ~ **a day ago**.

![](./images/5.jpg)

A time selection management window is displayed with three tabs: Absolute, Relative, and Now.

![](./images/6.jpg)

Select the **Absolute** tab. 

In the **Start date** field on the bottom of the Absolute tab, type **Mar 31, 2023 @ 00:00:00.000.**

![](./images/7.jpg)

Once you type the final zero, the calendar will automatically update to that date, and the date selection field should now display Mar 31, 2023 @ 00:00:00.000 -> now.

From the date selection field, select -> now.

![](./images/8.jpg)

Do not select the Now tab! 
A time selection management window is displayed with three tabs: Absolute, Relative, and Now. 

Select the **Absolute** tab. 

![](./images/9.jpg)

In the **End date** field on the bottom of the Absolute tab, type **Apr 1, 2023 @ 00:00:00.000.** 

![](./images/10.jpg)

Once you type the final zero, the calendar will automatically update to that date and the date selection field should now display Mar 31, 2023 @ 00:00:00.000 -> Apr 1, 2023 @ 00:00:00.000. 

Select **Update** to apply the new time settings.

![](./images/11.png)

The Refresh button changes to the Update button when you type something into the search field.). 

Select **Refresh** to update the Security events page.

![](./images/12.png)

You review the security flash message. It states that the notification relates to wazuh security alerts based on Rule ID **60112**. Type 60112 into the Search field, then select **Update**. 

![](./images/13.png)

Quick Quiz 

<details>
  <summary><strong>How many security alerts for Rule ID 60112 are present for DC10?</strong></summary>

<details><summary>23</summary>❌ Incorrect</details>

<details><summary>13</summary>❌ Incorrect</details>

<details><summary>42</summary>❌ Incorrect</details>

<details><summary>17</summary>✅ Correct — in this lab view, DC10 shows 17 alerts for rule **60112**.</details>
</details>

<sub>Note: Wazuh rules are filterable by <code>rule.id</code> (e.g., <code>60112</code>) in the Events/Threat Hunting view; counts reflect the current time window and agents selected.</sub>

Scroll down to view the list of Security alerts. Select one after another to review them.

![](./images/14.png)

After selecting an alert to expand it, select it again to collapse it.

![](./images/15.png)

In several of the Rule ID 60112 alerts, look for the data.win.eventdata.auditPolicyChanges value and the data.win.eventdata.subjectUserName value. Notice that the alerts are indicating PolicyChanges of Success removed and/or Failure removed. This is quite concerning, as this means that any events occurring after these audit changes would not be recorded in the logs. 

![](./images/16.jpg)

Disabling auditing is a common anti-forensic tactic performed by malicious entities once they obtain elevated privileges. This allows them to perform additional malicious activities without the risk of a record of those actions being created. 

Quick Quiz

<details>
  <summary><strong>What UserName is associated with the Rule ID 60112 security alerts on DC10?</strong></summary>

<details><summary>administrator</summary>❌ Incorrect</details>

<details><summary>DC10</summary>❌ Incorrect</details>

<details><summary>Jaime</summary>✅ Correct — these alerts map to Windows audit policy changes (Rule 60112) and in this case were generated under the user <em>Jaime</em>. <sub>(Rule 60112 detects Windows audit policy changes; see Wazuh docs/mappings.)</sub></details>
</details>

![](./images/17.jpg)

When you reach the bottom of the page of results, notice that only 10 rows of results are displayed per page by default. You can increase the number of events shown per page of results by selecting **Rows per page: 10**, then select **50 rows** from the pop-up list of options.

![](./images/18.png)

Find and expand the last of the Rule ID 60112 security alerts. Locate the data.win.system.eventRecordID and keep note of it.

![](./images/19.jpg)

RecordID for an Audit Change security alert: **17526** 

Be sure to collapse any alert record after reviewing it. Otherwise, it will stay open even after a new search (if it remains as a search result), which just makes scrolling more complicated. 

After considering the information from the numerous security alerts for Rule ID 60112, you realize that the changes were made by an account that has administrator privileges throughout the organization's network. This means that serious violations of company policy could have taken place through that account due to the fact that it has significant privileges on almost every system on the network. You decide to see what else might have been detected by wazuh in relation to that account. 

Scroll back to the top of the Security events page. 

In the Search field, type **jaime**, then select **Update**.

![](./images/20.png)

If the results do not update based on the new search term, select the **Update/Refresh** button a second time. 

Scroll down to locate a logon security alert related to jaime. You should see an alert related to Rule ID 92653. 

![](./images/21.jpg)

![](./images/22.png)

Quick Quiz

<details>
  <summary><strong>What type of connection is indicated in the security alert for Rule ID 92653 related to <em>jaime</em>?</strong></summary>

<details><summary>Local Workstation</summary>❌ Incorrect</details>

<details><summary>Interactive</summary>❌ Incorrect</details>

<details><summary>Network Connection</summary>❌ Incorrect</details>

<details><summary>Remote Desktop Connection (RDP)</summary>✅ Correct — Rule 92653 flags Windows logons over RDP. (Ref: Wazuh rule description shows RDP login events under 92653.)</details>
</details>

This is the logon event that occurred just before the audit policy changes. You find it a bit suspicious that so soon after establishing a connection would the user make the audit policy changes. You also find it odd that the jaime account connected to the DC10 over a remote access connection method, when normally, they perform their management tasks at the keyboard, which is the company's standard practice for managing domain controllers. Additionally, it is even more concerning that the RDP connection was made from a system with IP address 10.1.16.2, which is not the normal workstation used by Jaime. 10.1.16.2 is MS10 which is an older Windows Server. Jaime typically operates from his assigned workstation of PC10 (10.1.24.101). 

In a real-world investigation, the security specialist will already have knowledge of the organization's security policies, operational guidelines, and typical practices. This type of institutional knowledge, experience, and information is essential to provide context for the interpretation of the evidence uncovered during an investigation. 

Expand the security alert related to Rule ID 92653. Locate the data.win.system.eventRecordID and note it down 

RecordID for an RDP security alert: **17464** 

![](./images/23.jpg)

Collapse the security alert. Take note of the time of the alert 

Time of RDP security alert: **17:55:26**

![](./images/24.jpg)

Leave Firefox open to the wazuh security events page. 

After your review of the security alerts related to the audit policy changes, you decide to continue your investigation on the DC10 system. 

---

## Investigating the breach on DC10

we will now switch over to the DC10 system to continue the root cause investigation. In this part, we will determine what audit policies were changed and inspect the Security log of DC10 for more information. 

Connect to the DC10 virtual machine. sign in as Administrator. 

![](./images/25.jpg)

Minimize or close **Server Manager** if it appears. It will not be used in this exercise. 

You want to determine the state of the audit policy on DC10. 

Select **Type here to search** from the taskbar, enter **cmd**, right-click over **Command Prompt** from the results, and then select **Run as administrator**. 

![](./images/27.png)

Select **Yes** on the User Account Control window. 

Maximize the Command Prompt window. 

Enter 
```cmd
auditpol /get /category:* 
```
![](./images/28.jpg)

Scroll to view the entire list of audit policy status report lines.

![](./images/29.jpg)

Quick Quiz 

<details>
  <summary><strong>What is the status of the audit policies on DC10?</strong></summary>

<details><summary>Success and Failure</summary>❌ Incorrect</details>

<details><summary>Success</summary>❌ Incorrect</details>

<details><summary>Failure</summary>❌ Incorrect</details>

<details><summary>No Auditing</summary>✅ Correct — the current audit policy reports <em>No Auditing</em> for the relevant categories (see <code>auditpol /get /category:*</code> output).</details>
</details>

Close the Command Prompt window. 

Select **Type here to search** from the taskbar, enter **Event** and then select **Event Viewer**.

![](./images/30.png)

Maximize the Event Viewer. 

In the left pane, double-click **Windows logs** to expand it. 

![](./images/31.jpg)

In the expanded list, select **Security**.

![](./images/32.jpg)

Select the topmost event record, then select **Find…** from the right pane.

![](./images/33.jpg)

In the Find what: field type 17526, then select **Find Next**.

![](./images/34.png)

This is the Event Record ID for the first audit policy change event you pulled from the wazuh security alert. 

The Find function should have located a matching event record. Select Cancel to close the Find window. 

The event record with an Event Record ID of 17526 should be selected. 

Notice that this event record has an Event ID of 4719. The selected event record is the last in a series of event records with this same Event ID. This is the same collection of records that triggered the audit policy change security alerts in wazuh

![](./images/36.jpg)

The Event Record ID is located in the event record, but it is not displayed or viewable by default. To view the location in the event record where the Event Record ID is stored, select the Details tab, then select to expand the + **System** item, then scroll down to view the EventRecordID value line. 

An Event ID is a reference to a type of occurrence that was recorded in an event log. These are standard references established by Microsoft. An Event Record ID is a unique number assigned to each event record as it is added to the log in sequential order. 

![](./images/37.jpg)

We will take note of the time of the first of the event records related to the audit policy changes. That time is 05:56:05 PM (or 17:56:05). 

We want to view the event record of the logon event for the jaime account that occurred just before the audit policy changes. Select **Find…** from the right pane, then type 17464 into the Find what: field, then select **Find Next**. 

This is the Event Record ID for the RDP session where the jaime account connected to DC10. You pulled this number from the wazuh security alert. 

![](./images/39.png)

The Find function should have located a matching event record. Select Cancel to close the Find window. 

Note it’s very important to know logon types to find out how the device was accessed which would help a lot with the investigation here is a link to windows website explaining more https://learn.microsoft.com/en-us/windows-server/identity/securing-privileged-access/reference-tools-logon-types  
 
The event record with an Event Record ID of 17464 should be selected. Look over the information for this event record on the General tab. 

The General tab has a scrollable window of information. Be sure to scroll through this collection of details so you don't overlook something important. 

Quick Quiz 

<details>
  <summary><strong>What is the Logon Type for this event record related to the <em>jaime</em> connection over RDP?</strong></summary>

<details><summary>2</summary>❌ Incorrect — 2 = Interactive (local console).</details>

<details><summary>3</summary>❌ Incorrect — 3 = Network (non-interactive access).</details>

<details><summary>7</summary>❌ Incorrect — 7 = Unlock (workstation unlock).</details>

<details><summary>10</summary>✅ Correct — 10 = RemoteInteractive (RDP/Terminal Services). <sub>(See Microsoft/industry refs mapping Logon Type 10 to RDP.)</sub></details>
</details>

The Security log records logon events and categorizes them based on the following types: 

This record confirms what the wazuh security alert indicated specifically, that the RDP connection to DC10 was initiated from 10.1.16.2, which is the MS10 system.

![](./images/40.png)

we decide it is time to talk with Jaime directly to inquire about these events and alerts. However, before we contact HR and the physical security team, we look up the work schedule to determine whether Jaime is at work or not. The schedule shows that Jaime was at work today, but that his day likely ended at 6 PM and he may have already left. 

we decide to look up badge access uses for Jaime to see which buildings and data center rooms he entered today. The records show that Jaime only entered the building where his office is located, and there are no data center room entries recorded for him for today (3/31/2023). Since MS10 is located in a data center of a different building on the company campus, it is unlikely that Jaime was able to work from MS10. Also, we see that Jaime has already left for the day. we decide to continue to investigate the issue before contacting HR, legal, and physical security. 

we need to find more evidence to determine what happened and why. we would like to determine what happened before the RDP connection was established from MS10 to DC10. we continue the investigation on MS10. 

Leave the Event Viewer open. 

---

## Expanding the investigation to MS10

Since the RDP connection to DC10 originated from MS10, we will continue the root cause analysis and investigation from the MS10 system in this case. 

Connect to the MS10 virtual machine. select **other user and then** sign in as administrator

![](./images/41.jpg)

The default user to log into MS10 will be presented as Jaime. In this situation, this is not evidence of the violating event(s). The Jaime account is the default account for the MS10 system in the environment. 

Minimize or close **Server Manager** if it appears. It will not be used. 

Select **Type here to search** from the taskbar, enter Event and then select **Event Viewer**. 

Maximize the Event Viewer. 

In the left pane, double-click **Windows logs** to expand it. 

In the expanded list, select **Security**.

![](./images/42.jpg)

Select the topmost event record, then select **Find…** from the right pane. 

Type 5:55 into the Find what: field, then select **Find Next**.

![](./images/43.jpg)

Please ignore any events occurring after 5:55 PM on MS10. They are not relevant. 

Once the first event with a time stamp starting with 5:55 is found, change the search term to jaime in the Find what: field, then select **Find Next**. 

![](./images/44.jpg)

Select **Cancel** to close the Find window. 

The selected event record should be a Logon event with Event ID of 4648. Look over the information on the General page for this event record. 

![](./images/45.png)

Note the username from the Account Name: dylan 

RDP initiator = dylan 

We notice that the time stamp for this event record is nearly the same as that of the RDP connection event record on DC10, which was 17:55:26. This confirms that MS10 was the origin of the RDP session to DC10 and that the jaime account was used to log into DC10 over RDP. 

We now have evidence that the user that initiated the RDP session from MS10 to DC10 was not Jaime the administrator, but dylan from HR, who is a standard worker with a limited account. This means that the credentials for the jaime account were somehow obtained by dylan, and then used to connect to DC10 via RDP and disable auditing. 

Now we want to confirm that the dylan account was logged onto MS10. So, we look through the event log for an entry with an Event ID of 4624 (a logon event) and an Account name: of dylan. we discover one such event record with an Event Record ID of **4176**. 

While the event record for the RDP initiation is still selected, select Find… from the right pane, change the search term to 4176 in the **Find** what: field, then select **Find Next**. 

Select **Cancel** to close the Find window. 

![](./images/46.jpg)

The selected event record should indicate that An account was successfully logged on and that account was dylan. Review the other information presented on the General tab. 

Quick Quiz

<details>
  <summary><strong>What is the logon type for the currently selected event record related to <em>Dylan</em> and <strong>MS10</strong>?</strong></summary>

<details><summary>10</summary>❌ Incorrect — 10 = RemoteInteractive (RDP/Terminal Services). </details>

<details><summary>2</summary>✅ Correct — 2 = Interactive (console login). </details>

<details><summary>3</summary>❌ Incorrect — 3 = Network (non-interactive). </details>

<details><summary>7</summary>❌ Incorrect — 7 = Unlock (workstation unlock). </details>
</details>

<sub>Refs: Microsoft event 4624/logon-types mapping — Type **2** is Interactive; Type **3** Network; Type **10** RemoteInteractive (RDP). </sub>

We now have evidence that Dylan logged into MS10 directly. we consult the data center entry logs and see that Dylan was able to enter the data center at 5:32 PM. we check the video footage of the data center entrance at the time and see proof of Dylan entering the data center. (in a siuation like this we would need to look at evidence other than computer logs). 

We take a note of the time stamp for this logon event record of the dylan account accessing MS10. 

Time of Dylan logging into MS10: 5:48 PM 

Leave the Event Viewer window open. 

We now have evidence that Dylan was the perpetrator of the audit policy changes and that they were responsible for the RDP connection from MS10 to DC10. We have proof of their entry into the data center to access the MS10 system directly. However, we don't understand how Dylan was able to obtain login credentials for the jaime account. 

We should continue the investigation from Jaime's workstation, which is PC10.

---

## Continuing the investigation from PC10

We are now looking for anything that might reveal how the credentials for the jaime account were obtained by Dylan. We should look at Jaime's PC10 workstation. 

Connect to the PC10 virtual machine. sign in as Jaime.

![](./images/47.jpg)

As the security professional, you may be authorized to access systems throughout the network in pursuit of evidence related to security breaches. In this scenario, we are taking a shortcut to access to PC10 under the jaime account by log into the system with the jaime credentials. 

After reviewing the event log for security events and checking the malware scanner for records of malicious code discoveries, we don't find anything relevant. we decide to check Jaime's email inbox. 

We will check **Mozilla Thunderbird** from the Desktop.

![](./images/48.jpg)

While at first we are impressed by Jaime's adherence to Inbox Zero, we are curious about the single message remaining in their inbox. Select **Grab you free juice!**.

![](./images/49.png)

Immediately we suspect that this is a phishing scam email since the subject line is not using correct grammar. As we read over the message, we are now convinced that this is a scam email. Even though the source email address is one we know to be legitimate, it could easily be spoofed to give the scam message a sense of validity. 

We will position the cursor over the **System Update** link but we will not click on it.

![](./images/50.png)

Notice the URL that appears in the bottom status bar. It contains an IP address and a file named proxyset.bat. Note the IP address. 

Scam IP address: **10.1.24.142**
 
Leave the Thunderbird window open. 

Select **Type here to search** from the taskbar, enter **cmd**, then select **Command Prompt** from the results. 
 
Enter
```cmd
10.1.24.142 
```
![](./images/51.jpg)

Wait for the ping operation to complete and for the C\Users\jaime> prompt to be displayed. Notice that the results of this command show that the IP address used in the scam email is no longer present on the network. Technically, a ping check for a system is not a completely reliable means of knowing that a system is not present or does not exist. A firewall on the target can discard any ping echo requests, thus, the results look the same as when the target is not present. A more effective technique is to perform a full port scan over TCP and a full enumeration scan over UDP.  

We want to determine if the file that the URL from the scam email is present on the PC10 system.  

Enter: 
```cmd
cd c:\ && dir /s proxyset.bat
```
![](./images/52.jpg)

After a few seconds, we should see the result indicating that the proxyset.bat file is present on the system. 

We should not the absolute folder reference for where the proxyset.bat file is located: c:\Users\jaime\Downloads 

We have confirmed that Jaime received a spam email message, which included a link to download a file. Jaime must have fallen for the scam message and downloaded the file. 

Everyone is vulnerable to social engineering attacks. Even administrators can be fooled by a cleverly crafted pretext message. Don't blame the victim for falling for the scam. Blame the crafters of the attack for being malicious and using social engineering tricks to fool their targets. Social engineering remains one of the primaries means by which adversaries gain access to a secure organization's network. Attackers will use any and every opportunity to exploit an existing weakness, or they will use techniques to create a vulnerability. Social engineering is often used to trick a member of an organization into giving away information or granting logical or physical access to a secured infrastructure. We all need to be more aware of the potential to be targeted by social engineering attacks and be more skeptical of any and all communications. 
 

View the contents of the downloaded file by entering: 
```cmd
type c:\Users\jaime\Downloads\proxyset.bat
```
![](./images/53.jpg)

Based on the contents of this file, we see that it changes the proxy settings for Firefox. You want to see if Jaime executed this file. 

Select **Type here to search** from the taskbar, enter firefox, then select **Firefox** from the results. 

From the Firefox browser window, select the **Open application menu** from the toolbar (a.k.a. the hamburger menu), then select **Settings**. 

![](./images/54.png)

Scroll down to the bottom of the Settings General page. Under Network Settings select **Settings…**. 

![](./images/55.png)

![](./images/56.png)

Based on what you see on the Connection Settings you have verified that Jaime did fall for the scam email message, downloaded the batch script, and executed that downloaded script.

Quick Quiz

<details>
  <summary><strong>What is the setting selected in the Connection Settings area of Firefox?</strong></summary>

<details><summary>No proxy</summary>❌ Incorrect</details>

<details><summary>Use system proxy settings</summary>❌ Incorrect</details>

<details><summary>Automatic proxy configuration URL</summary>❌ Incorrect</details>

<details><summary>Auto-detect proxy settings for this network</summary>❌ Incorrect</details>

<details><summary>Manual proxy configuration</summary>✅ Correct</details>
</details>

<sub>Firefox’s Connection Settings include: No proxy, Auto-detect, Use system proxy settings, Manual proxy configuration, and Automatic proxy configuration URL. (See Mozilla’s docs.)</sub> :contentReference[oaicite:0]{index=0}
::contentReference[oaicite:1]{index=1}

Select **Cancel** to close the Connection Settings window of Firefox. 

Leave Firefox open. 

Switch back to Thunderbird by selecting its icon from the taskbar. Its icon is a blue phoenix around an envelope. 

Look over the scam email again. There is an encouragement to first run the System Update file, but the second inducement is to visit a URL for the Juice Shop located in the building. 

We recognize the URL of juiceshop.com as being valid. This seems odd as part of a scam message. You wonder why there would be a link to a valid site in a scam message.

![](./images/57.jpg)

Switch back to Firefox by selecting its icon from the taskbar. Its icon is the orange fox curled around a globe. 

In the Firefox address bar, enter: 
```text
juiceshop.com 
```
![](./images/58.png)

After 30 seconds or so, you will see an error of The connection has timed out. we then remember that Firefox is configured to use a proxy, which was set by the script from the scam email. Since the attempt to access this known valid URL failed, the proxy settings in use by Firefox are not currently working as expected. 

Switch back to the Command Prompt by selecting it from the taskbar.

![](./images/59.jpg)

Review the script, which should still be displayed in the Command Prompt. We notice that the proxy settings made by the script will direct all communications from Firefox to 10.1.16.2. We should recognize that IP address. That is the IP address of MS10. Thus, if Jaime did click on the Juice Shop link and reached the actual website, then there would have been a proxy function operating on MS10 at the time Jaime fell for the scam message. But, if such a proxy function was used to support Jaime's visit to the Juice Shop URL, it is not operating now. 

Leave all windows open. 

Based on the additional evidence gathered from PC10, we know that Jaime was the victim of a scam email. That scam email convinced Jaime to download and run a script. That script then changed the Firefox browser proxy settings to use 10.1.16.2 (MS10) as a proxy. We want to determine if Jaime clicked on the link to visit the Juice Shop URL. So, the next steps will take place on ROUTER-BORDER. 

---

## Continuing the investigation on ROUTER-BORDER

In an attempt to confirm whether Jaime clicked on the link to the Juice Shop website, we will continue the root cause investigation on the company's network firewall system, ROUTER-BORDER. This system is located between the private network and the internet. 

Switch to the KALI virtual machine and, if needed, sign in as root. 

We will be returning to Kali, the cybersecurity workstation, to use a web browser to access the GUI management interface of the ROUTER-BORDER system. While we could connect directly to that system, you would be limited to the CLI, and accessing the log details is significantly more cumbersome using that method. 

Open a Terminal window by selecting the **Terminal Emulator** from the Kali Linux toolbar (located at the top of the screen by default). This icon looks like a black computer screen with a cursor. 

![](./images/60.png)

In the Terminal window, enter: 
```bash
ping juiceshop.com -c 1
```
![](./images/61.jpg)

This command performs a single ping against juiceshop.com. This results in a presentation of the resolved IP address associated with that FQDN. Note the IP address resolved from juiceshop.com. 

Juice Shop IP address: 203.0.113.228 

Switch to the Firefox browser and open a new tab. 

On the new tab, enter **10.1.128.253** into the address bar. 

while working on this i came across an error message of **The connection was reset** instead of the OPNSense interface (or the "Warning: Potential Security Risk Ahead" page), I needed to perform the following steps before continuing. 

The ROUTER-BORDER firewall may become unresponsive due to the condition that its state was saved after the malicious activities were performed on 3/31/2023. When it booted for the current use, it may become unresponsive due to the time and date differences. 

We will witch to the **ROUTER-BORDER**. 

If the ROUTER-BORDER system is not operating properly, it may have an error display similar to the following screenshot: 

![](./images/62.jpg)

Press Enter to trigger the display of the login: prompt. 

If the prompt shown is instead Password:, press **Enter** again. This will result in a Login incorrect error and the presentation of the Login: prompt. 

![](./images/63.png)

Enter **root** at the Login: prompt. 

Enter the Password: prompt.

![](./images/64.png)

A menu of options will be displayed. 

Enter **6**, then enter **y** to reboot the ROUTER-BORDER system. 

![](./images/65.png)

Wait for the reboot process to complete. When we see the Login: prompt again.

![](./images/66.png)

Switch back to KALI and refresh Firefox. 

If an "Warning: Potential Security Risk Ahead" page is displayed when attempting to access 10.1.128.253, select **Advanced**, scroll down, and then select **Accept the Risk and Continue**.

![](./images/67.png)

This message appears because the certificates used by OPNSense automatically rotated on a regular basis. Therefore, each time this happens, it will not be recognized by the browser as a known entity. 

On the OPNsens login page, enter **root** in the Username: field and the Password field.

![](./images/68.png)

In the left pane, select **Firewall**, then select **Log Files** in the expanded options under Firewall, then select **Live View** in the expanded options under Log Files. 

![](./images/69.png)

At the top of the Firewall: Log Files: Live View page, there is a filtering rule configuration toolbar. Select the left field currently displaying **action**, then select **dst** from the pull-down list of options. 

![](./images/70.jpg)

The dst filter option is for the destination IP address. 

Leave the operator as contains. 

Enter **203.0.113.228** in the right field, replacing the current value of pass. 

![](./images/71.jpg)

Select + to implement the filter.

![](./images/72.jpg)

If you only see a single result, and that result is the icmp communication we performed just moments ago with the ping command against juiceshop.com, then the default search history depth is too shallow. Just above the results list, to the right, is a pull-down selector that is currently showing a value of **25**. Select **25**, then select **1000** (or if needed, **5000** or even **10000**). 

Create another filter. Select src, then set **10.1.24.101** (the IP address of PC10) as the value, then select + to implement the filter. 

The src filter option is for the source IP address. 

![](./images/73.jpg)

There should only be one result (if any) a single ICMP event. The lack of other communications indicates that no other direct connections were made from PC10 (10.1.24.101) to juiceshop.com  

(203.0.113.228) occurred. 

Next, let’s check to see if a connection to juiceshop.com from MS10 (10.1.16.2) occurred. 

Select **src~10.1.24.101** from under the filter configuration toolbar to remove it. 

Create another filter. Select **src**, then set **10.1.16.2** (the IP address of MS10) as the value, then select + to implement the filter.

![](./images/74.jpg)

There should be several results confirming that a connection to juiceshop.com (203.0.113.228) from MS10 (10.1.16.2) did occur. This is, therefore, some evidence that Jaime may have clicked on the Juice Shop link from the scam email. Since his Firefox browser was altered to use 10.1.16.2 as a proxy, his web communications would have been routed through MS10. 

Select the **information** icon on the right side of one of the filter results to open the details for the communication.

![](./images/75.jpg)

Review the details of the communication between 10.1.16.2 and 203.0.113.228. Notice the dstport value. Note the destination port number: 

Destination port number: 80 

Quick Quiz

<details>
  <summary><strong>The dstport value for one of the logged events between 10.1.16.2 and 203.0.113.228 indicates what about the transaction?</strong></summary>

<details><summary>It was an encrypted session.</summary>❌ Incorrect — encrypted web sessions typically use HTTPS on port **443**. :contentReference[oaicite:0]{index=0}</details>

<details><summary>It was an email transaction.</summary>❌ Incorrect — SMTP email commonly uses port **25** (and 587/465 for submission). :contentReference[oaicite:1]{index=1}</details>

<details><summary>It was a plaintext communication.</summary>✅ Correct — HTTP on port **80** is unencrypted/cleartext. :contentReference[oaicite:2]{index=2}</details>

<details><summary>It was an FTP session.</summary>❌ Incorrect — FTP control/data use ports **21/20** (or passive data ports). :contentReference[oaicite:3]{index=3}</details>
</details>

Notice the timestamp of the event. It likely has a value of or is similar to 2023-04-01T00:54:25

![](./images/76.jpg)

This may seem odd at first. However, the firewall uses UTC time, while the other systems and their log files use local time. The DC10, MS10, and PC10 systems use the Pacific US timezone. To convert UTC to Pacific, we must subtract 7 hours. So, the firewall's time stamp, when adjusted for the local time zone, is 2023-03-31T17:54:25 (i.e., 5:54 PM today (assuming as we are that today is 3/32/2023)). Therefore, this event fits in the timeline of the events discovered so far: 

Dylan logs into MS10 

Dylan (assumed) sent a spoofed scam email to Jaime 

Jaime downloaded and ran the malicious script from the scam email, which changed their proxy settings. 

Jaime visited the Juice Shop website by way of an unauthorized proxy service on MS10. 

Dylan logs into DC10 via RDP using jaime's credentials. Dylan disabled auditing on DC10. 

Leave all windows open. 

It seems like we have almost figured out the exploitation timeline and the TTPs (tactics, techniques, and procedures) of the attack. However, we still have not determined how Dylan obtained the credentials for the jaime account. Since the communication from PC10 to the Juice Shop website was redirected through MS10 and that connection was in plain text, we shuld have an idea of how Dylan may have accomplished credential theft. The investigation takes us back to MS10.

---

## Concluding the investigation on MS10

![](./images/25.jpg)
![](./images/77.png)
![](./images/78.png)
![](./images/79.jpg)
![](./images/80.png)
![](./images/81.jpg)
![](./images/82.jpg)
![](./images/83.png)
![](./images/84.jpg)
![](./images/85.jpg)
![](./images/86.png)
![](./images/88.png)
![](./images/89.png)
![](./images/90.png)
![](./images/91.jpg)
![](./images/92.jpg)
![](./images/93.png)

---


## Key Takeaways
- Time‑boxing first reduces noise and speeds attribution
- Correlate identity + process to reach a reliable root cause
- Capture evidence as you go to avoid rework

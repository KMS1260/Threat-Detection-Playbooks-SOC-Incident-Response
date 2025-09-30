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
2. [Investigate the breach on DC10](#investigate-the-breach-on-dc10)
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

## Investigate the breach on DC10

![](./images/25.jpg)
![](./images/27.png)
![](./images/28.jpg)
![](./images/29.jpg)
![](./images/30.png)
![](./images/31.jpg)
![](./images/32.jpg)
![](./images/33.jpg)
![](./images/34.png)
![](./images/36.jpg)
![](./images/37.jpg)
![](./images/39.png)
![](./images/40.png)

---

## Expanding the investigation to MS10

![](./images/41.jpg)
![](./images/42.jpg)
![](./images/43.jpg)
![](./images/44.jpg)
![](./images/45.png)
![](./images/46.jpg)

---

## Continuing the investigation from PC10

![](./images/47.jpg)
![](./images/48.jpg)
![](./images/49.png)
![](./images/50.png)
![](./images/51.jpg)
![](./images/52.jpg)
![](./images/53.jpg)
![](./images/54.png)
![](./images/55.png)
![](./images/56.png)
![](./images/57.jpg)
![](./images/58.png)
![](./images/59.jpg)


---

## Continuing the investigation on ROUTER-BORDER

![](./images/60.png)
![](./images/61.jpg)
![](./images/62.jpg)
![](./images/63.png)
![](./images/64.png)
![](./images/65.png)
![](./images/66.png)
![](./images/67.png)
![](./images/68.png)
![](./images/69.png)
![](./images/70.jpg)
![](./images/71.jpg)
![](./images/72.jpg)
![](./images/73.jpg)
![](./images/74.jpg)
![](./images/75.jpg)
![](./images/76.jpg)

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

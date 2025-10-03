# Working with Threat Feeds

As a security team member, you are working to improve your organization's security stance. This lab focuses on making you aware of threat feeds and other online security resources. First, we will explore IoC sources offering threat feeds. Finally, we will explore Exploit Database. 

## Objectives
- Search indicators (domains, IPs, hashes) and review pulses/collections.
- Extract related IoCs and context for detections.
- Record access/registration considerations for team use.
- Explaining various activities associated with vulnerability management. 

## Tools & Techniques
- Threat intel portals (OTX, etc.)
- Browser + CSV exports

## Table of Contents

- [Understanding IoC and threat intelligence sources](#Understanding-IoC-and-threat-intelligence-sources)
- [Explore The Exploit Database](#Explore-The-Exploit-Database)

---

## Understanding IoC and threat intelligence sources 

Sources of IoC (Indicators of Compromise) and threat intelligence feeds are available from numerous open-source community groups, commercial entities, and government agencies. An IoC source or a threat feed can provide you with actionable intelligence to defend against future attacks or discover evidence of previous compromise (i.e., threat hunting). 

However, most of these information sources require registration or a paid subscription. This is because an attacker can use the information, they provide to avoid detection, so the providers and vendors want to minimize access to this data by the malicious and limit it to those deemed benign. Therefore, these services do not lend themselves to a lab demonstration. 

Instead, in this exercise, we will view the introduction to the real-time cyber threat intelligence indicator feeds from CIS. Then, we will explore an example of an IoC report from AlienVault. 

On your local computer, open another tab in your current browser or open a new browser. 

Be sure to leave the current local browser tab open, which is focused on the GitHub. This will allow you to return to these instructions and perform additional steps. 

In your local browser's address bar, enter https://www.cisecurity.org/ms-isac/services/real-time-indicator-feeds. 

![](./images/0.jpg)

The Real-Time Indicator Feeds page from CIS: Center for Internet Security is displayed. Look over this document to learn about the information provided by CIS for free to US State, Local, Tribal, and Territorial entities (SLTTs) (i.e., US-based government entities). 

CIS is an excellent source of information. However, their feed may be limited to US SLTT entities (which usually means government). 

In your local browser's address bar, enter https://otx.alienvault.com/browse/global/pulses 

![](./images/1.jpg)

Enter mirai in the Search OTX field at the top of the page. 
```text
mirai
```
![](./images/2.png)

The results should include any AlienVault Pulse related to the Mirai botnet and its related malware. 

Pulses are collections of Indicators of Compromise, IPs, urls, and file hashes related to potentially malicious activity, contributed by the AlienVault Labs research team as well as other members of the OTX community. 

Select any of Pulse results which includes Mirai in the title (such as Mirai Botnet IOCs). 

![](./images/3.jpg)

Look over the page. Notice the coloured bar of the various TYPES OF INDICATORS.

![](./images/4.png)

<details>
  <summary><strong>On the selected AlienVault Pulse page related to Mirai, what is one of the types of indicators?</strong></summary>

<details><summary>Victim ID</summary>❌ Incorrect</details>
<details><summary>Host system</summary>❌ Incorrect</details>
<details><summary>Registrar</summary>❌ Incorrect</details>
<details><summary>FileHash</summary>✅ Correct</details>
</details>

Notice that the first 10 entries of indicators are displayed. The display of indicators is sorted by ADDED date and time by default. 

![](./images/5.jpg)

Select the item under the **INDICATOR** heading. 

![](./images/6.png)

The Analysis Overview page for the selected indicator is displayed.

![](./images/7.png)

Scroll down the page to view the Analysis tab results. 

![](./images/8.jpg)

Scroll back up and select the **Related Pulses** tab. 

![](./images/9.jpg)

This tab lists other IoCs with elements in common with this selected indicator. 

Use the **back** arrow button on the browser to return to the AlienVault Pulse page. 

In the Search: field directly above the indicator table, enter **domain**.

![](./images/10.jpg)

The results will be of indicators with domain names. 

Select any indicator to view its Analysis Overview page. Then, return to the AlienVault Pulse page using the **Back** arrow button on the browser. 

Perform additional searching using the key terms of "URL", "IPv4", "IPv6", "hostname", and "hash".

![](./images/11.png)

![](./images/12.jpg)

![](./images/13.png)

![](./images/14.png)

![](./images/15.png)

Not all of these search terms will have indicator results for the selected Pulse. 

Close the tab in your local browser focused on alienvault.com. 

<details>
<summary><strong>This exercise showed you an example of a US government IoC and threat intelligence feed description page and an older IoC page from AlienVault. There are many other IoC and threat intelligence sources to consider, but most require registration to access. Here are several to consider:</strong></summary>

- Cybersecurity and Infrastructure Security Agency: 
https://www.cisa.gov/cybersecurity 

- NIST Computer Security Resource Center: 
https://csrc.nist.gov/ 

- FBI InfraGard: 
https://www.infragard.org/ 

- SANS Internet Storm Center: 
https://isc.sans.edu/ 

- Virus Total Intelligence: 
https://www.virustotal.com/gui/intelligence-overview 

- Cisco Talos Intelligence: 
https://www.talosintelligence.com/ 

- SPAMHAUS: 
https://www.spamhaus.org/ 

- Crowdstrike: 
https://www.crowdstrike.com/products/threat-intelligence/ 

- AlienVault Open Source Threat Exchange: 
https://otx.alienvault.com/ 

- Anomali: 
https://www.anomali.com/products/threatstream 

- Mandiant: 
https://www.mandiant.com/advantage/threat-intelligence 

- Abuse.CH: 
https://abuse.ch/ 

- Free and open-source threat intelligence feeds: 
https://threatfeeds.io/ 
</details>
If you explore these URLs, open a new tab in your local browser. 

There is a community-managed list of threat intelligence sources and sites maintained on GitHub under the awesome-threat-intelligence project. You can find this project at https://github.com/hslatman/awesome-threat-intelligence 

---

## Explore The Exploit Database 

The Exploit Database is a CVE-compliant archive of public exploits and corresponding vulnerable software developed for use by penetration testers and vulnerability researchers. The Exploit Database is maintained by Offensive Security, an information security training company that provides various information security certifications and high-end penetration testing services. 

The Exploit Database is a non-profit project offered by Offensive Security as a public service. They aim to serve the most comprehensive collection of exploits gathered through direct submissions, mailing lists, and other public sources and then present them in a freely available and easy-to-navigate database. 

The Exploit Database is a repository for exploits and proofs-of-concept rather than advisories, making it a valuable resource for those who need actionable data right away. 

On your local computer, open another tab in your current browser or open a new browser. 

Be sure to leave the current local browser tab open, which is focused on the GitHub. This will allow you to return to these instructions and perform additional steps. 

In your local browser's address bar, enter https://www.exploit-db.com/

![](./images/16.jpg)

The Exploit Database main page should display listing posted exploit information in reverse chronological order (i.e., most recent at the top of the list). 

Look over the list of exploits. Notice how many of the postings are very recent. 

![](./images/17.jpg)

The default Exploits page provides the following information: 

![](./images/18.jpg)

- The *Date* column is sorted in reverse chronological posting order. 
- The *D* or *Download Exploit* column enables you to download the exploit file. 
- The *A* or *Vulnerable Application* column may include a link to download the vulnerable software (if licensing allows) 
- The *V* or *Verified* column indicates whether the site owners have verified the exploit works (with a checkmark) or have not been able to verify (with an X). Note: items that are proven not to work are removed entirely. 
- The *Title* column is a brief description of the target software and the type of exploit 
- The *Type* column indicates if the exploit is DOS, Local, Remote, or a WebApp. 
- The *Platform* column indicates the affected OS or host, such as Windows, Linux, Hardware, PHP, Python, or Multiple 
- The *Author* column indicates the entity creating or at least publishing the information. 

The most valuable feature of Exploit Database is unique compared to other exploit information sites and services - namely, the ability to download the source code of listed exploits. 

Select the **Filters** button.

![](./images/19.png)

The filter fields are displayed: Type, Platform, Author, Port, and Tag. 

Select each of the filter fields' pull-down lists to view the options. You can make selections from the pull-down lists to immediately filter the results in the main list.

![](./images/20.png)
![](./images/21.png)
![](./images/22.png)
![](./images/23.jpg)

<details>
  <summary><strong>Which of the following is NOT an option in the list of Filters for exploits on The Exploit Database?</strong></summary>

<details><summary>Code Injection</summary>✅ Present in Exploit-DB filters/tags. :contentReference[oaicite:0]{index=0}</details>

<details><summary>Authentication Bypass / Credentials Bypass</summary>✅ Present in Exploit-DB filters/tags. :contentReference[oaicite:1]{index=1}</details>

<details><summary>Heap Overflow</summary>✅ Present in Exploit-DB filters/tags. :contentReference[oaicite:2]{index=2}</details>

<details><summary>Deserialization</summary>✅ Present in Exploit-DB filters/tags. :contentReference[oaicite:3]{index=3}</details>

<details><summary>Evil Twin / Rogue Access Point</summary>❌ **Correct — NOT an Exploit-DB filter option.** (Not listed among Exploit-DB’s filter/tag categories.) :contentReference[oaicite:4]{index=4}</details>
</details>

On the left side of the page, there is a compressed side menu. Move your mouse over the side menu to expand it. 

![](./images/24.jpg)

Select **GHDB** from the side menu. 

![](./images/25.png)

The Google Hacking Database page is displayed. 

The GHDB is a collection of search expressions that can be used to find vulnerabilities in websites via Google. 

Select the **Filters** button.

![](./images/26.png)

Select the **Category** pull-down list to view all the groups/types of Google hacks (a.k.a., Google dorks). Then, select **Files Containing Passwords** from that list.

![](./images/27.png)

From the results, select any password-focused Google hacks to try. 

![](./images/28.png)

Since the collection of Google hacks is always changing, we can't indicate a specific one to try. So, pick the first one that seems interesting to you. 

A details page for the selected Google hack will be displayed. There are usually very few additional details on this page. 

![](./images/29.png)

Select the Google hack code beside the **Google Search:** indicator.

![](./images/30.png)

This should open a new browser tab to google.com with the selected Google hack as a search term. 

Look over the results. But do not visit any of the sites listed in the results at this time. 

**Note:** While visiting a site discovered through a Google search is often valid, there is always the possibility that the discovered site is malicious or hosting malicious content. Always perform Google hacking/dorking from a hardened system, such as one hosted in a VM which can be reset and restored to a known secure state in the event of malware exposure. 

Close the local browser tab focused on the google.com search results to return to the tab focused on exploit-db.com. 

There are other options to explore in the side menu. 

Other features of Exploit Database to explore from the side menu include: 

Security Papers - a collection of non-commercial papers on security and exploitation issues 

![](./images/31.png)
![](./images/32.png)

Shellcodes - ready-to-run payload exploit scripts which may be used in combination with a delivery/intrusion exploit 

![](./images/33.png)
![](./images/34.png)

SearchSploit: The Manual - a guide to using the Linux tool to search a downloaded cache of Exploit Database 

![](./images/35.png)
![](./images/36.png)

Close the tab in your local browser focused on exploit-db.com. 

---

## Quick Quiz — Threat Feeds, IoCs, OTX, Exploit-DB, Google Dorks

<details>
  <summary><strong>1) What is the goal of researching and understanding IoCs?</strong></summary>

<details><summary>Reducing resolution costs</summary>❌ Incorrect</details>
<details><summary>Improving ROSI of IAM</summary>❌ Incorrect</details>
<details><summary>Improving response efficiency</summary>❌ Helpful but not the core goal</details>
<details><summary>Detecting attempted and successful violations</summary>✅ Correct</details>

<sub>Refs: IOCs are clues used to detect and respond to breaches. :contentReference[oaicite:0]{index=0}</sub>
</details>

<details>
  <summary><strong>2) What types of entities provide threat intelligence feeds? (Select all that apply)</strong></summary>

<details><summary>Government agencies</summary>✅ Correct</details>
<details><summary>Open-source community groups</summary>✅ Correct</details>
<details><summary>Commercial organizations</summary>✅ Correct</details>
<details><summary>Dark web groups</summary>❌ Not a legitimate provider</details>

<sub>Refs: Threat intel feeds are offered by government/CERTs, open-source projects, and commercial vendors. :contentReference[oaicite:1]{index=1}</sub>
</details>

<details>
  <summary><strong>3) What indicators are used to locate IoC entries on the AlienVault (OTX) site? (Select all that apply)</strong></summary>

<details><summary>Domain</summary>✅ Correct</details>
<details><summary>FileHash</summary>✅ Correct</details>
<details><summary>IPv4 or IPv6</summary>✅ Correct</details>
<details><summary>URL</summary>✅ Correct</details>
<details><summary>MAC</summary>❌ Not an OTX indicator type</details>
<details><summary>OS type</summary>❌ Not an OTX indicator type</details>
<details><summary>Hostname</summary>✅ Correct</details>

<sub>Refs: OTX supports indicators like domain, IP (v4/v6), URL, file hash, and hostname. :contentReference[oaicite:2]{index=2}</sub>
</details>

<details>
  <summary><strong>4) What is unique about Exploit Database compared to most other exploit information sites and services?</strong></summary>

<details><summary>Access to the source code of exploits</summary>✅ Correct</details>
<details><summary>Indication of the types of targets</summary>❌ Not the key differentiator</details>
<details><summary>List new and zero-day exploits</summary>❌ Not unique to Exploit-DB</details>
<details><summary>Inclusion of exploits for hardware</summary>❌ Not the core uniqueness</details>

<sub>Refs: Exploit-DB is a public archive of **exploits with PoC/source** and related vulnerable software. :contentReference[oaicite:3]{index=3}</sub>
</details>

<details>
  <summary><strong>5) What is a Google dork?</strong></summary>

<details><summary>A type of hacker to performs OSINT using only Google searches</summary>❌ Incorrect</details>
<details><summary>A search expression which may use advanced operators to discover security issues of indexed websites through a Google search</summary>✅ Correct</details>
<details><summary>A person who does not understand how to use keywords to search for content on Google</summary>❌ Incorrect</details>
<details><summary>A listing of symbols used to alter search functions when used in Google searches</summary>❌ Incorrect</details>

<sub>Refs: “Google dorking/hacking” uses advanced operators to find exposed data/vulns. :contentReference[oaicite:4]{index=4}</sub>
</details>


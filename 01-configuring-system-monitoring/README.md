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

![](./images/0.png)
![](./images/1.png)
![](./images/2.png)
![](./images/3.jpg)
![](./images/4.jpg)
![](./images/5.png)
![](./images/6.jpg)
![](./images/7.png)
![](./images/8.jpg)
![](./images/9.png)
![](./images/10.jpg)
![](./images/11.jpg)
![](./images/12.jpg)
![](./images/13.png)
![](./images/14.png)
![](./images/15.jpg)
![](./images/16.png)
![](./images/17.jpg)
![](./images/18.png)
![](./images/19.png)
![](./images/20.png)
![](./images/21.png)
![](./images/22.png)
![](./images/23.jpg)
![](./images/24.png)
![](./images/25.jpg)
![](./images/26.png)
![](./images/27.jpg)
![](./images/28.png)
![](./images/29.png)
![](./images/30.png)
![](./images/31.png)
![](./images/32.png)
![](./images/33.jpg)
![](./images/34.jpg)
![](./images/35.png)
![](./images/36.png)
![](./images/37.jpg)
![](./images/38.jpg)
![](./images/39.png)
![](./images/40.png)
![](./images/41.jpg)
![](./images/42.png)
![](./images/43.jpg)
![](./images/44.png)
![](./images/45.png)
![](./images/46.jpg)

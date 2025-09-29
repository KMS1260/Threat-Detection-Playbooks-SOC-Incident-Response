# Performing Root Cause Analysis



## Objectives
- Anchor a time window around the SOC flash
- Locate audit‑policy change events and immediate precursors
- Attribute actor, source, and mechanism

## Tools & Techniques
- Windows Security Events (e.g., 4719, 4688, 4624/4648/4672)
- SIEM queries (KQL/SPL)
- PowerShell `Get-WinEvent`

## Table of Contents

1. [Investigating a security alert](#investigating-a-security-alert)
2. [Investigate the breach on DC10](#investigate-the-breach-on-dc10)
3. [Expanding the investigation to MS10](#expanding-the-investigation-to-ms10)
4. [Continuing the investigation from PC10](#continuing-the-investigation-from-pc10)
5. [Continuing the investigation on ROUTER-BORDER](#continuing-the-investigation-on-router-border)
6. [Concluding the investigation on MS10](#concluding-the-investigation-on-ms10)

---

## Investigating a security alert

![](./images/0.jpg)
![](./images/1.png)
![](./images/2.png)
![](./images/3.png)
![](./images/4.png)
![](./images/5.jpg)
![](./images/6.jpg)
![](./images/7.jpg)
![](./images/8.jpg)
![](./images/9.jpg)
![](./images/10.jpg)
![](./images/11.png)
![](./images/12.png)
![](./images/13.png)
![](./images/14.png)
![](./images/15.png)
![](./images/16.jpg)
![](./images/17.jpg)
![](./images/18.png)
![](./images/19.jpg)
![](./images/20.png)
![](./images/21.jpg)
![](./images/22.png)
![](./images/23.jpg)
![](./images/24.jpg)

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

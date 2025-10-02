# Using a Playbook

> **Scenario summary:** We will run a high‑CPU incident playbook to contain and analyse a rogue process. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Identify the offending process and verify persistence
- Contain by terminating or isolating the process
- Hash, triage, and quarantine the binary

## Tools & Techniques
- Task Manager / Process Explorer
- CLI: `tasklist`, `wmic`, `taskkill`
- PowerShell: `Get-Process`, `Stop-Process`

---

---

---

---


﻿![](./images/0.jpg)
![](./images/1.png)
![](./images/10.jpg)
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
![](./images/11.jpg)
![](./images/110.png)
![](./images/111.png)
![](./images/112.png)
![](./images/113.png)
![](./images/114.png)
![](./images/115.png)
![](./images/116.png)
![](./images/117.png)
![](./images/118.jpg)
![](./images/119.png)
![](./images/12.jpg)
![](./images/120.jpg)
![](./images/121.png)
![](./images/122.jpg)
![](./images/123.png)
![](./images/124.jpg)
![](./images/125.jpg)
![](./images/126.jpg)
![](./images/127.png)
![](./images/128.jpg)
![](./images/129.jpg)
![](./images/13.jpg)
![](./images/130.jpg)
![](./images/14.jpg)
![](./images/15.jpg)
![](./images/16.png)
![](./images/17.jpg)
![](./images/18.jpg)
![](./images/19.png)
![](./images/2.jpg)
![](./images/20.png)
![](./images/21.png)
![](./images/22.jpg)
![](./images/23.png)
![](./images/24.jpg)
![](./images/25.png)
![](./images/26.png)
![](./images/27.jpg)
![](./images/28.png)
![](./images/29.png)
![](./images/3.png)
![](./images/30.jpg)
![](./images/31.png)
![](./images/32.jpg)
![](./images/33.jpg)
![](./images/34.png)
![](./images/35.png)
![](./images/36.jpg)
![](./images/37.png)
![](./images/38.jpg)
![](./images/39.png)
![](./images/4.jpg)
![](./images/40.png)
![](./images/41.png)
![](./images/42.jpg)
![](./images/43.png)
![](./images/44.png)
![](./images/45.png)
![](./images/46.png)
![](./images/47.jpg)
![](./images/48.png)
![](./images/49.jpg)
![](./images/5.png)
![](./images/50.png)
![](./images/51.jpg)
![](./images/52.png)
![](./images/53.jpg)
![](./images/54.jpg)
![](./images/55.png)
![](./images/56.png)
![](./images/57.png)
![](./images/58.png)
![](./images/59.jpg)
![](./images/6.png)
![](./images/60.png)
![](./images/61.png)
![](./images/62.jpg)
![](./images/63.jpg)
![](./images/64.png)
![](./images/65.jpg)
![](./images/66.png)
![](./images/67.jpg)
![](./images/68.jpg)
![](./images/69.jpg)
![](./images/7.png)
![](./images/70.png)
![](./images/71.png)
![](./images/72.jpg)
![](./images/73.jpg)
![](./images/74.jpg)
![](./images/75.png)
![](./images/76.png)
![](./images/77.png)
![](./images/78.jpg)
![](./images/79.jpg)
![](./images/8.png)
![](./images/80.png)
![](./images/81.jpg)
![](./images/82.jpg)
![](./images/83.png)
![](./images/84.png)
![](./images/85.jpg)
![](./images/86.png)
![](./images/87.png)
![](./images/88.png)
![](./images/89.jpg)
![](./images/9.jpg)
![](./images/90.png)
![](./images/91.jpg)
![](./images/92.png)
![](./images/93.png)
![](./images/94.jpg)
![](./images/95.png)
![](./images/96.jpg)
![](./images/97.jpg)
![](./images/98.png)
![](./images/99.png)





## Key Takeaways
- Contain first; analysis can follow
- Always record hash and file path before removal
- Quarantine artefacts for later review

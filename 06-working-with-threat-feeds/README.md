# Working with Threat Feeds

> **Scenario summary:** We will pivot through public threat intel (e.g., OTX) to gather IoCs and related items. The guide is written for GitHub with step‑by‑step actions, exact commands, and screenshots stored in `./images/` (e.g., `./images/00.png`).

## Objectives
- Search indicators (domains, IPs, hashes) and review pulses/collections
- Extract related IoCs and context for detections
- Record access/registration considerations for team use

## Tools & Techniques
- Threat intel portals (OTX, etc.)
- Browser + CSV exports

---

## Step 1 — Prepare & Validate
Open the portal, search an indicator, and review the pulse/collection context.

---

## Step 2 — Execute & Observe
Export or manually copy IoCs; classify by type (domain, IP, URL, hash).

---

## Step 3 — Verify & Capture Evidence
Note caveats (rate limits, API keys) and add follow‑up detection ideas.

---

## Sample Outputs
```
domain: example[.]com
hash: 0123...abcd
```

## Key Takeaways
- Threat intel is a starting point; always verify in your logs
- Normalise IoCs for easy import into detections
- Document access controls and API usage for repeatability

# Configuring System Monitoring

> **Scenario summary:** We will configure centralised Windows Event Collection (WEC) so that **DC10** (collector) pulls logs from **MS10** (source). Steps use Event Viewer, Group Policy, WinRM, and PowerShell; screenshots should be saved under `./images/` (e.g., `./images/00.png`).

## Understand the Environment
- **VMs (domain joined):**
  - **DC10 – Windows Server 2019** → **Event Collector**.
  - **MS10 – Windows Server 2016** → **Source/Forwarder**.
- **Accounts & roles:**
  - Sign in to **DC10** as **Administrator** to configure domain settings.
  - **Jaime** (Domain Admins) has admin rights on **MS10**.
- **Connectivity & policy pre‑requisites:**
  - **WinRM** listener on domain members must accept connections (GPO sets `WinRM\Service\IPv4Filter` to `*`).
  - **DC10** must be a member of **Event Log Readers** on **MS10**.
  - **Windows Defender Firewall** on **MS10** must allow *Remote Event Log Management* and *Remote Event Monitor*.
  - A reboot of **MS10** may be required so GPO and group membership apply.

## Objectives
- Apply common security techniques to centralise Windows telemetry for security operations.
- Explain alerting & monitoring concepts by implementing **Windows Event Collection** end‑to‑end.
- Use **Forwarded Events** as a data source to support investigations.
- Practically deliver:
  - Enable **Windows Event Collector** on **DC10**.
  - Configure **WinRM** and **firewall rules** on **MS10**.
  - Grant **Event Log Readers** membership (add **DC10** to the local group on **MS10**).
  - Create a **collector‑initiated** subscription (**Logs from MS10**) that queries **Last 24 hours** across **Windows Logs** at all levels (Critical, Warning, Verbose, Error, Information).
  - Verify events flow to **Windows Logs → Forwarded Events** on **DC10**.

## Tools & Techniques
- **Group Policy (DC10):**
  - PowerShell (run as admin):
    ```powershell
    Import-Module GroupPolicy
    $gpo = Get-GPO -Name "cc-domain-default"
    $winrmRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service"
    Set-GPRegistryValue -Name $gpo.DisplayName -Key $winrmRegKey -ValueName "IPv4Filter" -Type String -Value "*"
    gpupdate /force
    ```
  - Purpose: change WinRM listener default from *deny‑all* (empty) to **accept‑all** (`*`) so the collector can reach sources.
- **Windows Event Collector (DC10):**
  ```powershell
  wecutil qc
  ```
  - Accept the change to **Delayed Start** when prompted. Expect: *Windows Event Collector service was configured successfully*.
- **Windows Defender Firewall (MS10):**
  ```powershell
  Set-NetFirewallRule -DisplayGroup "Remote Event Log Management" -Enabled True -Profile Domain
  Set-NetFirewallRule -DisplayGroup "Remote Event Monitor" -Enabled True -Profile Domain
  ```
- **WinRM (MS10):**
  ```powershell
  winrm quickconfig
  ```
  - If WinRM is not set up, follow prompts to start and set to delayed auto‑start.
- **Local Group Membership (MS10):**
  - *Computer Management* → **Local Users and Groups** → **Groups** → **Event Log Readers** → **Add…** → **Object Types…** → check **Computers** → add **DC10**.
- **Event Viewer Subscription (DC10):**
  - *Event Viewer* → **Subscriptions** → **Create Subscription…**
  - **Subscription name:** *Logs from MS10*; **Destination Log:** *Forwarded Events*; **Collector initiated** → **Select Computers…** → add **MS10** → **Test** (expect *Connectivity test succeeded*).
  - **Select Events…** → **Logged:** *Last 24 hours* → **Event level:** enable *Critical, Warning, Verbose, Error, Information* → **By log** = *Windows Logs* (Application, Security, Setup, System, Forwarded Events).
  - **Runtime Status:** should show **Active** after creation.
- **Verification:**
  - *Windows Logs → Forwarded Events* on **DC10** should begin to populate. If empty, click **Refresh** and allow several minutes for the first poll cycle.

---

## Next Steps (Troubleshooting & Validation)
- Confirm GPO applied (`gpupdate /force` or wait for policy refresh; reboot **MS10** if needed).
- Ensure **WinRM** service is running and listening; re‑run `winrm quickconfig` if required.
- Recheck **Event Log Readers** membership and domain replication.
- Validate network reachability (firewall groups enabled, no blocking between hosts).

> Save screenshots and command outputs to `./images/` and reference them inline in this guide.

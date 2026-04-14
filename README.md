
Technical analysis and mitigation strategy for a PSEXEC lateral movement alert, completed as part of the ReliaQuest SOC Analyst simulation labs.

# Incident Analysis: PSEXEC Pivoting & Credential Harvesting

## Lab Context
**Platform:** ReliaQuest Grey Matter (Simulation)

**Scenario:** Blind Investigation & Root Cause Analysis. Tasked with identifying the entry point and full attack path of a PSEXEC pivoting alert using the Grey Matter platform.

## Executive Summary
**Date:** March 6, 2024  
**Analyst:** Jack Hessefort  
**Host(s) Involved:** WORKSTATION-01, TARGET-SRV-01  
**User Account:** [REDACTED_USER]@company.local  

### Overview
This investigation followed a high-severity alert for **PSEXEC Pivoting with Process Execution**. The analysis confirmed a successful phishing attack leading to lateral movement and credential harvesting attempts.

---

## Technical Narrative
1. **Initial Access & Execution:** At 08:28, the user `[REDACTED_USER]` accessed a malicious URL via Outlook (`icedrive.net...IMPRESSED_BANANA.exe`). The file was executed at 08:38, spawning a command shell.
2. **Lateral Movement:** The malicious process used **PSEXEC** to pivot from `WORKSTATION-01` to `TARGET-SRV-01`, dropping a secondary payload: `C:\ProgramData\WARFARE_GLACIAL.exe`.
3. **Post-Exploitation (Credential Access):** Once the connection was established on the target machine, the following commands were executed to harvest credentials:
   - `cmdkey /list` (Targeting stored usernames/passwords)
   - `vaultcmd /list` (Targeting Windows Vault)
   - `reg query HKLM /f pass /t REG_SZ /s` (Searching registry for plaintext passwords)

---

## Detection Logic (Grey Matter)
The alert was triggered based on **Event Code 4688** (Process Creation) matching known administrative tool abuse patterns:
- **Parent Process:** `psexesvc.exe`
- **Command Line includes:** `\\` (Remote naming convention)
- **Tools:** `psexec.exe`, `paexec.exe`

## Mitigation & Recommendations
- **Immediate Action:** Disable/Lock `[REDACTED_USER]` account; Isolate `WORKSTATION-01` and `TARGET-SRV-01`.
- **Eradication:** Delete `IMPRESSED_BANANA.exe` and `WARFARE_GLACIAL.exe` based on file hashes.
- **Network Defense:** Block the malicious Icedrive URL at the firewall/web proxy level.
- **Email Security:** Purge Email ID `643858ac42bbe_681aa42289a4@169.254.172.1.mail` from all inboxes.

  ---
  
## Skills Demonstrated
* **Toolsets:** ReliaQuest Grey Matter, Windows Event Logs (SIEM/XDR analysis).
* **Investigation:** Pivot analysis, process tree visualization, and timeline construction.
* **Incident Response:** Containment, eradication, and strategic remediation recommendations.

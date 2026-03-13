# Lab Task 4: Alert Triage with Threat Intelligence 
**Tools:** Wazuh, VirusTotal API, AlienVault OTX  

---

## 4.1 Triage Simulation: Suspicious PowerShell Execution
**Objective:** Perform systematic triage on an endpoint alert to determine if the activity is a benign administrative action or a malicious "Living off the Land" (LotL) attack.

### Triage Context:
The Wazuh agent on a Windows endpoint triggered a high-severity alert for an obfuscated PowerShell command. The initial triage focuses on identifying the source, the user context, and the nature of the script.

### Triage Documentation Table:
| Alert ID | Description            | Source IP      | Priority | Status |
|----------|------------------------|----------------|----------|--------|
| 004      | PowerShell Execution   | 192.168.1.101  | High     | Open   |

**Analyst Note:** The PowerShell process was spawned by `explorer.exe` rather than a management service, increasing the suspicion of a user-initiated malicious script (e.g., a macro-enabled document or a downloaded payload).

---

## 4.2 IOC Validation & Intelligence Cross-Referencing
**Objective:** Use external Threat Intelligence (TI) to validate indicators of compromise (IOCs) such as file hashes or destination IPs found during the triage.

### Validation Process:
1. **Hash Extraction:** Extracted the SHA256 hash of the script block executed by PowerShell from the Wazuh `sysmon` event logs.
2. **VirusTotal Query:** Checked the hash against the VirusTotal database.
3. **OTX Cross-Reference:** Searched the Destination IP found in the PowerShell network socket activity within AlienVault OTX pulses.

### Validation Findings Summary:
> "Validation confirmed the PowerShell script utilized an encoded 'DownloadString' command. VirusTotal flagged the associated file hash as 'Malicious' (45/72 detections) linked to the Cobalt Strike beacon. AlienVault OTX correlated the destination IP with a known C2 server in the 'GoldenJackal' threat actor pulse. Result: **True Positive.**"

---

## 4.3 Triage Decision (Disposition)
- **Classification:** True Positive (Malicious Activity)
- **Incident Mapping:** [T1059.001 - Command and Scripting Interpreter: PowerShell](https://attack.mitre.org/techniques/T1059/001/)
- **Next Action:** Immediate host isolation and memory acquisition.

---

## 4.4 Verification & Success Criteria
- [x] Alert successfully captured in the Wazuh `Alerts` dashboard.
- [x] External API lookups (VirusTotal/OTX) were performed and documented.
- [x] Disposition (True/False Positive) was assigned based on evidence.

**Common Error Noted:** Analysts often focus only on the IP. I noted during this lab that checking the **Parent Process** (e.g., `cmd.exe` vs `services.exe`) is equally important for determining if a PowerShell execution is authorized or part of an exploit chain.

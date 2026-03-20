# 4: Alert Triage with Automation 
**Tools:** Wazuh (SIEM), TheHive (Case Management), VirusTotal (Threat Intel API)  

---

## 4.1 Triage Simulation: Suspicious File Download
**Objective:** Perform initial triage on endpoint telemetry to identify potential malware ingress via non-standard browser behavior.

### Triage Context:
A Wazuh agent on a Linux workstation triggered an alert for a file download into a hidden directory (`/tmp/.hidden/`). This behavior is highly indicative of a dropper script or persistent threat actor activity.

### Triage Documentation Table:
| Alert ID | Description            | Source IP      | Priority | Status |
|----------|------------------------|----------------|----------|--------|
| 005      | Suspicious File Download| 192.168.1.102  | High     | Open   |

**Analyst Note:** The source IP `192.168.1.102` was observed initiating an outbound `wget` request to an external domain. The file was immediately moved to a hidden folder, bypassing standard user downloads directories.

---

## 4.2 Automated Validation Workflow
**Objective:** Reduce manual lookup time by automating the cross-referencing of file hashes against global malware databases.

### Automation Implementation:
1. **Wazuh-TheHive Integration:** Configured the `ossec.conf` on the Wazuh manager to automatically forward all alerts with a level > 10 to TheHive.
2. **Cortex Analyzer Trigger:** In TheHive, I enabled the **VirusTotal_GetReport** analyzer. 
3. **Logic:** When the alert reaches TheHive, the system extracts the `md5/sha256` hash from the Wazuh payload and automatically queries the VirusTotal API.

---

## 4.3 Results Summary (Automated Findings)
> "Automated validation via TheHive’s VirusTotal analyzer returned a detection ratio of 58/72 for the downloaded file hash. The file was identified as a variant of the 'XMRig' cryptominer. Automation successfully removed the need for manual hash extraction, allowing the analyst to move directly to the containment phase within seconds."

---

## 4.4 Verification & Success Criteria
- [x] Wazuh successfully captured the `syscheck` event for the file creation.
- [x] TheHive automatically created an 'Observable' for the file hash.
- [x] VirusTotal report was successfully attached to the case without analyst intervention.

**Common Error Noted:** During configuration, the VirusTotal API key reached its daily request limit. I implemented a 'caching' policy in TheHive to prevent repeated lookups of the same hash within a 24-hour window, preserving our API quota for unique threats.

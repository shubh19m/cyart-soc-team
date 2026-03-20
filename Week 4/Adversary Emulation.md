# 6: Adversary Emulation Practice  
**Tools:** MITRE Caldera (Emulation), Wazuh (Detection & SIEM)  

---

## 6.1 Emulation Objective (MITRE T1566)
**Objective:** Simulate a **Spearphishing Attachment (T1566.001)** to validate the efficacy of endpoint security policies and Wazuh's ability to detect suspicious file drops and process execution.

### Execution Methodology:
1. **Caldera Setup:** Configured an 'Ability' in MITRE Caldera to simulate a user downloading and executing a malicious `.docm` file containing a macro.
2. **Operation:** Launched a "Spearphishing Simulation" operation against the target Windows agent.
3. **Telemetry Check:** Monitored the Wazuh dashboard for File Integrity Monitoring (FIM) and Sysmon process creation events.

---

## 6.2 Detection Status & Telemetry
| Timestamp            | TTP         | Detection Status | Notes                                     |
|----------------------|-------------|------------------|-------------------------------------------|
| 2025-08-18 17:00:00  | T1566       | **Detected** | Phishing attachment identified by Wazuh FIM |

---

## 6.3 Emulation Report
**Summary of Results:**
> "The adversary emulation exercise successfully triggered a 'High' severity alert in Wazuh. The simulation utilized MITRE Caldera to drop a payload mimicking a spearphishing attachment (T1566). While the initial file creation was detected via Sysmon and Rule 554 (File Ingress), a critical detection gap was identified: the subsequent 'outbound beaconing' from the macro-spawned process was not immediately flagged. This indicates that while our endpoint monitoring is strong, our network egress filtering requires tighter integration. Future improvements include deploying Wazuh rules specifically for anomalous parent-child process relationships (e.g., Word spawning PowerShell)."

---

## 6.4 Gap Analysis & Remediation
- **Strengths:** Wazuh's FIM (File Integrity Monitoring) immediately identified the creation of the malicious file in the `Downloads` directory.
- **Gaps:** The delay between execution and network-based alerting (C2 callback) exceeded the 5-minute threshold.
- **Remediation Action:** Implement a custom Wazuh decoder to monitor `Sysmon Event ID 22` (DNS Query) specifically for processes initiated by the MS Office suite.

---

## 4. Verification & Success Criteria
- [x] Caldera agent successfully executed the atomic test.
- [x] Wazuh manager generated a human-readable alert for the TTP.
- [x] Detection gaps were documented and remediation steps proposed.

**Common Error Noted:**

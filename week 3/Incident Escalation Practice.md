# Lab Task 3: Incident Escalation Practice
**Tools:** TheHive 5, Google Docs (Reporting), Splunk Phantom/SOAR Concepts  

---

## 3.1 Escalation Simulation (TheHive)
**Objective:** Formalize a technical alert into a managed incident and escalate to senior analysts (Tier 2/3) based on severity.

### Execution Steps:
1. **Case Creation:** Generated Case #2026-01: "Unauthorized Access - Metasploitable VM".
2. **Priority Setting:** Assigned **High Severity** due to confirmed successful authentication from a malicious IP.
3. **Tier 2 Escalation Summary:**
> "At 13:00, automated alerts flagged a successful SSH login on Server-Y (192.168.1.15) via account 'msfadmin'. The source IP (192.168.1.200) is currently flagged in our AlienVault OTX feed as a known C2 relay. Preliminary triage shows unauthorized 'sudo' execution and modification of the `.bash_history` file. Tier 1 has performed initial network isolation. Escalating to Tier 2 for full memory forensics and persistent threat investigation to determine if a rootkit was deployed."

---

## 3.2 SITREP (Situation Report) Draft
**Objective:** Provide an executive-level summary of the incident for management and stakeholders.

### SITREP: Unauthorized Access on Server-Y
**Date/Time:** 2026-03-13 15:30  
**Incident ID:** INC-2026-0818-01  
**Status:** Contained / Under Investigation  

**Summary:** Detection of unauthorized access occurred on 2026-03-13 at 13:00. Attacker utilized compromised credentials to access Server-Y from IP 192.168.1.200. This correlates with MITRE Technique **T1078 (Valid Accounts)**.

**Actions Taken:** - **Identification:** Confirmed breach via Wazuh log correlation.
- **Containment:** Server-Y was logically isolated from the production VLAN via firewall rules.
- **Eradication:** Passwords for 'msfadmin' and 'root' have been reset across the environment.
- **Escalation:** Incident moved to Tier 2 for deep-dive forensic analysis.

**Next Steps:** Recover server from the last known clean backup after forensic imaging is complete.

---

## 3.3 Workflow Automation (SOAR Concept)
**Objective:** Reduce "Mean Time to Respond" (MTTR) by automating the ticket assignment process.

### Automation Logic (Splunk Phantom/SOAR):
- **Trigger:** New Alert in Wazuh where `severity >= 12` AND `rule.group == "authentication_failed"`.
- **Action 1:** Query AlienVault OTX for IP Reputation.
- **Action 2:** If Reputation == "Malicious", Create Case in TheHive.
- **Action 3:** Set Case Severity to "High" and Auto-Assign to `Tier2_OnCall_Queue`.
- **Action 4:** Post notification to SOC Slack/Teams channel.

### Validation:
Tested with a mock high-priority alert. The SOAR playbook successfully bypassed the Tier 1 manual queue and placed the incident directly in the Tier 2 dashboard within 4 seconds of alert generation.

---

## 4. Verification & Success Criteria
- [x] High-priority case is visible in TheHive with appropriate tags (T1078).
- [x] SITREP contains all necessary NIST phase details (Identification, Containment).
- [x] Automation logic addresses the "Human Bottleneck" in the escalation chain.

**Common Error Noted:** During the SITREP draft, I initially omitted the MITRE ID. In a real-world SOC, this is critical for trend analysis and executive reporting, so it was added to the final template to ensure alignment with industry standards.

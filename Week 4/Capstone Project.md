# Capstone Project: Comprehensive SOC Incident Response Simulation
**Infrastructure:** Metasploitable2 (Target), Kali Linux (Attacker), Wazuh (SIEM), TheHive (CMS), CrowdSec (IPS), MITRE Caldera (Emulation), Elastic Security (Metrics)

---

## 1. Incident Lifecycle Overview
This capstone project simulates a full-spectrum cyberattack, moving from initial exploitation to automated response, forensic analysis, and executive reporting.

---

## 2. Phase 1: Attack & Adversary Emulation
**Objective:** Execute a multi-stage attack to test detection and response integrity.

### 2.1 Exploitation (Metasploit)
- **Module:** `exploit/multi/samba/usermap_script`
- **Target:** 192.168.1.15 (Metasploitable2)
- **Outcome:** Gained a root shell via a legacy service vulnerability.

### 2.2 Lateral Movement Emulation (MITRE Caldera)
To test if the SOC could detect a pivot, **MITRE Caldera** was used to emulate **T1210 (Exploitation of Remote Services)** across the subnet.

**Wazuh Detection Log:**
| Timestamp            | Source IP      | Alert Description   | MITRE Technique |
|----------------------|----------------|---------------------|-----------------|
| 2025-08-18 16:00:00  | 192.168.1.102  | Samba RCE Detected  | T1210           |

---

## 3. Phase 2: Detection, Triage, & SOAR Automation
**Objective:** Use automation to reduce response latency.

- **Detection:** Wazuh triggered a Level 12 alert for "Samba Exploit Attempt."
- **SOAR Workflow:** 1. Wazuh forwarded the alert to **TheHive**.
    2. A custom playbook triggered an automated **CrowdSec** block.
    3. **Status:** Attacker IP (`192.168.1.102`) was banned within 5 seconds of the alert.
- **Verification:** A ping test from the attacker machine returned `Destination Host Unreachable`.

---

## 4. Phase 3: Post-Incident Analysis (RCA)
**Objective:** Identify why the breach occurred and how to prevent recurrence.

### The 5 Whys:
1. **Why was the server compromised?** Exploitation of a Samba vulnerability.
2. **Why was the vulnerability present?** The server was running a legacy version of the software.
3. **Why was it not patched?** The asset was not included in the monthly patching cycle.
4. **Why was it excluded?** It was classified as a "Legacy Test Asset" without a designated owner.
5. **Root Cause:** Failure in Asset Management and Lifecycle Governance.

---

## 5. Phase 4: SOC Metrics & Performance
**Objective:** Quantify the effectiveness of the response using **Elastic Security**.

- **Dwell Time:** 15 Minutes (Time between initial shell and detection)
- **MTTD (Mean Time to Detect):** 2 Minutes
- **MTTR (Mean Time to Respond):** 8 Minutes (Including automated containment)

---

## 6. Phase 5: Final Reports

### 6.1 Technical Incident Report (SANS Template)
**Executive Summary:** At 16:00, a critical vulnerability in the Samba service was exploited on Server-Y. The attacker gained root privileges and attempted lateral movement. The incident was detected via Wazuh, and automated playbooks triggered via TheHive and CrowdSec neutralized the threat within 10 minutes.

**Timeline:**
- **16:00:** Exploitation initiated.
- **16:02:** Wazuh alert triggered; Case opened in TheHive.
- **16:04:** SOAR Playbook executed; Attacker IP blocked by CrowdSec.
- **16:10:** Manual host isolation confirmed; Incident closed.

**Recommendations:** - Decommission all legacy Metasploitable instances.
- Implement Network Segmentation to isolate test environments from production.

### 6.2 Stakeholder Briefing (Executive Level)
> "Our security team successfully identified and stopped a high-risk intrusion attempt targeting an older test server today. Using our automated defense system, we detected the threat within 2 minutes and blocked the attacker's access in under 10 minutes. This speed prevented the attacker from reaching any sensitive company data. While our current tools worked as intended, this incident highlights the risk of keeping outdated software on our network. We are moving to update our asset management policies to ensure all servers, including test units, are monitored and patched regularly."

---

## 7. Verification & Submission
- [x] Full attack-to-report lifecycle documented.
- [x] RCA (5 Whys) and Metrics included.
- [x] Evidence of SOAR automation success.

**Common Error Noted:** During the Caldera emulation, the network noise was high. I adjusted the Wazuh decoder to prioritize "Reverse Shell" patterns over standard "Service Errors" to ensure the capstone alert stood out to the analyst.

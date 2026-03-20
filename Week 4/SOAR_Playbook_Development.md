# 2: SOAR Playbook Development 
**Tools:** Splunk Phantom (SOAR), Wazuh (SIEM), CrowdSec (Remediation), TheHive (CMS)  

---

## 2.1 Playbook Objective
**Objective:** Automate the containment of malicious external actors identified in phishing alerts to reduce Mean Time to Respond (MTTR) and eliminate manual triage for high-confidence threats.

### Playbook Logic Flow:
1. **Ingestion:** Receive an alert from Wazuh regarding a "Suspicious URL/IP" in an email header.
2. **Enrichment:** Extract the `source.ip` and query **AlienVault OTX/VirusTotal** for reputation scoring.
3. **Decision:** If the reputation score is > 70 (Malicious), proceed to containment.
4. **Containment:** Execute a "block" action via the **CrowdSec** API to drop all traffic from the IP.
5. **Ticketing:** Automatically create a High-Priority case in **TheHive** with all enrichment metadata attached.

---

## 2.2 Playbook Test & Verification
To test the automation, a mock phishing alert was triggered via the Wazuh manager using a known malicious C2 IP (`192.168.1.102`).

### Execution Log:
| Playbook Step    | Status  | Notes                                         |
|------------------|---------|-----------------------------------------------|
| Ingest Alert     | Success | Received Wazuh ID: 15982743.12               |
| Check IP         | Success | IP flagged as malicious in OTX (Pulse #822)   |
| Block IP         | Success | CrowdSec blocked 192.168.1.102 on all nodes   |
| Create Ticket    | Success | TheHive Case #402 opened and assigned to T2   |

---

## 2.3 Verification Method
1. **CrowdSec Check:** Verified the ban status using the command `cscli decisions list`. The IP `192.168.1.102` was correctly listed with the reason "SOAR Auto-Block".
2. **TheHive Check:** Confirmed Case #402 exists with the tag `automation:blocked` and includes the OTX pulse link.

---

## 2.4 Playbook Summary (Google Docs Draft)
> "This SOAR playbook automates the response to phishing alerts by integrating threat intelligence and active containment. It extracts source IPs from Wazuh alerts, validates reputation via VirusTotal/OTX, and triggers an immediate block through CrowdSec. Finally, it centralizes documentation by opening a high-priority case in TheHive for forensic review."

---

## 4. Verification & Success Criteria
- [x] Automation logic successfully handles API rate-limiting.
- [x] Containment action (CrowdSec) is verified at the host level.
- [x] Manual intervention is bypassed for confirmed malicious indicators.

**Common Error Noted:** During initial testing, the playbook failed because the API key for CrowdSec had insufficient permissions to "write" decisions. I resolved this by generating a new LAPI (Local API) key with `admin` scope and updating the Phantom asset configuration.

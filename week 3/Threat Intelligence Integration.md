# Lab Task 2: Threat Intelligence Integration
**Tools:** Wazuh Manager, AlienVault OTX (Open Threat Exchange), TheHive  

---

## 2.1 Threat Feed Import & IOC Matching
**Objective:** Automate the ingestion of external Threat Intelligence to proactively identify known malicious actors within the internal network.

### Execution Steps:
1. **API Integration:** Configured the Wazuh `integratord` daemon to pull active pulses from AlienVault OTX using a Python-based API bridge.
2. **IOC Subscription:** Subscribed to "Botnet C2 IP" and "Known Scanner" pulses.
3. **Mock Injection:** Simulated a network connection from a documented malicious IP (`192.168.1.100`) to test the matching engine.

### Verification:
The Wazuh manager successfully triggered a **Level 12 Alert** (High Severity) when the source IP matched an entry in the local `malicious_ips.txt` lookup table derived from the OTX feed.

---

## 2.2 Alert Enrichment: IP Reputation
**Objective:** Provide analysts with immediate context regarding the reputation of an indicator (IP) without requiring manual lookups.

### Enrichment Documentation:
| Alert ID | IP            | Reputation        | Notes                                     |
|----------|---------------|-------------------|-------------------------------------------|
| 003      | 192.168.1.100 | Malicious (OTX)   | Linked to Emotet C2 Infrastructure        |

**Technical Detail:** The enrichment process utilized a `CURL` request within a Wazuh integration script. When a login failure or network event occurred, the script queried the OTX API. The returned JSON metadata (e.g., "Malicious", "Whitelisted", "Scanner") was appended to the Wazuh alert `data` field and subsequently pushed to **TheHive** for case management.

---

## 2.3 Proactive Threat Hunting (MITRE T1078)
**Objective:** Hunt for anomalous usage of "Valid Accounts" (T1078) to detect credential theft or insider threats.

### Hunting Query (Wazuh/Kibana):
`user.name: * AND NOT user.name: "system" AND NOT user.name: "network service"`

### Findings Summary:
> "The hunt identified successful SSH logins on Metasploitable from an unauthorized subnet at 02:00 AM. Investigation revealed the account 'msfadmin' was used to execute `sudo` commands. Cross-referencing with OTX showed the source IP had a 'Malicious' reputation, confirming a credential compromise (T1078.003) rather than an authorized administrative action."

---

## 4. Verification & Success Criteria
- [x] OTX pulses are successfully downloaded to `/var/ossec/etc/lists/`.
- [x] Wazuh alerts display `otx_reputation` field in the JSON output.
- [x] Hunting queries successfully isolated non-system account activity for manual review.

**Common Error Noted:** Initial integration failed due to an expired OTX API Key. After renewing the key and restarting the `wazuh-manager` service, the connection was established. I also noted that rate-limiting on the free OTX tier requires a 60-second cache to prevent API drops during high-volume alert bursts.

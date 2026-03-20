# Threat Hunting 
**Tools:** Elastic Security, Velociraptor, AlienVault OTX  

---

## 1.1 Hypothesis Development
**Hypothesis:** Threat actors are leveraging compromised credentials to assign administrative privileges to standard user accounts to establish persistence and bypass security controls.

### Methodology:
- **Focus:** Privilege Escalation via Windows Event ID 4672 (Special privileges assigned to new logon).
- **Target:** Accounts that do not typically appear in the Domain Admin group or have non-standard logon hours.

### Hunting Query (Elastic KQL):
`event.code : "4672" AND NOT winlog.event_data.SubjectUserName : "Administrator" AND NOT winlog.event_data.SubjectUserName : "*$"`

---

## 1.2 Data Evidence Table
The following anomalous event was identified during the log query in Elastic Security:

| Timestamp            | User       | Event ID | Notes                                      |
|----------------------|------------|----------|--------------------------------------------|
| 2026-03-20 15:00:00  | testuser   | 4672     | Unexpected admin role; User not in IT group |

---

## 1.3 Threat Intelligence Hunt (OTX & Velociraptor)
**Objective:** Cross-reference the suspicious user activity with external indicators of compromise (IOCs) and endpoint artifacts.

### Execution Steps:
1. **OTX Lookup:** Searched for the source IP associated with the `testuser` logon. OTX flagged the IP as a known exit node for a malicious VPN provider.
2. **Velociraptor Investigation:** Deployed a "Live Response" collector to the affected endpoint to correlate the privilege assignment with active processes.

### Velociraptor VQL Query:
```sql
SELECT Name, CommandLine, ParentName, User 
FROM pslist() 
WHERE User =~ "testuser" AND Name =~ "cmd|powershell"

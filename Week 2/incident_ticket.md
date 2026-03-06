# Incident Ticket: INC-2026-001
**Status:** Open | **Assignee:** Shubham (SOC Analyst)

## [CRITICAL] Ransomware Detection on Server-X

**Detection Summary:**
- **Source:** Wazuh Manager (Rule 5760 - File Encryption)
- **Time:** 2026-03-06 14:45:00 IST

**Indicators of Compromise (IOCs):**
- **Malicious File:** `crypto_locker.exe`
- **Hash (MD5):** `5d41402abc4b2a76b9719d911017c592`
- **C2 IP Address:** `192.168.1.50`

**Incident Details:**
Multiple file encryption signatures were detected in the `/home/shubham/` directory. High CPU usage and the presence of a ransom note were verified.

**Immediate Actions Taken:**
- Host isolated via network firewall.
- Snapshot of VM taken for forensics.

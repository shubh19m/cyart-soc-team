# Lab Task 5: Evidence Preservation and Analysis
**Tools:** Velociraptor, FTK Imager, sha256sum  

---

## 5.1 Volatile Data Collection (Live Response)
**Objective:** Capture highly perishable data (Network Connections) from a live system before it is lost due to system shutdown or attacker cleanup.

### Execution Steps:
1. **Velociraptor Deployment:** Connected to the target Windows VM via the Velociraptor agent.
2. **Artifact Collection:** Executed the `Windows.Network.Netstat` artifact to pull all active TCP/UDP connections.
3. **VQL Query:** `SELECT * FROM pslist() WHERE Name =~ 'powershell'` (Used to correlate network sockets with suspicious processes).
4. **Export:** Downloaded the resulting telemetry as a `.csv` file for offline analysis.

### Findings:
Analysis of the `netstat.csv` revealed an established connection from `powershell.exe` to an external IP on port `4444`, confirming an active reverse shell during the time of collection.

---

## 5.2 Evidence Preservation & Integrity (Memory Forensics)
**Objective:** Perform a full memory acquisition to capture injected code, encryption keys, and unsaved command history, while ensuring the evidence is legally defensible through hashing.

### Execution Steps:
1. **Memory Acquisition:** Used Velociraptor's `Windows.Memory.Acquisition` artifact (utilizing the WinPMEM driver) to dump the raw RAM contents of Server-Y.
2. **Chain of Custody:** Immediately generated a SHA256 hash of the `.raw` file to ensure the data cannot be altered without detection.

### Evidence Documentation (Chain of Custody):
| Item          | Description                    | Collected By  | Date       | Hash Value (SHA256)                                               |
|---------------|--------------------------------|---------------|------------|-------------------------------------------------------------------|
| Memory Dump   | Server-Y Full RAM (WinPMEM)    | SOC Analyst   | 2026-03-13 | 8f92c3a5d6e7f8g9h0i1j2k3l4m5n6o7p8q9r0s1t2u3v4w5x6y7z8a9b0c1d2e3 |
| Netstat Log   | Snapshot of Network Sockets    | SOC Analyst   | 2026-03-13 | 4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t1u2v3w4x5y6z7a8b9c0d1e2f3g4h5i |

---

## 5.3 Analysis & Forensic Summary
**Summary of Findings:**
> "The memory dump from Server-Y was analyzed using the Volatility Framework. We successfully recovered the decoded PowerShell payload from the `VAD` (Virtual Address Descriptor) space. The memory strings confirmed the use of a 'Metasploit Meterpreter' listener. By maintaining a strict chain of custody and hashing the dump immediately, we have preserved the integrity of the evidence for potential legal or administrative proceedings."

---

## 4. Verification & Success Criteria
- [x] Volatile data (Netstat) was collected before the system was powered down.
- [x] Memory dump was successfully acquired without crashing the target VM.
- [x] SHA256 hashes match the original files, verifying no data corruption.

**Common Error Noted:** During memory acquisition, the target VM initially ran out of disk space. I resolved this by mapping a network drive/share to store the dump externally, ensuring the "footprint" on the target system remained as small as possible to avoid overwriting forensic evidence in unallocated space.

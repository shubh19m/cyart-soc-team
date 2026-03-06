# Standard Operating Procedure (SOP): Alert Triage Workflow

This document outlines the end-to-end process followed by the SOC team for every incoming alert.

### Phase 1: Detection & Initial Analysis
1. **Monitor Wazuh Dashboard:** Watch for alerts with Level 10+ (High/Critical).
2. **Identify Alert Type:** Cross-reference the Rule ID with the `alert_classification.md` table.
3. **Validate:** Determine if the alert is a **True Positive** (real threat) or **False Positive** (authorized activity).

### Phase 2: Prioritization
1. **Assign CVSS Score:** Use the `prioritization_logic.md` to determine the urgency.
2. **SLA Check:** Critical alerts (CVSS 9.0+) must be acknowledged within 15 minutes.

### Phase 3: Containment & Documentation
1. **Immediate Action:** If Critical, use Wazuh "Active Response" to isolate the host or block the attacker's IP.
2. **Ticket Creation:** Open a ticket in TheHive (documented in `incident_ticket.md`) with all relevant IOCs (IPs, Hashes, Filenames).

### Phase 4: Escalation
1. **Tier 2 Notification:** If the threat persists or requires forensics, send the escalation email (see `escalation_email.md`).

### Phase 5: Recovery & Lessons Learned
1. **Remediation:** Patch the vulnerability (e.g., Log4Shell) or remove the malware.
2. **Post-Mortem:** Update the Alert Classification sheet if new patterns were discovered.

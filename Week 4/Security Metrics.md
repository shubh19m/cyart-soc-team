# 7: Security Metrics & Executive Reporting 
**Tools:** Elastic Security (Dashboards), Google Sheets (Analytics), Google Docs (Reporting)  

---

## 7.1 Advanced SOC Metrics Dashboard
**Objective:** Visualize the operational efficiency of the SOC to identify bottlenecks in the detection and response lifecycle.

### Key Performance Indicators (KPIs):
I developed a custom **Elastic Security Dashboard** to track real-time performance against our Service Level Agreements (SLAs).

| Metric | Current Value | Target (SLA) | Status |
| :--- | :--- | :--- | :--- |
| **MTTD** (Mean Time to Detect) | 2 Hours | < 1 Hour | ⚠️ Needs Improvement |
| **MTTR** (Mean Time to Respond) | 4 Hours | < 4 Hours | ✅ On Track |
| **False Positive Rate** | 12% | < 15% | ✅ On Track |

**Analyst Note:** The dashboard utilizes the `@timestamp` field vs. the `event.ingested` and `incident.closed` timestamps to automatically calculate the duration of each phase.

---

## 7.2 Executive Summary Report
**Objective:** Provide leadership with a high-level overview of the threat landscape and resource requirements.

### Executive Summary:
> "During the Q1 reporting period, the SOC successfully neutralized 45 high-severity incidents, including a significant RCE attempt on our legacy Samba infrastructure. While our **Mean Time to Respond (MTTR)** remains strong at 4 hours due to newly implemented SOAR playbooks, our **Mean Time to Detect (MTTD)** averaged 2 hours. This delay is primarily attributed to a lack of visibility in remote endpoint telemetry. 
>
> **Recommendations:** To achieve our 1-hour detection target, we recommend the deployment of Sysmon across all remote workstations and the integration of a dedicated Managed Detection and Response (MDR) partner for 24/7 coverage. These improvements will reduce our overall **Dwell Time** and lower the potential financial impact of a breach."

---

## 7.3 Metrics Analysis: Dwell Time Investigation
**Objective:** Analyze the specific gap between initial compromise and final eradication for a mock breach.

### Findings Summary:
> "Analysis of a mock phishing incident revealed a **Dwell Time of 1.5 hours** (the time the attacker spent active in the network before detection). While the containment was rapid (15 minutes post-detection), the 90-minute dwell time allowed for initial internal reconnaissance. Reducing this requires more proactive **Threat Hunting** and automated SIEM alerting."

---

## 7.4 Verification & Success Criteria
- [x] Metrics calculated accurately using the difference between event timestamps.
- [x] Executive report uses non-technical language focused on risk and recommendations.
- [x] Dashboard provides a 'single pane of glass' for SOC performance.

**Common Error Noted:** Initially, the MTTR calculation included 'Time to Ticket' which skewed the data. I updated the formula to focus strictly on the time from **Alert Generation** to **Host Isolation**, providing a more accurate measure of our technical response speed.

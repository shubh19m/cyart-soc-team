# Alert Prioritization Logic (CVSS v3.1)

To ensure effective incident response, alerts are prioritized based on their CVSS (Common Vulnerability Scoring System) scores.

## Scoring Table
| Incident Type | CVSS Score | Severity | SLA (Response Time) |
| :--- | :--- | :--- | :--- |
| **Log4Shell RCE** | 10.0 | **Critical** | Immediate (15-30 mins) |
| **SQL Injection** | 8.8 | **High** | < 4 Hours |
| **Account Brute Force** | 5.3 | **Medium** | < 24 Hours |
| **Internal Port Scan** | 3.3 | **Low** | Weekly Review |

## Priority Visualization Plan
Our Wazuh dashboard is configured to visualize these priorities using:
- **Pie Chart:** Rule Level Distribution (Level 12+ = Critical/High).
- **Bar Chart:** Top 5 Alerting Agents (Identifying targeted assets).

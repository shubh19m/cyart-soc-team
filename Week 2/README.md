# Week 2: Alert Management & SOC Operations

## 1. Alert Classification System
This folder contains the mapping of security alerts to the MITRE ATT&CK framework.

| Alert ID | Threat Type | Priority | MITRE Tactic | MITRE Technique | Response Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 001 | Phishing Email | High | Initial Access | T1566 | Isolate Host |
| 002 | SSH Brute Force | Medium | Credential Access | T1110 | Block Source IP |
| 003 | Log4Shell Exploit | Critical | Initial Access | T1190 | Patch / WAF |
| 004 | SQL Injection | High | Initial Access | T1190 | Block IP / WAF |

## 2. Workflows & Procedures
Detailed steps for incident response and escalation.
- [Log4Shell Response Procedure](./Workflows/Log4Shell_Workflow.md)
- [Escalation Email Template](./Workflows/Escalation_Template.md)

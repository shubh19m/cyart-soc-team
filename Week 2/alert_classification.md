# Alert Classification & MITRE Mapping

The following table maps common security alerts to the MITRE ATT&CK framework to provide tactical context for incident response.

| Alert ID | Threat Type | Priority | MITRE Tactic | MITRE Technique | Response Procedure |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 001 | Phishing: Suspicious Link | High | Initial Access | T1566 | Isolate host, reset credentials |
| 002 | SSH Brute Force Attempt | Medium | Credential Access | T1110 | Block source IP, check for success |
| 003 | SQL Injection Attempt | High | Initial Access | T1190 | Block IP, Web App Firewall update |
| 004 | Unauthorized Sudo Usage | Medium | Privilege Escalation | T1548 | Verify user identity, audit logs |
| 005 | Log4Shell RCE Attempt | Critical | Initial Access | T1190 | Immediate patch, isolate server |

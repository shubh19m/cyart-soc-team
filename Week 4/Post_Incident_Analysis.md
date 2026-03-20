# 3: Post-Incident Analysis
**Tools:** Google Sheets (Analysis), Draw.io (Diagramming), SOC Metric Frameworks  

---

## 3.1 Root Cause Analysis (RCA): The 5 Whys
**Scenario:** A successful phishing attack led to credential harvesting on a high-value asset. To prevent recurrence, a deep-dive RCA was performed using the "5 Whys" methodology.

### 5 Whys Table:
| Step | Question | Answer |
| :--- | :--- | :--- |
| **1** | Why was the malicious email opened? | The user clicked a link in a highly targeted spear-phishing email. |
| **2** | Why was the link clicked? | The email bypassed standard spam filters by using a newly registered domain. |
| **3** | Why did the filter not catch it? | The email gateway policy for "Zero-Day" or "Uncategorized" domains was set to 'Audit' instead of 'Block'. |
| **4** | Why was the policy set to 'Audit'? | To prevent false positives for legitimate new business partners during a recent merger. |
| **5** | **Root Cause** | Lack of a "Sandbox/Wait" policy for uncategorized domains and insufficient user security awareness for "Look-alike" domains. |

---

## 3.2 Fishbone (Ishikawa) Diagram Analysis
A Fishbone diagram was constructed in **Draw.io** to categorize the contributing factors of the incident across four domains:

* **Technology:** Email gateway failed to block uncategorized URLs; EDR did not prevent the initial browser-based credential prompt.
* **Process:** Onboarding procedures for new domains were too permissive during the merger phase.
* **People:** Lack of "Stop-Look-Think" training for the finance department regarding external payment links.
* **Environment:** Increased pressure during end-of-quarter led to hurried user decision-making.

---

## 3.3 SOC Performance Metrics
To evaluate the efficiency of the response, we calculated key performance indicators (KPIs) based on the incident logs.

### Metrics Formulas:
* **Mean Time to Detect (MTTD):** $T_{detection} - T_{compromise}$
* **Mean Time to Respond (MTTR):** $T_{containment} - T_{detection}$

### Incident Calculation:
* **Incident Start:** 13:00  
* **Detection Time:** 15:00 (**MTTD: 2 Hours**)  
* **Response/Containment:** 19:00 (**MTTR: 4 Hours**)  

**Metrics Summary:**
> "The incident yielded an MTTD of 2 hours and an MTTR of 4 hours. While the response time (MTTR) is within the SLA for 'High' severity incidents, the 2-hour detection gap (Dwell Time) highlights a need for better real-time alerting on anomalous browser-to-external-IP behavior."

---

## 3.4 Lessons Learned & Continuous Improvement
1.  **Policy Update:** Change Email Gateway policy to "Quarantine" for all domains registered within the last 30 days.
2.  **User Training:** Conduct a simulated phishing exercise targeting the Finance team within the next 14 days.
3.  **Automation:** Implement the SOAR playbook (from Task 2) to auto-verify URL reputation in real-time.

---

## 4. Verification & Success Criteria
- [x] RCA identifies a systemic failure rather than just "human error."
- [x] Metrics are calculated using industry-standard formulas.
- [x] Fishbone analysis covers multiple domains (People, Process, Tech).

**Common Error Noted:** It is easy to stop at the first "Why" (User error). By pushing to the 5th "Why," I identified a configuration gap in our Email Gateway that, if fixed, protects all users regardless of their individual actions.

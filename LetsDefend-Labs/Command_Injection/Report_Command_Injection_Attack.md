# Incident Report: Whoami Command Detected in Request Body

## 1. Alert
- **Rule Name:** SOC168 – Whoami Command Detected in Request Body 
- **Event ID:** 118 
- **Severity:** High (Based on successful exploitation) 
- **Date:** Feb 28, 2022 
- **Target Host:** WebServer1004 (172.16.17.16) 
- **Source IP:** 61.177.172.87 
- **Target Port:** 443 (HTTPS) 

**Description:** A web attack alert was triggered after a suspicious system command (`whoami`) was detected within the POST request body targeting a public-facing web server.

---

## 2. Detection & Verification

The alert was triggered because the request body of a POST method contained a command injection string .

**The original request URL:**
```text
https://172.16.17.16/video/
```
Upon examining the Log Management page and filtering by the source IP address (61.177.172.87), it was confirmed that the attacker sent the `whoami` command in the request body. Further investigation revealed multiple malicious commands.

## 3. Analysis
### Threat Intelligence

The attacker IP:
- **61.177.172.87** 
  
Findings:
- **Location:** China 
- **VirusTotal/AbuseIPDB:** Flagged as malicious 

Based on the reputation analysis and the nature of the payloads, the traffic is confirmed as malicious.

### Attack Sequence Analysis

Reviewing the logs and the terminal history of the targeted web server showed a clear progression of command injection attempts.

**Observed payload progression:**
- `ls` 
- `whoami` 
- `uname` 
- `cat /etc/passwd` 
- `cat /etc/shadow` 

### HTTP Response Analysis & Endpoint Verification

The attack was confirmed as **successful** based on the following evidence:

- **HTTP Status Code:** 200 (OK) 
- **Response Sizes:** Variation in HTTP response sizes indicated that different data was being returned to the attacker based on the commands executed.
- **Endpoint Security:** The **Command History** on WebServer1004 explicitly showed that the attacker's commands were executed on the system.

## 4. MITRE ATT&CK Mapping
- **T1190** – Exploit Public-Facing Application
- **T1059.004** – Command and Scripting Interpreter: Unix Shell
- **T1552.001** – Unsecured Credentials: Password Stores (Attempted access to `/etc/shadow`)

## 5. Impact Assessment
- Public-facing web application compromised 
- Successful remote command execution (RCE) confirmed
- Sensitive system files (`/etc/passwd`, `/etc/shadow`) were targeted and likely accessed 
- Potential for full system takeover or lateral movement

## 6. Conclusion

This alert is classified as a **True Positive** .

The attacker (61.177.172.87) successfully exploited a command injection vulnerability on WebServer1004. The endpoint's own command history confirms the execution of discovery and credential-access commands. 

Due to the confirmed compromise, the incident was escalated to **Tier 2** for further forensic investigation and remediation.

## 7. Containment & Recommendations
### Recommended Immediate Actions
- **Isolate Host:** WebServer1004 must be contained to prevent further malicious activity.
- **Block IP:** The malicious IP 61.177.172.87 should be blocked.
- **Credential Rotation:** Since `/etc/shadow` was accessed, a full rotation of all system credentials is required.

### Preventive Measures
- **Input Sanitization:** Implement strict validation for all user-supplied input to prevent command injection.
- **Principle of Least Privilege:** Ensure the web service account has minimal permissions.
- **WAF Tuning:** Update Web Application Firewall signatures to block common shell command strings in request bodies.

## 8. Artifacts

| Artifact type | Value| 
| :--- | :--- |
| **Attacker IP** | 61.177.172.87 |
| **Target Host** | 172.16.17.16 | 
| **Attack Type** | Command Injection |
| **Command Log** | `ls`, `whoami`, `uname`, `cat /etc/passwd`, `cat /etc/shadow` |


---

## 9. Lessons Learned (Personal Reflection)

This investigation highlights the importance of checking endpoint logs when web logs seem suspicious. While the "whoami" trigger in the request body was a red flag, seeing the actual execution in the **Command History** of the server turned a "suspicion" into a "confirmed breach".

It is a reminder that:
- HTTP 200 status codes combined with changing response sizes are strong indicators of successful data retrieval during an injection attack.
- Attackers will move quickly from simple discovery (`whoami`) to targeting sensitive system files (`/etc/shadow`).
- Prompt containment of the affected host is critical the moment successful execution is verified.

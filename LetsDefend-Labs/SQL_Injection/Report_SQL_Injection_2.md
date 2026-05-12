# Incident Report: Possible SQL Injection Payload Detected

## 1. Alert
- **Rule Name:** SOC165 – Possible SQL Injection Payload Detected  
- **Event ID:** 115  
- **Severity:** Medium  
- **Date:** Feb 25, 2022  
- **Target Host:** WebServer1001 (172.16.17.18)  
- **Source IP:** 167.99.169.17  
- **Target Port:** 443  

**Description:**  
A web attack alert was triggered after suspicious SQL-related payloads were detected in HTTP GET requests targeting a public-facing web server.

---

## 2. Detection & Verification

The alert was triggered because the requested URL contained a classic SQL injection pattern:

```text
" OR 1 = 1 -- -
```
The original request:
```text
https://172.16.17.18/search/?q=%22%20OR%201%20%3D%201%20--%20-
```
The payload was decoded using CyberChef, revealing:
```text
OR 1 = 1 -- -
```
This confirmed the request was a clear SQL injection attempt targeting the q parameter of the /search/ endpoint.

## 3. Analysis
### Threat Intelligence

The attacker IP:
- 167.99.169.17
  
was investigated using:
- WHOIS
- VirusTotal
- AbuseIPDB

Findings:

- Hosting Provider: DigitalOcean, LLC
- VirusTotal: flagged as malicious (primarily phishing-related activity)
- AbuseIPDB: reported more than 14,000 times

Based on reputation analysis and attack behavior, the traffic was classified as malicious.

## Attack Sequence Analysis

Reviewing proxy and web logs showed the attacker performed multiple SQL injection attempts over several minutes.

Observed payload progression:
- /search/?q='
- ' OR '1
- ' OR 'x'='x
- 1' ORDER BY 3--+
- " OR 1 = 1 -- -

This sequence strongly suggests the attacker was:
- Testing for SQL injection vulnerability
- Attempting query manipulation
- Enumerating database structure

## HTTP Response Analysis

All requests returned:
- HTTP Status Code: 500

Key observations:

- Response sizes remained consistent
- No variation indicating successful query execution
- No evidence of data extraction or authentication bypass

HTTP 500 Internal Server Error suggests:
- The application encountered errors processing malformed queries
- The injection attempts likely failed

Additionally:

No suspicious activity was identified on the target server indicating successful compromise.

## 4. MITRE ATT&CK Mapping
- T1190 – Exploit Public-Facing Application
- T1595 – Active Scanning
- T1059 – Command and Scripting Interpreter (attempted SQL manipulation)

## 5. Impact Assessment
- Public-facing web application targeted
- Multiple SQL injection attempts confirmed
- No evidence of successful exploitation
- No confirmed database compromise
- Attack unsuccessful

## 6. Conclusion

This alert is classified as a True Positive.

The attacker attempted several SQL injection payloads against the web application hosted on 172.16.17.18. Analysis of the requests confirmed clear SQL injection behavior, including boolean-based injection tests and database enumeration attempts.

Despite multiple attempts, the attack appears to have been unsuccessful:
- All requests returned HTTP 500 responses
- No evidence of successful query execution was identified
- No suspicious activity was observed on the target system

## 7. Containment & Recommendations
### Recommended Immediate Actions
- Block attacker IP address (167.99.169.17)
- Continue monitoring for repeated attack attempts
- Review web application logs for additional suspicious behavior
### Preventive Measures
- Tune Web Application Firewall (WAF)
- Validate and sanitize user input
- Monitor for SQL injection signatures
- Perform regular web application security testing

## 8. Artifacts

| Artifact Type    | Value             |
| ---------------- | ----------------- |
| Attacker IP      | 167.99.169.17     |
| Target Host      | 172.16.17.18      |
| Attack Type      | SQL Injection     |
| Hosting Provider | DigitalOcean, LLC |

## 9. Lessons Learned (Personal Reflection)

This investigation helped me better understand how attackers gradually test web applications for SQL injection vulnerabilities.
As this wasn't my first case with SQL Injections, I must say this one went smoother and I could solve it pretty quickly.

Important observations from this case:

- Attackers often start with simple payloads before escalating to more advanced queries
- HTTP response codes can provide valuable insight into attack success or failure
- Consistent response sizes and HTTP 500 errors can indicate unsuccessful exploitation attempts
- Reviewing the sequence of requests helps reveal attacker methodology


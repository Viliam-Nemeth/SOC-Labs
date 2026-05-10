# Incident Report: SQL Injection Attack Detected

## 1. Alert
- **Rule Name:** SOC127 – SQL Injection Detected  
- **Event ID:** 235  
- **Severity:** High  
- **Date:** March 2024  
- **Attacker IP:** 118.194.247.28  
- **Target Host:** Atlanta Server (172.16.20.12)  

**Description:**  
A web attack alert was triggered after suspicious requests targeting a public-facing web application were detected. Initial investigation indicated attempted SQL injection activity against the web server.

---

## 2. Detection & Verification

Log analysis on the proxy confirmed multiple suspicious HTTP requests originating from:

- **118.194.247.28**

Threat intelligence checks showed:
- Malicious reputation on:
  - VirusTotal
  - AbuseIPDB
- Associated with:
  - Brute force attacks
  - Web attacks

WHOIS analysis identified the IP as belonging to:

- **Beijing CNISP Technology Co., Ltd.**
- Location: Beijing Province, China

---

## 3. Analysis

###  Reconnaissance Activity

Before the SQL injection attempts, the attacker performed:
- Port scanning activity against the target host
- Enumeration of exposed services

Analysis showed:
- Port 80 was accessible externally
- Attacker subsequently targeted the web application

This demonstrated a typical attack progression:

Reconnaissance → Service Discovery → Exploitation Attempt

---

###  SQL Injection Payload Analysis

Raw proxy logs revealed multiple SQL injection payloads targeting:

```text
/index.php?id=
```
One decoded request included:
```
1') AND 2574=CAST((CHR(113)||CHR(107)||CHR(107)||CHR(118)||CHR(113))
||(SELECT (CASE WHEN (2574=2574) THEN 1 ELSE 0 END))::text
||(CHR(113)||CHR(112)||CHR(122)||CHR(106)||CHR(113)) AS NUMERIC)
AND ('FiHf'='FiHf
```
The request contained:

- Conditional SQL logic
- Character encoding obfuscation
- Database interaction attempts

The User-Agent string identified the tool:
```text
sqlmap/1.7.2
```
### Payload Decoding
To better understand the payloads, the malicious URLs were decoded using:
- CyberChef – Parse URL

This helped reveal:

- SQL conditional statements
- Enumeration attempts
- Database interaction logic

The attacker attempted to:

- Test SQL query execution
- Extract database information
- Potentially interact with backend systems

### HTTP Response Analysis

Several malicious requests returned:
```text
HTTP 200 OK
```
Important observation:

- HTTP 200 indicates the server processed the request successfully
- It does NOT confirm successful SQL injection exploitation

At this stage:

- Evidence confirms attack attempts
- No definitive proof of database compromise was identified

## 4. MITRE ATT&CK Mapping
- T1595 – Active Scanning
- T1190 – Exploit Public-Facing Application
- T1110 – Brute Force (related attacker behavior)

## 5. Impact Assessment
-  Public-facing web application targeted
-  SQL injection attempts confirmed
-  Potential database exposure risk
-  No confirmed database compromise
-  Server was contained promptly

## 6. Conclusion

This alert is classified as a True Positive.

The attacker conducted reconnaissance against the target system before launching multiple SQL injection attempts against the web application hosted on 172.16.20.12.

Analysis of raw logs and decoded payloads confirmed the use of automated SQL injection tooling (sqlmap). While several malicious requests received HTTP 200 responses, there was no conclusive evidence that the attacker successfully compromised the database.

The affected server was successfully contained to prevent further malicious activity.

## 7. Containment & Recommendations
Immediate Actions Taken:
- Contained affected server
- Investigated malicious requests
- Validated attacker reputation
  
Recommended Security Improvements:
- Implement Web Application Firewall (WAF)
- Use parameterized SQL queries
- Restrict unnecessary external exposure
- Regularly patch web applications
- Monitor for abnormal HTTP requests
- Enable IPS/IDS protections for web attacks

## 8. Artifacts

| Artifact Type   | Value            |
| --------------- | ---------------- |
| Attacker IP     | 118.194.247.28   |
| Target Host     | 172.16.20.12     |
| Web Path        | `/index.php?id=` |
| Tool Identified | sqlmap/1.7.2     |

## 9. Lessons Learned (Personal Reflection)

I am very greatful, that LetsDefend has also a report on the cases I close. When I compare it to my investigation notes I always learn something new.

Few things I didn't do well/notice:

- There were more kinds of SQL injections than just 1 - I analysed only one, which really doesn't suffice, action for next time.
- I didn't fully read the SQL injection, so when I was doing the analysis the 2nd time (with the LetsDefend report) I only then saw the sqlmap source.
- Off note - LetsDefend had some bug and I couldn't submit this as a true positive... even though it obviously is.
- Also I am happy that LetsDefend has educations available to specific attacks like "Web Attack" education for this case :) 
- MITTRE ATT&CK is still little fuzzy for me - I am mapping it based on suggestions from AI and reading through it, but it doesn't feel natural, yet.
- I am thinking of starting attaching also screenshots from my investigations  for better visualisation.

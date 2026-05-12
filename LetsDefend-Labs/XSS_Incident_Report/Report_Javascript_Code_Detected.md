# Incident Report: Reflected XSS Attack Detected

## 1. Alert
- **Rule Name:** SOC166 – Javascript Code Detected in Requested URL  
- **Event ID:** 116  
- **Severity:** Medium  
- **Date:** Feb 26, 2022  
- **Target Host:** WebServer1002 (172.16.17.17)  
- **Source IP:** 112.85.42.13  
- **Target Port:** 443  

**Description:**  
A web attack alert was triggered after JavaScript code was detected inside a URL parameter targeting a public-facing web application.

---

## 2. Detection & Verification

The alert was triggered because the following request contained JavaScript code inside the `q` parameter:

```text
https://172.16.17.17/search/?q=<$script>javascript:$alert(1)<$/script>
```
The payload strongly indicated an attempt to execute JavaScript within the application.

The payload type was identified as Reflected Cross-Site Scripting (XSS)
because the malicious script was delivered directly through the URL.

## 3. Analysis
### Threat Intelligence
The attacker IP:
```text
112.85.42.13
```
was investigated using multiple threat intelligence sources.

Findings:

AbuseIPDB:
- Reported more than 45,000 times
- Classified as malicious
  
VirusTotal:
- Flagged with malicious reputation

WHOIS:
- China Unicom CHINA169 Jiangsu Province Network

Based on threat intelligence and attack behavior, the traffic was classified as malicious.

### XSS Payload Analysis

The attacker attempted to inject JavaScript code into the web application through the search parameter:
```text
<$script>javascript:$alert(1)<$/script>
```
The objective of this payload was likely to:
- Execute JavaScript in the victim’s browser
- Test for reflected XSS vulnerability
- Potentially steal session information or manipulate browser behavior

Log analysis showed:

- Multiple XSS payload attempts from the same IP
- Different variations of JavaScript injection attempts
### HTTP Response Analysis

All malicious requests returned **HTTP status code 302**

Key observations:

- Requests were redirected
- No evidence that JavaScript executed successfully
- No indication of successful client-side compromise

HTTP 302 redirects suggest:
- The application redirected requests before payload execution
- The XSS attempts were likely unsuccessful

## 4. MITRE ATT&CK Mapping
- T1059.007 – JavaScript
- T1189 – Drive-by Compromise
- T1190 – Exploit Public-Facing Application

## 5. Impact Assessment
- Public-facing web application targeted
- Multiple reflected XSS payloads identified
- No evidence of successful script execution
- No confirmed compromise
- Attack unsuccessful

## 6. Conclusion

This alert is classified as a True Positive.

The attacker attempted multiple reflected XSS attacks against the web server hosted on 172.16.17.17. Analysis confirmed that the requests contained JavaScript payloads designed to test for Cross-Site Scripting vulnerabilities.

Although the requests were malicious, all requests returned HTTP 302 responses and no evidence of successful payload execution was identified.

At this stage, the attack appears to have been unsuccessful.

## 7. Containment & Recommendations

### Recommended Immediate Actions
- Block attacker IP address (112.85.42.13)
- Continue monitoring for repeated XSS attempts
- Review web server logs for suspicious behavior
### Preventive Measures
- Implement proper input validation and sanitization
- Deploy Web Application Firewall (WAF)
- Use Content Security Policy (CSP)
- Conduct regular web application security testing

## 8. Artifacts
| Artifact Type | Value         |
| ------------- | ------------- |
| Attacker IP   | 112.85.42.13  |
| Target Host   | 172.16.17.17  |
| Attack Type   | Reflected XSS |
| HTTP Status   | 302 Redirect  |

## 9. Lessons Learned (Personal Reflection)

This investigation helped me better understand how reflected XSS attacks appear in web logs and how attackers test web applications for client-side vulnerabilities. 
It was quite cool and I am getting more and more comfortable with the investigation process. Some things are still kust guessing - differentiate XSS types is still not my forte, but I will get it.

### Important observations from this case:

- Reflected XSS payloads are often directly visible in URL parameters 
- Reviewing multiple requests helps identify attacker testing behavior
- HTTP response codes can provide clues about attack success or failure
- Even unsuccessful XSS attempts should be treated seriously because successful exploitation can lead to session theft or malicious browser execution

#  Incident Report: Quishing Attack (QR Code Phishing)

## 1. Alert
- **Rule Name:** SOC251 – Quishing Detected (QR Code Phishing)  
- **Event ID:** 214  
- **Severity:** Medium  
- **Date:** Jan 01, 2023  
- **Target User:** claire@letsdefend.io  
- **Sender Email:** security@microsecmfa.com  
- **SMTP IP:** 206.189.190.128  

**Description:**  
An email containing a suspicious QR code was delivered to a user. The QR code potentially redirects to a malicious phishing website designed to harvest user credentials.

---

## 2. Detection & Verification

Initial analysis of the email revealed:
- Suspicious sender impersonating a security-related Microsoft service  
- Subject related to MFA activation (common phishing lure)  
- Email bypassed security controls (status: allowed)  

The QR code was extracted and analyzed, revealing the following URL:
https://ipfs[.]io/ipfs/Qmbr8wmr41C35c3K2GfiP2F8YGzLhYpKpb4K66KU6mLmL4#

Threat intelligence lookup confirmed:
- URL flagged as malicious
- Associated with phishing activity according to LetsDefend Threat Intel

---

## 3. Analysis

###  QR Code / URL Analysis
- QR code redirects to IPFS-hosted content
- URL flagged by threat intelligence sources as malicious
- Likely used to bypass traditional email filters

---

###  Phishing Behavior
- Landing page imitates a webmail login portal
- Designed to harvest:
  - Email addresses  
  - Passwords  

The attack relies on user interaction (QR scan via mobile device), which reduces visibility in traditional logs.

---

###  Security Gap Identified
- Activity likely performed on a mobile device
- Limited logging available for QR-based attacks
- Email security failed to block the message

---

## 4. MITRE ATT&CK Mapping
- T1566 – Phishing  
- T1589.002 – Gather Victim Identity Information  
- T1204 – User Execution  

---

## 5. Impact Assessment
-  High likelihood of credential harvesting  
-  User may have submitted credentials  
-  No direct endpoint logs confirming execution  
-  No containment action initially performed  

---

## 6. Conclusion
This alert is classified as a True Positive.

A phishing email containing a malicious QR code was successfully delivered to the user.  
The QR code redirected to a phishing page designed to capture credentials.

Due to the nature of QR-based attacks, the activity likely occurred outside monitored systems, increasing the risk of undetected credential compromise.

---

## 7. Containment & Recommendations

###  Immediate Actions (Recommended)
- Reset user credentials immediately  
- Invalidate active sessions  
- Isolate affected host (if applicable) - this was incorrectly missed by me and corrected by the system 
- Remove phishing email from mailbox - I kept the screenshot of the email for reporting purposes.  

---

###  Preventive Measures
- Implement QR code analysis in email security tools  
- Improve phishing awareness training  
- Enforce MFA across all services  
- Deploy mobile device management (MDM) solutions  

---

## 8. Artifacts
- **Sender Email:** security@microsecmfa.com  
- **SMTP IP:** 206.189.190.128  
- **Malicious URL:** https://ipfs[.]io/ipfs/...  
- **Secondary Domain:** nsggroup[.]it  

---

## 9. Lessons Learned (Personal Reflection)

During this investigation, I have identified several gaps in my approach:

- QR code was analyzed using a non-secure online tool instead of a controlled environment  
- The URL was not analyzed in a sandbox environment  
- No source code analysis of the phishing page was performed  
- Containment actions were missed  

### Key Takeaways:
- Always use safe analysis tools (e.g., sandbox, isolated lab, CyberChef,Any.run)  
- Never skip containment phase  
- Validate findings with multiple analysis methods - Instead of using just LetsDefend TI other sources can be used. (VirusTotal, AbuseIPDB)  
- Treat phishing cases as potential credential compromise incidents - always assume that the link was clicked / QR code scanned 

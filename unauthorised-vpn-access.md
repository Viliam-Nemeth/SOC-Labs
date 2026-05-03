#  Incident Report: Unauthorized VPN Access

## 1. Alert
- **Rule Name:** SOC257 – VPN Connection from Unauthorized Country  
- **Event ID:** 225  
- **Severity:** Low  
- **Date:** Feb 13, 2024  
- **User:** monica@letsdefend.io  
- **Source IP:** 113.161.158.12  

**Description:**  
Alert triggered due to a VPN login attempt from an unauthorized geographic location targeting a valid user account.

---

## 2. Detection & Verification

Log analysis confirmed:
- VPN access attempt to vpn-letsdefend.io
- HTTP response code 200 (service reachable)
- Multiple login attempts observed

---

## 3. Analysis

### 🔹 IP Reputation
- External IP address located in Vietnam  
- Flagged as malicious in multiple threat intelligence sources  
- Associated with:
  - Brute force attacks  
  - Web attacks  
  - Malicious activity  

---

### 🔹 Authentication Analysis
- Valid username and password were used  
- Multi-Factor Authentication (MFA) was triggered  
- One-Time Password (OTP) entered incorrectly multiple times  

**Conclusion:**
- Credentials were **compromised**
- MFA successfully **prevented account takeover**

---

### 🔹 Additional Activity
- Evidence of **port scanning activity** targeting internal asset `33.33.33.33`

---

## 4. MITRE ATT&CK Mapping
- T1133 – External Remote Services 
- T1110 – Brute Force
- T1078 – Valid Accounts  

---

## 5. Impact Assessment
- No successful login  
- No system compromise  
- User credentials exposed  

---

## 6. Conclusion
This alert is classified as a True Positive.

An attacker attempted to access a user account via VPN from an unauthorized location using valid credentials.  
The attack was unsuccessful due to MFA enforcement, preventing unauthorized access.

---

## 7. Containment & Recommendations
- Reset the affected user’s password immediately  
- Monitor account for suspicious activity  
- Implement:
  - Account lockout policy after failed attempts  
  - Geo-restrictions for VPN access  
  - Strong password policies  

---

## 8. Artifacts
- **Attacker IP:** 113.161.158.12  
- **Target User:** monica@letsdefend.io  
- **Target System:** vpn-letsdefend.io  

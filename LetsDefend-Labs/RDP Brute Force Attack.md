#  Incident Report: RDP Brute Force Attack Leading to Compromise

## 1. Alert
- **Rule Name:** SOC176 – RDP Brute Force Detected  
- **Event ID:** 234  
- **Severity:** Medium–High  
- **Date:** March 7, 2024  
- **Target Host:** Matthew (172.16.17.148)  
- **Source IP:** 218.92.0.56  

**Description:**  
Multiple failed RDP login attempts were detected from an external IP address targeting a Windows host. The activity was flagged as a potential brute force attack.

---

## 2. Detection & Verification

Initial investigation confirmed:
- Source IP is **external**
- Repeated connection attempts to **RDP port (3389)**
- Firewall allowed traffic (no blocking in place)

Threat intelligence lookup showed:
- IP flagged as **malicious**
- Previously associated with brute force and suspicious activity

---

## 3. Analysis

###  Traffic Analysis
- 15 connection attempts from attacker IP
- All targeting **single host (Matthew – 172.16.17.148)**
- No evidence of lateral targeting across multiple systems

---

###  Endpoint Analysis

Windows Event Logs revealed:

#### Failed Logins:
- **Event ID:** 4625  
- Usernames attempted:
  - admin  
  - guest  
  - sysadmin  
- Error: invalid credentials  

#### Successful Login:
- **Event ID:** 4624  
- Username: **Matthew**  
- Indicates **successful compromise**

---

###  Post-Compromise Activity

After gaining access, the attacker executed:

```bash
whoami
net user letsdefend
net localgroup administrators
netstat -ano
```
This indicates:
- User and privilege enumeration
- Network reconnaissance
- Preparation for further actions

## 4. MITRE ATT&CK Mapping
- **T1110** – Brute Force
- **T1078** – Valid Accounts
- **T1087** – Account Discovery
- **T1059** – Command and Scripting Interpreter

## 5. Impact Assessment
- Successful unauthorized access
- System fully compromised
- Attacker performed internal reconnaissance
- No evidence of lateral movement (at this stage)

## 6. Conclusion
This alert is classified as a True Positive.

An attacker performed a brute force attack against an exposed RDP service and successfully gained access using valid credentials. 
After access, the attacker began basic system and network enumeration.

This represents a full system compromise and requires immediate containment.

## 7. Containment & Recommendations
Immediate Actions Taken:
- Isolated compromised host (172.16.17.148)

Recommended Actions:
- Reset all user credentials on the system
- Disable or restrict RDP access from external networks

Enforce:
- Strong password policy
- Multi-Factor Authentication (MFA)

## 8. Artifacts
Attacker IP: 218.92.0.56

Target Host: 172.16.17.148

Compromised User: Matthew

Attempted Usernames: admin, guest, sysadmin

## 9. Lessons Learned (Personal Reflection)

This investigation helped me to learn several important SOC concepts:

- Always verify attack scope carefully:
    - Initially, I assumed multiple systems were targeted. In reality, the attack focused on a single host.
- Endpoint logs (Event IDs 4624 & 4625) are critical for confirming compromise.
- Brute force alerts should always be treated as potential full compromises, not just noise
- Post-login activity is key to understanding attacker intent

# SOC Incident Report: VPN Brute Force & User Enumeration Attack

## 1. Executive Summary
* **Alert Title:** Successful VPN Brute Force & Account Compromise
* **Platform:** LetsDefend SIEM
* **Severity:** High
* **Final Verdict:** True Positive
* **Summary:** Investigated a high-volume authentication anomaly targeting the organization's external VPN gateway. Triage confirmed an automated brute-force attack originating from an external IP address. Log analysis revealed user enumeration vulnerabilities in the authentication response. A successful unauthorized login was detected after 9 failed attempts, indicating the attacker successfully guessed valid credentials.
  
![Alert Details](Screenshot%20(390).png)

---

## 2. Incident Details
* **Source IP (Attacker):** `[37.19.221.229]`
* **Destination IP (VPN Gateway):** `[33.33.33.33]`
* **Target Protocol / Service:** SSL-VPN / HTTPS (Port 443)
* **Target Account(s):** `[mane]`
  
![Artifacts](Screenshot%20(410).png)

---

## 3. Investigation & Log Analysis

### A. Authentication Log Findings
* Querying SIEM logs for the source IP `[37.19.221.229]` revealed multiple rapid authentication attempts within a short timeframe.
* **Log Status Messages Identified:**
  * `username does not exist`
  * `username is correct but password is wrong`
* **Outcome:** (9) identified attempts resulted in login failure. A successful authentication session was established for the target account after the 9th attempt.
  
  ![SIEM Authentication Logs](Screenshot%20(401).png)

### B. Threat Intelligence Lookup
* **Source IP Reputation:** Checked on VirusTotal / AbuseIPDB.
* **Findings:** The source IP `[37.19.221.229]` is classified as a known malicious scanner actively engaged in credential-harvesting and brute-force campaigns.
  
  ![Threat Intelligence Reputation](Screenshot%20(397).png)
  ![Threat Intelligence Reputation](Screenshot%20(396).png)

### C. Security Risk Identification
* **User Enumeration Vulnerability:** The VPN portal provides distinct error responses for invalid usernames vs. invalid passwords. This permits attackers to perform user enumeration to map active domain accounts prior to password-guessing attempts.
  
![User Enumeration Logs](Screenshot%20(402).png)
![User Enumeration Logs](Screenshot%20(403).png)

---

## 4. Framework Mapping
* **MITRE ATT&CK Tactic:** Credential Access ([TA0006](https://attack.mitre.org/tactics/TA0006/))
* **MITRE ATT&CK Techniques:** 
  * Brute Force ([T1110](https://attack.mitre.org/techniques/T1110/))
  * Password Guessing ([T1110.001](https://attack.mitre.org/techniques/T1110/001/))

---

## 5. Verdict & Remediation Recommendations

* **Final Verdict:** **True Positive** (External Brute-Force & Enumeration Attempt).
  
  ![Verdict](Screenshot%20(416).png)

### Recommended Containment & Preventive Actions:
1. **Firewall Blocking:** Block the malicious source IP address (`[37.19.221.229]`) on the perimeter firewall.
2. **Mitigate User Enumeration:** Reconfigure the VPN authentication portal to return generic error messages (e.g., *"Invalid username or password"*) for all failed login attempts.
3. **Enforce Rate Limiting:** Implement IP-based rate limiting and temporary account lockouts after consecutive failed login attempts.
4. **Forensic Investigation:** Audit all actions performed during the active VPN session to check for lateral movement or data exfiltration.
5. **Mandate MFA:** Ensure Multi-Factor Authentication (MFA) is strictly enforced for all remote-access portals.

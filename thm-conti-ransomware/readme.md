# Ransomware Incident Investigation Report
## TryHackMe – Conti Ransomware (Exchange Compromise)

## Analyst
SOC Analyst Trainee

---

# 1. Incident Overview

Multiple employees reported inability to access Outlook services, and the Exchange Administrator was unable to access the Exchange Admin Center.

Initial triage revealed ransom notes across the Exchange server, indicating a ransomware infection.

The objective of this investigation was to determine how the attacker gained access, escalated privileges, and deployed ransomware.

---

# 2. Impact Evidence

Users experienced service disruption when attempting to access Outlook.

### Evidence

![Outlook Error](screenshots/outlook_error.png)

Ransom notes were found across the system, confirming ransomware activity.

### Evidence

![Ransom Note](screenshots/ransom_note.png)

---

# 3. Initial Access – Web Shell

Investigation revealed that the attacker gained access through a **web shell** deployed on the Exchange server.

Timestamp:
03:52:09 PM

This indicates exploitation of a vulnerable web-facing service, allowing remote command execution.

### Evidence

![Web Shell Activity](screenshots/web_shell.png)

---

# 4. Persistence and Privilege Escalation

After gaining access, the attacker created a new user account.

Timestamp:
04:04:10 PM

The account was added to:
- Administrators group
- Remote Desktop Users group

This step ensured persistent privileged access to the system.

### Evidence

![User Creation](screenshots/user_creation.png)

---

# 5. Malicious Execution

A suspicious executable was identified:

C:\Users\Administrator\Documents\cmd.exe

Timestamp:
04:05:32 PM

This location is abnormal for `cmd.exe`, indicating it is a malicious or renamed binary.

Associated hash:

MD5: 290C7DFB01E50CEA9E19DA81A781AF2C

### Evidence

![Suspicious Executable](screenshots/suspicious_exec.png)

---

# 6. Credential Access

Analysis revealed interaction with:

C:\Windows\System32\lsass.exe

This strongly suggests credential dumping activity, as LSASS stores authentication credentials.

---

# 7. Process Injection / Migration

Suspicious process behavior was observed:

Source:
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

Target:
C:\Windows\System32\wbem\unsecapp.exe

Timestamp:
2021-09-08 12:54:12

This indicates process injection or migration, commonly used to evade detection.

### Evidence

![Process Migration](screenshots/process_migration.png)

---

# 8. Ransomware Deployment

Multiple instances of:

readme.txt

were created across the system.

This file contains the ransomware note.

Note: The file itself is not malicious, but indicates successful ransomware deployment.

### Evidence

![Ransom Note Files](screenshots/readme_files.png)

---

# 9. Attack Timeline

- 03:52:09 PM  
  Initial access obtained via Exchange web shell

- 04:04:10 PM  
  New user account created and added to privileged groups

- 04:05:32 PM  
  Execution of malicious binary (cmd.exe in non-standard location)

- Post-exploitation  
  Credential dumping, process injection, and ransomware deployment

---

# 10. Indicators of Compromise (IOCs)

### Files

C:\Users\Administrator\Documents\cmd.exe  
readme.txt (multiple locations)

---

### Hash

MD5: 290C7DFB01E50CEA9E19DA81A781AF2C

---

### Processes

powershell.exe  
unsecapp.exe  
lsass.exe  

---

# 11. Root Cause

The compromise originated from exploitation of the Exchange server, allowing the attacker to deploy a web shell.

This access was used to create privileged accounts, execute malicious binaries, perform credential harvesting, and deploy ransomware.

---

# 12. Recommendations

- Patch and secure Exchange servers against known vulnerabilities
- Monitor for web shell activity
- Detect execution from non-standard directories
- Monitor account creation and privilege escalation
- Detect access to LSASS
- Monitor PowerShell activity
- Implement endpoint detection and response (EDR)

---

# 13. Conclusion

The Exchange server was compromised through a web shell, followed by privilege escalation and credential harvesting.

The attacker successfully deployed ransomware, resulting in service disruption.

This incident highlights the importance of early detection and proper monitoring of web-facing services and system activity.

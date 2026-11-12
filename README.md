# Microsoft SharePoint ToolShell Attack (2025)

![Harvard CS50](https://img.shields.io/badge/Harvard-CS50-red?style=for-the-badge&logo=harvard)
![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Enabled-blue?style=for-the-badge)
![Microsoft SharePoint](https://img.shields.io/badge/Microsoft_SharePoint-On--Premises-0078D4?style=for-the-badge&logo=microsoftsharepoint)
![CVE](https://img.shields.io/badge/CVE-2025--53770-FF4B4B?style=for-the-badge)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-Mapped-lightgray?style=for-the-badge)

## Overview
This repository contains my Final Project for **Harvard University's CS50 Cybersecurity** course. This project analyzes the July 2025 exploitation of vulnerabilities affecting **on-premises Microsoft SharePoint Server**. 

*Important Distinction:* These vulnerabilities targeted **on-premises** installations of SharePoint Server. **SharePoint Online (Microsoft 365)** was *not* affected by the ToolShell vulnerabilities, as it operates on entirely different managed infrastructure.

## Project Objective
The objective of this project is to analyze a recent, real-world cybersecurity incident and connect it directly to the core concepts covered in the CS50 Cybersecurity curriculum, demonstrating the practical impact of authentication bypasses, insecure deserialization, and perimeter defense failures.

## Incident
- **Incident:** Microsoft SharePoint ToolShell Attacks
- **Date:** July 2025
- **Primary Vulnerability:** CVE-2025-53770 (Authentication Bypass and Remote Code Execution / Deserialization)
- **Related Vulnerability:** CVE-2025-53771 (Path Traversal / Security Bypass)
- **Related Earlier Vulnerabilities:** CVE-2025-49704 and CVE-2025-49706

## Technical Concepts
This analysis covers the following critical cybersecurity concepts:
- **Authentication Bypass & Path Traversal:** Manipulating URL routing to evade security checks.
- **Insecure Deserialization:** Executing malicious code by feeding untrusted serialized data into memory objects.
- **Remote Code Execution (RCE):** Gaining the ability to run arbitrary system commands over the internet.
- **ASP.NET & SharePoint Server:** The underlying framework and application targeted.
- **Web Shells:** Malicious scripts dropped onto web servers for persistent backdoor access.
- **ASP.NET Machine Keys:** Cryptographic keys targeted by attackers to forge administrator session tokens.
- **PowerShell:** Exploited to run commands natively as the system account.
- **AMSI & EDR:** Antimalware Scan Interface and Endpoint Detection and Response tools used for defense.
- **MITRE ATT&CK:** Mapping the attacker behavior to an industry-standard framework.

## Attack Chain
The "ToolShell" attack progressed through the following high-level chain:
1. **Internet-facing SharePoint Server:** The attacker locates an exposed on-premises server.
2. **Security/authentication bypass:** Exploiting CVE-2025-53771 to reach restricted components.
3. **Remote Code Execution:** Exploiting CVE-2025-53770 via a malicious serialized payload.
4. **Web shell:** Dropping persistent backdoors (e.g., `spinstall0.aspx`).
5. **Machine-key theft:** Accessing ASP.NET machine keys to forge admin tokens.
6. **Persistence:** Maintaining long-term access to the compromised server.
7. **Potential further compromise:** Using the server as a beachhead for lateral network movement.

## MITRE ATT&CK
The attacker methodology maps to the following verified techniques:
- **Initial Access:** Exploit Public-Facing Application (`T1190`)
- **Execution:** Command and Scripting Interpreter - PowerShell (`T1059.001`)
- **Persistence:** Server Software Component: Web Shell (`T1505.003`)
- **Credential Access:** Steal Application Access Token / Machine Keys (`T1528`)
- **Defense Evasion:** Indicator Removal on Host / Obfuscation (`T1070`)

## Impact
Because thousands of on-premises servers were directly exposed to the internet, they presented a massive attack surface. 
- **Verified Impact:** The confirmed post-exploitation activity included the installation of persistent web shells, the theft of ASP.NET machine keys, and the execution of PowerShell commands as the SYSTEM account. 
- **Potential Consequences:** Once a web shell is established, the attackers possess a critical beachhead that can potentially be used for lateral movement into the internal corporate network, massive data exfiltration, or the deployment of ransomware.

## Defensive Recommendations
To defend against this and similar threats, organizations should:
- Apply Microsoft security updates immediately.
- Keep SharePoint Server supported and patched.
- Enable and properly configure AMSI (Antimalware Scan Interface).
- Use EDR (Endpoint Detection and Response) tools.
- Monitor SharePoint and IIS worker processes (`w3wp.exe`) for anomalous child processes.
- Monitor PowerShell execution.
- Detect web-shell activity in web directories.
- Rotate ASP.NET machine keys after a compromise to invalidate forged tokens.
- Reduce unnecessary internet exposure (e.g., placing admin interfaces behind VPNs or Reverse Proxies).
- Use robust network segmentation.
- Maintain rigorous vulnerability-management processes.
- Maintain tested incident-response procedures.

## Presentation
**Watch the Presentation**
[https://youtu.be/3DA53YWsZzs](https://youtu.be/3DA53YWsZzs)
*(This video is the final, corrected presentation for my CS50 Cybersecurity project.)*

## Sources
The following authoritative sources were utilized in compiling this analysis:
- **Microsoft Security Response Center (MSRC):** *CVE-2025-53770 and CVE-2025-53771 Security Update Guide.* [msrc.microsoft.com]
- **Microsoft Threat Intelligence:** *Analysis of the ToolShell Campaign.* [microsoft.com/security/blog]
- **Cybersecurity and Infrastructure Security Agency (CISA):** *Known Exploited Vulnerabilities Catalog Update.* [cisa.gov]
- **MITRE Corporation:** *MITRE ATT&CK Framework.* [attack.mitre.org]
- **NIST National Vulnerability Database (NVD):** *Vulnerability Metrics.* [nvd.nist.gov]

## Disclaimer
This repository is an academic cybersecurity analysis prepared as part of Harvard CS50 Cybersecurity. It is intended for educational purposes only.

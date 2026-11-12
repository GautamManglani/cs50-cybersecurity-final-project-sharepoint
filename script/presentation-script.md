# Presentation Script

**[Slide 1: Title Slide]**
Hello everyone. My name is Gautam Manglani. Today, for my CS50 Cybersecurity Final Project, I will be analyzing a major cybersecurity incident that occurred very recently: the Microsoft SharePoint ToolShell Attack of July 2025. This attack centered around two critical vulnerabilities: CVE-2025-53770 and CVE-2025-53771. 
I selected this topic because it is a perfect real-world case study of concepts we have covered in CS50—specifically, the dangers of authentication bypasses, the catastrophic impact of insecure deserialization, and the importance of defending the network perimeter.

**[Slide 2: What is Microsoft SharePoint?]**
To understand the attack, we first need to understand the target. Microsoft SharePoint is a massive enterprise platform used for document management, intranets, and collaboration. 
It is critical to distinguish between the two versions of SharePoint. SharePoint Online is hosted by Microsoft in the cloud. However, this attack specifically targeted **on-premises SharePoint Servers**—meaning servers hosted and maintained by the organizations themselves on their own hardware. 
Organizations frequently expose these on-premises servers to the internet so remote employees can access files. Because these servers sit on the edge of the corporate network and hold highly sensitive data, they are incredibly valuable targets for threat actors.

**[Slide 3: The Vulnerabilities]**
In July 2025, security researchers and Microsoft identified a vulnerability chain dubbed "ToolShell". This chain relied on two specific flaws.
The first, CVE-2025-53771, is a path traversal and security bypass vulnerability. This flaw allowed an attacker to manipulate path routing to evade standard security checks.
The primary vulnerability, CVE-2025-53770, is a critical authentication bypass and remote code execution deserialization flaw. 
When combined, an unauthenticated attacker on the internet could bypass the security boundary, reach a restricted component, and force the server to execute malicious code. This is known as Unauthenticated Remote Code Execution, which is a worst-case scenario in cybersecurity.

**[Slide 4: Timeline]**
The timeline of this incident reveals a complex game of cat-and-mouse. Earlier in 2025, Microsoft had released patches for two related vulnerabilities, CVE-2025-49704 and 49706. 
However, in July 2025, attackers discovered that they could bypass those previous patches by slightly modifying how they routed their traffic using path information. 
This active, in-the-wild exploitation was immediately confirmed. In response, Microsoft released urgent, out-of-band security updates in July 2025 to definitively close the bypass. Shortly after, CISA added CVE-2025-53770 to its Known Exploited Vulnerabilities catalog and provided mitigation guidance, including applying vendor security updates and configuring AMSI.

**[Slide 5: How the ToolShell Attack Worked]**
Let us look at the attack chain step-by-step. 
First, an attacker scans the internet for exposed, on-premises SharePoint servers. 
Second, they send a specially crafted HTTP request using the path traversal security bypass vulnerability to evade standard checks.
Third, they exploit the critical authentication bypass and deserialization vulnerability, sending a malicious payload to a restricted file. 
Fourth, the server blindly deserializes the payload, triggering the Remote Code Execution.
Fifth, the attacker uses this code execution to drop a web shell onto the server—a hidden script that acts as a backdoor—and they steal the server's cryptographic machine keys. 
Finally, they establish persistence, meaning they can come back whenever they want, even if the original vulnerability is patched.

**[Slide 6: Technical Deep Dive]**
To dive a bit deeper technically: SharePoint is built on the ASP.NET framework. 
Deserialization is a normal process where a server takes formatted data—like text—and converts it back into an executable object in memory. The fatal flaw here is insecure deserialization: the server converted the attacker’s data into an object without checking if it was safe, immediately executing the attacker's commands.
Furthermore, the attackers prioritized stealing ASP.NET machine keys. These are cryptographic keys the server uses to sign and encrypt session tokens. By stealing these keys, the attackers could forge their own valid administrator tokens later, entirely bypassing normal login procedures.

**[Slide 7: Verified Impact]**
The verified impact of this campaign was severe. Because thousands of these servers were directly exposed to the internet, they presented a massive attack surface.
During post-exploitation, attackers successfully installed persistent web shells—often disguised as normal SharePoint files, like `spinstall0.aspx`. They stole machine keys and executed arbitrary PowerShell commands as the system account, giving them total control over the local server.
The potential consequences are dire. Once an attacker owns an internet-facing server, they can use it as a beachhead to pivot laterally into the internal corporate network, steal sensitive databases, or deploy ransomware across the organization.

**[Slide 8: MITRE ATT&CK Mapping]**
We can map this behavior precisely using the MITRE ATT&CK framework.
The **Initial Access** was achieved by Exploiting a Public-Facing Application.
For **Execution**, they utilized the Command and Scripting Interpreter, specifically PowerShell.
They maintained **Persistence** by installing a Server Software Component Web Shell.
And they achieved **Credential Access** by stealing Application Access Tokens and Machine Keys.
By using native tools and web shells, the attackers lived off the land, making their activity harder to detect.

**[Slide 9: Why Existing Security Controls Failed]**
Why did standard defenses fail? 
First, the servers were exposed directly to the internet. Traditional firewalls allow web traffic through port 443, so the malicious request sailed right through to the application layer. 
Second, there were patching delays. Vulnerability management takes time, and attackers exploited these flaws before many organizations could apply the July updates.
Third, detecting web shells is incredibly difficult. A web shell is just a text file. If endpoint detection software is not specifically looking for the web server process unexpectedly writing aspx files or launching PowerShell, the intrusion goes unnoticed.

**[Slide 10: Recommendations]**
To defend against this, organizations must take concrete steps based on Microsoft and CISA guidance.
One. **Apply Security Updates:** Immediately install the July 2025 out-of-band updates.
Two. **Rotate Machine Keys:** If a server was compromised, patching is not enough. You must rotate the ASP.NET machine keys to invalidate any forged tokens.
Three. **Deploy EDR and Monitor:** Use Endpoint Detection and Response tools to monitor the SharePoint worker processes for suspicious child processes. Enable Microsoft's Antimalware Scan Interface to catch malicious scripts in memory.
Four. **Restrict Exposure:** Never expose SharePoint admin interfaces directly to the internet. Place them behind a VPN or a properly configured Web Application Firewall.

**[Slide 11: References]**
This analysis was built strictly using verified facts from the Microsoft Security Response Center, CISA advisories, and the MITRE ATT&CK framework regarding the July 2025 incidents. 
Thank you for listening to my presentation. I hope this provided a clear look at the mechanics and impact of the SharePoint ToolShell attack.

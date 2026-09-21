




# Windows Privilege Escalation: Access Token Manipulation

**Privilege escalation via Access Token Manipulation allows an attacker to hijack security credentials to run processes under a higher-privileged user security context.** By exploiting how Windows manages user rights, processes, and threads via tokens, an attacker with local administrator or specialized account access can impersonate local system services or administrative users to gain complete control over the system (**NT AUTHORITY\SYSTEM**). This technique is tracked as **MITRE ATT&CK T1134**.

---

### Core Mechanics: How Windows Tokens Work

Every process and thread in Windows has an **Access Token** that functions like a digital security badge. It contains the security identity (SID) of the user, their privileges, and their group memberships. 

Windows uses two primary types of tokens:
* **Primary (TokenImpersonation = False):** Assigned to a process during creation. It defines the security boundary of the executable lifecycle.
* **Impersonation (TokenImpersonation = True):** Assigned to specific execution threads. This allows a server or a thread to temporarily run in the security context of a client process to perform actions on its behalf.

---

### Primary Attack Vectors

Attackers manipulate tokens through distinct Win32 API abuse paths:

#### 1. Token Impersonation (`DuplicateTokenEx`)
An attacker extracts an existing impersonation token from a high-privilege process and mirrors it into a secondary thread.
* **The Method:** Open a target process $\rightarrow$ Call `OpenProcessToken` $\rightarrow$ Duplicate it via `DuplicateTokenEx` $\rightarrow$ Assign the new token to a thread using `SetThreadToken`.
* **Prerequisites:** Requires the `SeImpersonatePrivilege` (typically held by local administrators, IIS accounts, and network services).

#### 2. Process Creation with Token (`CreateProcessWithTokenW`)
Instead of assigning a token to a thread, an attacker can launch a completely new, independent process carrying a high-privilege token.
* **The Method:** Capture a primary or impersonation token $\rightarrow$ Duplicate it $\rightarrow$ Pass it into `CreateProcessWithTokenW` or `CreateProcessAsUserW` to spawn an executive shell (e.g., `cmd.exe`).
* **Prerequisites:** Requires `SeAssignPrimaryTokenPrivilege` or `SeImpersonatePrivilege`.

#### 3. Token Theft via Handle Hijacking
If an administrative process leaks handles or runs ineffectively, a low-privilege process can occasionally read its access tokens directly out of memory.

---

### High-Privilege Windows Tokens Frequently Targeted

| Token Name | Associated Privileges | Typical Target Impact |
| :--- | :--- | :--- |
| **SeDebugPrivilege** | Debug any process on the operating system. | Allows an attacker to read/write to `lsass.exe` memory or duplicate tokens from any running system process. |
| **SeImpersonatePrivilege** | Impersonate a client after authentication. | Leveraged by common tools (e.g., JuicyPotato, PrintSpoofer) to trick local system processes into connecting to an attacker-controlled pipe. |
| **SeTakeOwnershipPrivilege** | Take ownership of objects (files, registry keys). | Permits an attacker to overwrite system files or executable paths to execute malicious binaries as SYSTEM. |

---

### Detection & Mitigation Strategies

#### Detection Signals (SIEM / EDR)
* **Process Creation Logs (Event ID 4688 / Sysmon Event ID 1):** Monitor for anomalous `ParentCommandLine` properties where a system process spawns unusual binaries (like `cmd.exe` or `powershell.exe`).
* **Privilege Assertions (Event ID 4673 / 4674):** Audit explicit requests to use sensitive privileges like `SeDebugPrivilege` or `SeImpersonatePrivilege`.
* **Handle Tracing:** Track applications making frequent calls to `OpenProcessToken` against core system binaries (e.g., `winlogon.exe`, `lsass.exe`).

#### Hardening & Mitigation
* **Principle of Least Privilege:** Strictly limit which service accounts are granted `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege`.
* **Protected Processes (PPL):** Enable Protected Process Light for critical authentication containers (`lsass.exe`) to prevent token handle harvesting.
* **Credential Guard:** Use virtualization-based security to isolate credential structures so secrets cannot be intercepted even if an attacker attempts token duplication.


# Additional notes


In the Windows operating system, malware steals and manipulates access tokens to elevate privileges (e.g., upgrading to  permissions) or move laterally across a network. Because access tokens dictate the security context and permissions of running processes, compromising them allows an attacker to bypass User Account Control (UAC) and multi-factor authentication (MFA) without triggering traditional alerts. [1, 2]  
Malware typically steals these tokens using two primary categories of attack vectors: Internal OS Token Manipulation (abusing Windows architecture) and External Application Token Theft (stealing session data from apps like web browsers). 
1. Internal OS Token Manipulation (Windows API Abuse) 
If malware has already gained local administrative or highly privileged access, it can interact directly with the Windows kernel and running processes to clone existing permissions. According to the MITRE ATT&CK framework (Technique T1134.001), malware achieves this by calling specific, legitimate Windows APIs: 

* Process Opening (): The malware identifies a highly privileged process (such as  or ) running under the  context and opens a handle to it. 
* Token Extraction (): It requests access to the primary security token attached to that high-privilege process. 
* Token Duplication (): Because it cannot directly use another process's active token, the malware duplicates it to create an identical copy. 
* Impersonation or Execution: 

	* Impersonation: The malware uses  or  to assign the stolen token to its own current thread, instantly inheriting  privileges. 
	* New Process Creation: It uses  to launch an entirely new, hidden command prompt or malicious payload running completely inside the stolen user's context. [5, 6]  

2. External Application Token Theft (Infostealers) 
Modern malware, known as Information Stealers (e.g., RedLine, Lumma, Racoon), often targets application-level tokens rather than internal operating system tokens. These tools extract tokens generated after a user completes a login session: 

* Browser Cookie Database Harvesting: Cloud applications and email clients store session tokens (OAuth tokens) inside the browser’s local SQLite databases. Even though Windows encrypts these databases using DPAPI (Data Protection API), infostealer malware running under the user's logged-in session can quietly request the DPAPI key, decrypt the database, and copy the plaintext active session tokens. 
* Local Security Authority (LSA) Memory Dumping: Tools like Mimikatz or specialized malware look directly inside the memory space of the Local Security Authority Subsystem Service () to find and extract active Entra ID (Azure AD) or Kerberos tickets/tokens generated for enterprise cloud access. 
* Adversary-in-the-Middle (AiTM) Phishing: Rather than attacking the local disk, sophisticated malware components act as proxy servers. They intercept the live authentication traffic between the user and a legitimate service (like Microsoft 365), letting the user log in normally but tealing the final, valid session token out of the network transit. [1, 8, 9]  

Direct Impact Summary 

| Attack Vector | Target Data | Goal | Prevention / Mitigation  |
| --- | --- | --- | --- |
| OS Token Impersonation | Active  or Admin process handles. | Local Privilege Escalation & UAC Bypass. | Run users as standard accounts; monitor API chains like .  |
| Application Token Theft | Web browser cookies, app caches, or cloud session profiles. | Bypassing passwords/MFA to access cloud resources. | Enable Microsoft Entra Token Protection (Device Binding) and LSA Guard.  |

Are you analyzing this to diagnose a security alert in your environment, or are you developing detection rules (such as Sigma or YARA rules) to catch token manipulation attempts? Let me know your specific goal so I can provide the right technical architecture or script examples. 
AI responses may include mistakes.

- [1] https://www.obsidiansecurity.com/blog/token-based-attacks-how-attackers-bypass-mfa
- [2] https://www.mcafee.com/blogs/other-blogs/mcafee-labs/access-token-theft-and-manipulation-attacks-a-door-to-local-privilege-escalation/
- [3] https://attack.mitre.org/techniques/T1134/
- [4] https://medium.com/@itzsanskarr/manipulating-access-tokens-how-adversaries-leverage-windows-security-contexts-2bf7ed38e55f
- [5] https://attack.mitre.org/techniques/T1134/001/
- [6] https://www.manageengine.com/products/eventlog/cyber-security/access-token-manipulation.html
- [7] https://redcanary.com/threat-detection-report/techniques/steal-application-access-token/
- [8] https://itbutler.sa/blog/understanding-token-theft-how-hackers-steal-your-access-how-to-stay-safe/
- [9] https://www.obsidiansecurity.com/blog/the-new-attack-surface-oauth-token-abuse
- [10] https://techcommunity.microsoft.com/blog/microsoft-entra-blog/how-to-break-the-token-theft-cyber-attack-chain/4062700


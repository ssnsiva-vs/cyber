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

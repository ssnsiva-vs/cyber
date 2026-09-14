# Privilege Escalation in Ethical Hacking — Study Notes

## 1. What Is Privilege Escalation?

Privilege escalation is the phase of a penetration test (post-exploitation) where a tester who has gained limited/low-privilege access to a system attempts to obtain higher-level permissions — ultimately root (Linux/Unix) or SYSTEM/Administrator (Windows) — through legitimate misconfigurations, weak controls, or unpatched software, rather than by writing custom exploit code.

It maps to the MITRE ATT&CK **Privilege Escalation (TA0004)** tactic and is a core stage in frameworks like the Penetration Testing Execution Standard (PTES) and OSCP-style methodology: Recon → Initial Access → **Privilege Escalation** → Lateral Movement → Persistence → Reporting.

## 2. Two Broad Categories

| Type | Description | Example Goal |
|---|---|---|
| **Vertical** | Low-privilege user → higher-privilege account | Standard user → root/SYSTEM |
| **Horizontal** | Same privilege level, different account/scope | User A's data → access User B's account/resources |

## 3. General Methodology

1. **Enumeration** — gather as much information as possible about the OS, installed software, running services, scheduled tasks, file permissions, network config, and user context.
2. **Identify misconfigurations or vulnerabilities** — compare enumerated data against known weak patterns (weak permissions, outdated software, exposed credentials).
3. **Select an escalation vector** — choose the most reliable, lowest-risk path.
4. **Validate in a lab/authorized scope** — confirm access without causing instability (a key ethical/professional consideration).
5. **Document evidence** — screenshots, commands run, and impact, for the client report.
6. **Recommend remediation** — this is the actual deliverable value of the engagement.

## 4. Common Linux Vectors

- **Kernel exploits** — unpatched kernel vulnerabilities (should be flagged, not weaponized in a report without client consent).
- **SUID/SGID binaries** — misconfigured setuid programs that can be abused to spawn a privileged shell (`find / -perm -4000` is the standard enumeration command).
- **Weak file permissions** — world-writable `/etc/passwd`, `/etc/shadow`, cron jobs, or scripts run by root but writable by other users.
- **Sudo misconfiguration** — `sudo -l` reveals commands a user can run as root; certain binaries (documented on sites like GTFOBins) can be abused if allowed via sudo.
- **Cron job abuse** — scheduled tasks running as root that call scripts writable by lower-privileged users.
- **Path hijacking** — exploiting an insecure `$PATH` variable in scripts executed by privileged users.
- **Credential harvesting** — reused passwords in config files, bash history, or environment variables.
- **Container/VM escape** — misconfigured Docker sockets or privileged containers.

## 5. Common Windows Vectors

- **Unquoted service paths** — a service binary path with spaces and no quotes can be hijacked by planting an executable earlier in the path.
- **Weak service permissions** — services with modifiable binaries or configurations that run as SYSTEM.
- **AlwaysInstallElevated** — a misconfigured registry policy that allows any user to install MSI packages with SYSTEM rights.
- **Insecure scheduled tasks** — tasks executable/writable by non-admin users but running as SYSTEM/Admin.
- **Token impersonation** — abusing privileges like `SeImpersonatePrivilege` (e.g., techniques historically grouped under "Potato" style attacks).
- **DLL hijacking** — placing a malicious DLL where an application will load it with elevated rights due to search-order weaknesses.
- **Stored/cached credentials** — unattended install files, PowerShell history, registry-stored passwords.
- **UAC bypass techniques** — abusing auto-elevating binaries or registry keys.

## 6. Key Enumeration Tools (Recognize, Don't Just Run Blindly)

| Tool | Platform | Purpose |
|---|---|---|
| LinPEAS | Linux | Automated enumeration of common escalation vectors |
| WinPEAS | Windows | Automated enumeration of common escalation vectors |
| LinEnum | Linux | Bash enumeration script |
| Seatbelt | Windows | C# host enumeration |
| PowerUp / PowerSploit | Windows | PowerShell privilege-escalation checks |
| GTFOBins | Linux | Reference site for abusable Unix binaries |
| LOLBAS | Windows | Reference site for "living off the land" binaries |
| pspy | Linux | Monitor processes without root, to catch cron/root activity |

These tools **surface misconfigurations**; understanding *why* something is exploitable matters more than memorizing tool output.

## 7. Defensive / Blue-Team Countermeasures

- Apply the **principle of least privilege** — users and services should run with the minimum rights needed.
- Patch management — keep OS/kernel and software up to date to close known exploit paths.
- Regularly audit **SUID/SGID binaries**, **sudoers file**, and **scheduled tasks/services** for unsafe configurations.
- Use **quoted service paths** and restrict write access to service binaries/directories.
- Enforce **strong, unique credentials**; avoid storing plaintext passwords in scripts or config files.
- Enable **logging and monitoring** for anomalous privilege changes (e.g., SIEM alerts on `sudo` usage, new admin accounts).
- Apply **AppLocker/WDAC** (Windows) or **SELinux/AppArmor** (Linux) to restrict what can execute and with what rights.
- Conduct regular **configuration reviews** and **penetration tests** to catch drift before attackers do.

## 8. Ethical & Legal Guardrails

- Only perform privilege escalation testing **within an authorized scope** (signed rules of engagement / contract).
- Avoid actions that could cause **denial of service** or **data loss** on production systems unless explicitly permitted.
- Maintain **detailed logs** of every command and technique used, for reporting and legal accountability.
- Report findings responsibly, prioritizing **remediation guidance** over technique showcasing.

## 9. Suggested Further Study

- MITRE ATT&CK — Privilege Escalation tactic (TA0004)
- OWASP Testing Guide
- OSCP / PEN-200 course material (official, hands-on labs)
- GTFOBins.github.io and LOLBAS-project.github.io (reference only, use ethically)

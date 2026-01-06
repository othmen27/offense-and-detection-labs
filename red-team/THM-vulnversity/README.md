## Reconnaissance
- nmap shows a squid http proxy port which is open 
- Gobuster dir scan showed /internal which is interesting
- Internal page let's you upload something
- Internal page has /uploads/ dir 
### Initial access

- Noticed the internal page doesn't allosw you to upload regular .php files
- Used buripsuite and a wordlist with different php extensions to upload a reverse shell payload
- prepared netcat to listen on port 4444 then accessed the uploads and the reverse shell payload

### Privillege escalation

- the command **find / -type f -perm -4000 2>/dev/null** shows /bin/systemctl
- used the website **https://gtfobins.github.io/** to exploit this vulnerability
- created a root.service reverse shell on attacker machine and sent it with an http server
- ran the service using the vulnerable systemctl while netcat is listening to that port
- Got root access
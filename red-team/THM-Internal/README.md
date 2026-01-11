### Personal notes
since this is a pentesting roleplay i'll document everything i find interesting i'll put everything that i think is worth mentioning below
### Reconnaissance
- Nmap shows port 22 and port 80 open perhaps a vulnerable webpage?
- after enumarating the directories with gobuster i found /wordpress endpoint which is prob worth investigating
- since it's a wordpress page i'm gonna use wpscan to scan it further more
- found /blog/wp-login.php endpoint
- tried username admin and it confirmed that the user exists with this error
- i'll try bruteforcing using this command ```wpscan --url http://MACHINEIP --passwords /usr/share/wordlists/rockyou.txt --usernames admin```
- after i let the bruteforce working for a while the password is "my2boys" 
### Initial access
- now that i'm inside the admin dashboard i'll prepare a php reverse shell by pentestmonkey and put it in the themeEditor then access it
- i changed the index.php to my php reverse shell and quickly accessed the mainpage and boom i've got a shell
### Privillege escalation
- first started looking for anything interesting and i found wp-save.txt inside of /opt
- these look like login credentials(aubreanna:bubb13guM!@#123) i'll try using them in ssh.. Boom they worked now let's enemurate even further
- inside the the /home/aubreanna i found "jenkins.txt" file which is interesting "Internal Jenkins service is running on 172.17.0.2:8080"
- ill setup a port forwarding for that exact port and see
- I'm in the jenkins login page i'll try different usernames and see if i can find something
- i tried default combos like admin:admin but none worked i'll try bruteforcing with burpsuite's intruder sniper attack i'm gonna be using rockyou.txt wordlist
- i was able to find that the password is "spongebob" because it's request had the only unique length number
- now that i'm inside of jenkins dashboard i'll try and get a reverse shell with groovy scripting
- i'll be using this script "https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy"
- now that i've got a shell i'll start looking around for interesting stuff
- i found a file called "note.txt" inside /opt "root:tr0ub13guM!@#123" it looks like i'm done

### Honerable mention
- main page is the apache default web page it's never a good idea to document that
- when i did the wpscan it showed this version of Wordpress "5.4.2" which is vulnerable

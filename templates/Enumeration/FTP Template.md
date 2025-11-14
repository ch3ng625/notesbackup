Template Version: 2025.09.06
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Anonymous Access:
###### Commands:
- `ftp <IP> [-P <PORT>]` Credentials: `anon:anon`; `anonymous:anonymous`


---
### Password Attacks:
###### Brute force:
- `hydra -l <user> -P /usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt ftp://<IP>`
###### Default creds:
- `hydra -C /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt ftp://<IP>`
- `paste -d ':' users.txt users.txt > creds.txt`
###### Findings:

---
### Recursive File Download:
###### Commands:
- `wget -r ftp://<user>:<pass>@<IP>:<PORT>`


---
- [ ] Exploit search
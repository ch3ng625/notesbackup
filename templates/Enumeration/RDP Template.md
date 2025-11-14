Template Version: 2025.10.24
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Password Attacks:
###### Commands:
- `hydra -l <user> -P <wordlist> rdp://<IP>` (brute user)
- `hydra -C <wordlist> rdp://<IP>` (brute credential pair)
- `hydra -L <wordlist> -p "<pass>" rdp://<IP>` (password spray)
###### Wordlist:
- `/usr/share/wordlists/rockyou.txt`
- `/usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt`
- `paste -d ':' users.txt users.txt > creds.txt`
- `/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt`
- `/usr/share/wordlists/dirb/others/names.txt`
###### Checklist:
- [ ] `user:user`
- [ ] `default-passwords.txt` (per user)
- [ ] `rockyou.txt` (per user)
- [ ] Password spray (per password found)
###### Findings:

---
### Access Check:
###### Commands:
- **XFreeRDP:** `xfreerdp3 /dynamic-resolution /u:<user> /p:<pass> [/pth:<NTLM>] [/tls:seclevel:0] [/sec:tls|rdp] /v:<IP>`
- **RDesktop Interactive:** `rdesktop <target>`
###### Results:

| User | Have Access? |
| ---- | ------------ |
|      |              |







---
- [ ] Exploit search
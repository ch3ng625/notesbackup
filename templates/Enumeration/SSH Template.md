Template Version: 2025.10.24
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Password Attacks:
###### Commands:
- `hydra -l <user> -P <wordlist> [-s <PORT>] ssh://<IP>` (brute user)
- `hydra -C <wordlist> [-s <PORT>] ssh://<IP>` (brute credential pair)
- `hydra -L <wordlist> -p "<pass>" [-s <PORT>] ssh://<IP>` (password spray)
###### Wordlist:
- `/usr/share/wordlists/rockyou.txt`
- `/usr/share/seclists/Passwords/Default-Credentials/default-passwords.txt`
- `/usr/share/seclists/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt`
- `paste -d ':' users.txt users.txt > creds.txt`
- `/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt`
- `/usr/share/wordlists/dirb/others/names.txt`
###### Checklist:
- [ ] `user:user`
- [ ] `ssh-betterdefaultpasslist.txt`
- [ ] `default-passwords.txt` (per user)
- [ ] `rockyou.txt` (per user)
- [ ] Password spray (per found password)
###### Findings:

---
### Access Check:
###### Commands:
- `ssh <user>@<host> [-p <port>] [-i <key>]`
###### Results:

| User | Have Access? |
| ---- | ------------ |
|      |              |



---
- [ ] Exploit search
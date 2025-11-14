Template Version: 2025.09.08
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Brute Force:
###### Commands:
- `nmap --script imap-brute -p 143 <IP>`
- `hydra -L users.txt -P password.txt imap://<IP>`

---
### Service Interaction:
###### References:
- https://hackviser.com/tactics/pentesting/services/imap
- https://blog.1nf1n1ty.team/hacktricks/network-services-pentesting/pentesting-imap
###### Connection:
- `nc -nv <IP> 143`
- `telnet <IP> 143`
- `openssl s_client -connect <IP>:993 -quiet`
###### Login:
- `A1 LOGIN "<user>" "<pass>"`
###### List Folders / Mailboxes:
- `A1 LIST "" *`
- `A1 LIST INBOX *`
- `A1 LIST "Archive" *`

---
- [ ] Exploit search
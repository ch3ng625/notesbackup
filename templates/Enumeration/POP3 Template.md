Template Version: 2025.09.08
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Brute Force:
###### Commands:
- `nmap -p 110 --script pop3-brute --script-args userdb=users.txt,passdb=pass.txt <IP>`
- `hydra -L users.txt -P password.txt -f <IP> pop3`

---
### Service Interaction:
###### Connection:
- `nc -nv <IP> 110`
- `telnet <IP> 110`
- `openssl s_client -connect <IP>:995 -crlf -quiet`
###### Commands:
- `USER <user>`
- `PASS <password>`
- `CAPA` - show capabilities
- `STAT` - list number of messages, total mailbox size
- `LIST` - list messages and sizes
- `RETR 1` - show message 1
- `TOP msg n` - Show first n lines of message msg
- `DELE 1` - mark 1 for deletion
- `RSET` - undo changes
- `QUIT` - logout





---
- [ ] Exploit search
Template Version: 2025.09.08
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Targeted Nmap Scan:
###### Commands:
- `sudo nmap -p 25 --script=smtp* <IP>`

---
### Service Interaction:
###### References:
- https://mailtrap.io/blog/smtp-commands-and-responses/
###### Connection:
- Linux: `nc -nv <IP> 25`; `telnet <domain> 25`
- Windows: `.\telnet.exe <IP> 25`
###### Initialize:
- `HELO <domain>`; `EHLO <domain>` (extended)
###### Username/email enumeration:
- `VRFY <user/email>` (550 - does not exist; 252 will accept delivery, cannot confirm)
- `MAIL FROM:<fake>; RCPT TO:<user>` (250 - exists; 550 does not exist)
- Kali: `smtp-user-enum -M <mode> -U users.txt -t <host.domain.tld>`
	- Mode: `VRFY`; `EXPN`; `RCPT`; `ALL`
###### Mailing list membership:
- `EXPN <mail_list>`
###### Send email:
- `swaks -t <rcpt> --from <sender> [--attach @<file>] --server <IP> --body <body> --header "Subject: <subject>" [-ap]`

---
### Email Attachment Attacks:
- See *File Attacks*.

---
### SMTP Open Relay:
###### POC:
```
HELO test.com
MAIL FROM: attacker@test.com
RCPT TO: victim@anothertest.com
DATA
Subject: test
Hello
.
QUIT
```




---
- [ ] Exploit search
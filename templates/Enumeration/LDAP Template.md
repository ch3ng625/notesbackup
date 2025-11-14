Template Version: 2025.03.25
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Naming Context:
###### Commands:
- `ldapsearch -H ldap://<IP> -x -s base namingcontexts`

---
### Anonymous Access:
###### Commands:
- `ldapsearch -x -H ldap://<IP> -D '' -w '' -b '<DN>'`

---
### Root Dump:
###### Commands:
- `ldapsearch -H ldap://<IP> -x -s base -b '' * +`

---
### Base Dump:
###### Commands:
- `ldapsearch -H ldap://<IP> -x -s base -b '<DN>' * +`

---
### Domain Dump:
###### Commands:
- Ref: https://book.hacktricks.xyz/network-services-pentesting/pentesting-ldap#valid-credentials 
- `ldapdomaindump <IP> -u '<domain>\<user>' -p '<pwd>'`

---
### User Dump:
###### Commands:
- `ldapsearch -x -H ldap://<IP> -D '<user>@<domain.tld>' -b '<DN>' 'objectClass=user' | grep sAMAccountName`

---
### Computer Dump:
###### Commands:
- `ldapsearch -x -H ldap://<IP> -D '<user>@<domain.tld>' -b '<DN>' 'objectClass=computer'`

---
- [ ] Exploit search
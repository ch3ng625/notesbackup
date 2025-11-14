Last update: 2025.11.14
## Brute Force:
### Custom Wordlist Generation:
###### Crunch:
- `crunch <min> <max> -t <pattern> > <wordlist>`
- `crunch 6 6 -t Lab%%% > wordlist.txt`
### HTTP POST Form:
- `hydra -l <user> -P <wordlist> http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"`
- `hydra -l <user> -P <wordlist> http-post-form "<endpoint>:<POST data>:<fail string>"`
	- POST data: password to test = `^PASS^`; user to test = `^USER^`

---
## Cracking:
### Hash Conversion:
###### KeePass:
- `keepass2john <file.kdbx> > keepass.hash`
### Rule-Based:
###### Hashcat:
- https://hashcat.net/wiki/doku.php?id=rule_based_attack
- `hashcat -m <mode> "<hash>" <wordlist> -r <rule> [--force]`
- `/usr/share/hashcat/rules/best64.rule`
###### John:
- `john --wordlist=<wordlist> --rules=<rulename> hash.txt`
`/etc/john/john.conf`:
```bash
[List.Rules:rulename]
# c = capitalization of first letter; $ = append character
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#
```
### SSH:
###### Hash conversion:
- `ssh2john id_rsa > ssh.hash`
###### Cracking:
- `john --wordlist=<wordlist> ssh.hash`
### NTLM:
- `hashcat -m 1000 "<NTLM>" <wordlist>`
### Net-NTLMv2
- `hashcat -m 5600 "<hash>" <wordlist>`
### KeePass:
###### Hash conversion:
- `keepass2john <file.kdbx> > keepass.hash`
###### Cracking:
- `hashcat -m 13400 keepass.hash <wordlist>` (remove "`Database:`" string)
- `john --wordlist=<wordlist> keepass.hash`

---
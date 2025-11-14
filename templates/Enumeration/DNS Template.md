Template version: 2025.11.13
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Forward Lookup:
###### Commands:
- `host -t <a/aaaa/ns/mx/ptr/soa/txt/any> <domain>`
- (Windows): `nslookup [-type=<TYPE>] <domain> [<DNS_IP>]`

---
### Reverse Lookup:
###### Commands:
- `for ip in $(seq 200 254); do host xx.xx.xx.$ip; done | grep -v "not found"`

---
### Subdomain Enumeration:
###### Commands:
- `for sub in $(cat list.txt); do host $sub.<domain>; done`
- `dnsrecon -d <domain> -D <wordlist> -t brt`
- `gobuster dns -d <domain> -w <wordlist> -t 10`

---
### General Recon:
###### Commands:
- `dnsrecon -d <domain> -t std`
- `dnsenum <domain>`

---
### Dig:
###### Any record:
- `dig any <domain.tld> @<IP>`

###### Zone transfer (TCP):
- `dig axfr <domain.tld> @<IP>`




---
- [ ] Exploit search
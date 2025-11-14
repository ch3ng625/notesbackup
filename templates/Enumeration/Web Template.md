Template version: 2025.09.05
### Versions:
Source: Nmap, Wappalyzer
- [ ] 
##### Exploit Search:

##### Findings:

---
## Targeted Nmap Scan:
`sudo nmap -p 80/443 --script=http-enum <IP>`

---
## Subdomain/VHost Scan:
`gobuster vhost -u <URL> -w <namelist.txt/bitquark> -t 100 --append-domain`
`ffuf -w <namelist.txt/bitquark> -u <URL> -H "Host: FUZZ.<domain>" -fc (status) -fs (size) -fw (words)`
###### Terminal Output:
```

```
###### Subdomains:
- [ ] 

---
## Directory Bust:
###### Directory:
`gobuster dir -u <URL> -w <wordlist> -t 100 -x <ext>`
`feroxbuster -u <URL> -w <wordlist> -x <ext>`
###### API:
`gobuster dir -u <URL> -w <wordlist> -p <pattern.txt> -t 100`
pattern.txt:
```
{GOBUSTER}/v1
{GOBUSTER}/v2
```
###### Wordlists:
`/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt`
`/usr/share/seclists/Discovery/Web-Content/raft-large-files.txt`
#### Base: `/`; Extensions: `raft`:

#### Endpoints:
- [ ] 

---
## Main domain:








---
- [ ] Exploit search
- [ ] Directory/Parameter Fuzzing
- [ ] Debugger / Source
- [ ] Response headers / Sitemap / Robots
- [ ] NIKTO Enumeration
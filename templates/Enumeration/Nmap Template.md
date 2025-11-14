Template Version: 2025.11.04
### Machine Lookup (Windows):
`nmblookup -A <IP>` (Linux); `nbtstat -A <IP>` (Windows)

---
### TCP all ports:
###### Commands:
- `sudo nmap --min-rate 1000 -p- <IP>`
- `.\rustscan.exe -r 1-65535 -a <IP>`
- Static binary: https://github.com/opsec-infosec/nmap-static-binaries
###### Terminal Output:
```

```
###### Open Ports:

- [ ] Slow scan

---
### UDP all ports:
###### Commands:
- `sudo nmap --min-rate 1000 -sU -p- --reason <IP>`
###### Terminal Output:
```

```
###### Open Ports:

- [ ] Top 100
- [ ] Slow scan

---
### Aggressive TCP:
###### Commands:
- `sudo nmap -A -p <PORTS> <IP>`
###### Terminal Output:
```

```
###### Details:


---
### Aggressive UDP:
###### Commands:
- `sudo nmap -A -p <PORTS> -sU <IP>`
###### Terminal Output:
```

```
###### Details:


---
### OS Fingerprinting:
###### Commands:
- `sudo nmap -O --osscan-guess <IP>`
###### Terminal Output:
```

```
###### Details:


---
### Vulnerability Scan:
###### Commands:
- `sudo nmap -sV -p <PORTS> --script "vuln" <IP>`
###### Terminal Output:
```

```
###### Details:


---
### Network Sweeping:
###### Commands:
- `sudo nmap -sn xx.xx.xx.1-254`
- `for ip in {1..254}; do ping -c 1 -W 1 xx.xx.xx.$ip | grep "64 bytes" | cut -d " " -f 4; done`
- `for i in $(seq 1 254); do nc -zv -w 1 xx.xx.xx.$ip 445; done`
- `1..254 | % { $ip="xx.xx.xx.$_"; if (Test-Connection -Count 1 -Quiet -TimeoutSeconds 1 $ip) { $ip } }`

---
### Windows Port Scan:
###### Commands:
- `Test-NetConnection -Port <PORT> <IP>`
- `1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("<IP>", $_)) "TCP port $_ is open"} 2>$null`

---
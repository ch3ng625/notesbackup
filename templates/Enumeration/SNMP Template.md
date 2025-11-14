Template Version: 2025.08.23
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Community String:
###### Commands:
- `onesixtyone -c /seclists/Discovery/SNMP/<file> <IP>`

---
### SNMPWalk Enumeration:
###### Commands:
- `snmpwalk -c <CS> -v<1/2c/3> [-t 10] <IP> <MIB>`
###### MIBs:
- Entire MIB tree: `leave empty`
- Windows users: `1.3.6.1.4.1.77.1.2.25`
- Running processes: `1.3.6.1.2.1.25.4.2.1.2`
- Installed software: `1.3.6.1.2.1.25.6.3.1.2`
- TCP listening ports: `1.3.6.1.2.1.6.13.1.3`

---
- [ ] Exploit search
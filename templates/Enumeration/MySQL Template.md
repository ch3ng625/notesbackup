Template Version: 2025.09.17
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Authenticated Enumeration:
###### Connection:
- `mysql -u <user> -p'<pass>' -h <host> -P <port> [-D <db> -e <cmd> --skip-ssl-verify-server-cert]`
###### Commands:
- Version check: `select version();`
- Check current user: `select system_user();`
- Show databases: `show databases;` (output depends on user access)
- Select: `select user, authentication_string from mysql.user where user = '<user>';`

---
- [ ] Exploit search
